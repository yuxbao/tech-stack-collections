---
title: Golang 笔记
createTime: 2025/12/15 22:30:00
permalink: /golang/
next: /golang/2qzk1z31/
tags:
    - Golang
---

这里是 Golang 学习笔记的汇总。

## 概述::material-icon-theme:go-gopher::

<ImageCard image="/golang/go.webp" width="8rem" center="true"/>

::material-icon-theme:go-gopher::Go（又称 Golang）是一门**现代化、编译型、并发友好**的编程语言，由==Google 于 2009 年推出==。Go 的设计目标非常明确：  
> **用更简单的方式，解决高并发、大规模系统的工程问题。**

Go 以简洁的语法、极高的工程效率和优秀的并发模型，广泛应用于**云原生、微服务、基础设施、分布式系统**等领域。


## 核心特性

### ::catppuccin:go:: 编译速度快
Go 是编译型语言，但==编译速度极快==，几乎接近脚本语言的开发体验，适合快速迭代与持续集成。

### ::catppuccin:go:: 原生并发模型
Go 内置 **goroutine + channel** 并发模型，使并发编程变得直观、安全且高效。

```go
go func() {
    fmt.Println("Hello from goroutine")
}()
```
### ::catppuccin:go:: 简洁的语法

Go 刻意减少语法复杂度：
- 无继承
- 简洁泛型（1.18+）
- 无隐式类型转换

强调 可读性、可维护性、团队协作效率。

### ::catppuccin:go:: 强类型 + 自动内存管理

Go 是强类型语言，拥有==自动垃圾回收（GC）==，在保证性能的同时，极大降低了内存管理成本。


## 基本示例

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

## 应用场景
- ☁️ 云原生与容器（Docker、Kubernetes）
- ⚙️ 微服务与高并发后端
- 🔧 基础设施与中间件
- 🌐 API 服务（REST / gRPC）
- 🧩 分布式系统与高性能工具链

## 生态与工具链

### 常用框架
- Gin / Fiber / Echo（Web 框架）
- gRPC
- GORM / sqlx

### 构建与依赖管理
- Go Modules（原生支持）

### 开发工具
- Go 官方工具链（go build / go test / go fmt）
- VS Code / GoLand