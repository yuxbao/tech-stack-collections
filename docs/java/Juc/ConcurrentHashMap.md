---
title: ConcurrentHashMap
createTime: 2026/02/25 14:44:35
permalink: /java/jdkjls49/
tags:
  - java
  - juc
---

ConcurrentHashMap 是 Java 中一个线程安全的哈希表实现，提供了高效的并发访问和修改操作。它是 Java Collections Framework 中的一部分，位于 java.util.concurrent 包中。

### 锁

JDK7使用**分段锁**，Map分为多个segments，每个segment都可以独立加锁，不同线程可以同时操作不同锁，从而实现并发。

map->segment->[]hashEntry(k,v)，在segment层加锁，每个segement 都extends `ReentrantLock`可重入锁。

JDK8开始，使用**桶锁**，这是更细粒度的锁，结合`CAS+synchronized`代码块控制实现并发，以更大程度减少锁的
竞争。

[]Node链表可以转换为[]TreeBin红黑树，同时对每个链表和树加锁：

- 对于读操作，使用`volatile变量`保证内存可见性。
- 对于写操作，优先使用`CAS`尝试插入，成功直接返回;否则使用**synchronized**代码块进行加锁处理。

### 可见性

Node节点中，value和next都是`volatile`的，保证更新会在多线程内被看到。

```java
static final class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V value;
    volatile Node<K,V> next;
}
```

get无锁保证可见性，put使用cas无锁更新，失败后使用synchronized加锁更新。

### computeIfAbsent & computeIfPresent

```java
V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)
```

key是一个K类型的键，mappingFunction是Functional Interface，接受K返回V。

这个方法作用是检查ConcurrentHashMap中是否包含key：

1. 如果key存在，方法直接返回对应的值。
2. 如果key不存在，方法会使用提供的mappingFunction来计算值，然后将（key，value）存放入哈希表，返回这个值。

这个方法是**线程安全的**，具有原子性，常用于缓存计算、初始化操作等场景。

类似的还有**computeIfPresent**，但后者是在key存在时才会执行mappingFunction，常用于缓存更新、多线程增量操作。
