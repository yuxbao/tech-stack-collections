---
title: threadLocal
createTime: 2026/01/01 22:51:48
permalink: /java/vxwu3loz/
tags:
  - java
  - juc
---

java 并发编程是后端学习很重要的一环，理解多线程和并发程序执行时可能会产生的 data consistency 的 wetting，以及 race conditions==，是很重要的。

首先，需要了解 juc 包下的并发工具类，其中**threadLocal** 是必须掌握的。

## 概述

> 作为程序员要知道，任何一个编程语言写的一行代码，可能没有你想象的这么简单。比如对于 int 类型数据 a，执行 a++;操作时，背后被操作系统拆解为了取值、加 1、赋值三个步骤，因此在多线程环境下就是不安全的。

如上所述，所以我们需要一些方法论实现线程安全，比如加锁（synchronized 或者 ReentrantLock）、或者使用一些设计模式，比如**单例模式**等。

threadLocal 就是其中一种方式，利用线程内置的变量来隔离多线程访问冲突。
好处有：

- 传递数据
- 线程隔离

ThreadLocal 可⽤于跨⽅法、跨类时传递上下⽂数据，不需要在⽅法间传递参数。

## 使用

创建：

```java
// 创建一个ThreadLocal实例，用于保存用户登录信息
            private static ThreadLocal<User> currentUser = ThreadLocal.withInitial(() -> null);
```

使用：get()和 set(), remove()方法

## 实现

jdk8 之前与 jdk8 中实现不同，查看源码：

```java
public class ThreadLocal<T> {
        static class ThreadLocalMap {
        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }

        private Entry[] table;
        private int size = 0;
        private int threshold;  // Default to 0
    }
}
```

静态内部类`ThreadLocalMap`，用来存储每个线程的 ThreadLocal 实例和对应的值。
其类似一个哈希表，其中`Entry[]`数组维护 K-V 键值对：

- jdk8 之前：ThreadLocalMap 存储在 Thread 对象中，ThreadLocalMap 的 key 是 ThreadLocal 对象，value 是对应的值。
- jdk8 之后：ThreadLocalMap 存储在 Thread 对象中，ThreadLocalMap 的 key 是 ThreadLocal 对象的**弱引用**，value 是对应的值。

<ImageCard image="/java/threadLocal.png" center="true"/>

这样存储好处是从 ThreadLocal 维护变到 Thread 维护，减少了内存开销，因为 Thread 销毁的时候也会销毁对应的 ThreadLocalMap。

Entry 继承了 `WeakReference`，它限定了 key 是⼀个弱引⽤，弱引⽤的好处是当内存不⾜时，JVM 会回收
ThreadLocal 对象，并且将其对应的 Entry.value 设置为 null，这样可以在很⼤程度上避免内存泄漏。

threadLoca 对象的几乎所有方法，都调用了 threadLocaMap 的相应方法来完成实际的存取操作。

### ThreadLocalMap

<ImageCard image="/java/threadLocal_uml.png" center="true"/>

ThreadLocalMap 的 Key 是 弱引⽤，但 Value 是强引⽤。

如果⼀个线程⼀直在运⾏，并且 value ⼀直指向某个强引⽤对象，那么这个对象就不会被回收，从⽽导致==内存泄
漏==。

除非及时调用 `remove()` ⽅法，会将当前线程的 ThreadLocalMap 中的所有 key 为 null 的 Entry 全部清除，这样就能避免内存泄漏问题。

#### WeakReference

key 是弱引⽤， `new WeakReference(new ThreadLocal())` 是弱引⽤对象，当 JVM 进⾏垃圾回收时，只要发现
了弱引⽤对象，就会将其回收。

```java
WeakReference key = new WeakReference(new ThreadLocal());
```

⼀旦 key 被回收，ThreadLocalMap 在进⾏ set、get 的时候就会对 key 为 null 的 Entry 进⾏清理。

### 解决哈希冲突

### 扩容机制

### 后续