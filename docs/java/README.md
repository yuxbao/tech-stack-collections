---
title: Java学习笔记
createTime: 2025/12/15 22:22:32
permalink: /java/
next: /java/2pmp16wy/
tags:
    - java
---

这里是 Java 学习笔记的汇总。

## 概述

<ImageCard image="/java/java.png" width="8rem" center="true"/>

Java 是一门**面向对象、跨平台、强类型**的通用编程语言，由 ==Sun Microsystems 于 1995 年发布，现由 Oracle 维护==。凭借其稳定性、安全性和生态成熟度，Java 长期被广泛应用于**企业级系统、后端服务、分布式系统、大数据平台**等领域。

Java 的核心理念是：

> **Write Once, Run Anywhere（一次编写，到处运行）**

这使得 Java 程序可以在不同操作系统和硬件平台上保持一致的运行行为。


## 核心特性

### ::catppuccin:java:: 跨平台
Java 源代码会被编译为 **字节码（Bytecode）**，运行在 Java 虚拟机（JVM）之上。只要平台支持 JVM，Java 程序即可运行。

### ::catppuccin:java:: 面向对象
Java 以 **类（Class）和对象（Object）** 为核心，支持封装、继承和多态，便于构建结构清晰、可维护性强的大型系统。

### ::catppuccin:java:: 强类型 & 安全性
Java 具有严格的类型检查机制，并在 JVM 层面提供：
- 自动内存管理（垃圾回收）
- 数组越界检查
- 空指针检查（运行时）

有效减少了底层错误和安全隐患。

### ::catppuccin:java:: 丰富的标准库
Java 提供了大量成熟的标准库，涵盖：
- 集合框架（Collection）
- 并发编程（Concurrency）
- 网络通信（Networking）
- IO / NIO
- 反射（Reflection）

---

## 基本示例

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

## Java 应用场景
- 🧩 企业级后端开发（Spring / Spring Boot）
- 🌐 Web 服务与微服务架构
- ⚙️ 分布式系统与中间件
- 📊 大数据生态（Hadoop / Flink）
- 🏦 金融、电商、政企系统

## 生态与工具链

### 主流框架
- Spring / Spring Boot / Spring Cloud
- MyBatis / Hibernate
- Netty

### 构建与依赖管理
- Maven
- Gradle

### 开发工具
- IntelliJ IDEA

