---
title: 初识struct
createTime: 2025/12/16 00:26:12
permalink: /golang/lw46nqhj/
---

在 Go 语言中，`struct` 是一种非常重要的==复合数据类型==，用于将多个不同类型的数据组合成一个实体。它相当于其他语言中的类或对象，但 Go ==不直接支持类和继承==，`struct` 是构建对象的基础。

#### 定义 Struct

`struct` 是由字段（field）组成的，每个字段都有名字和类型。你可以将它看作是一个记录，存储一组相关的信息。

#### 示例代码

```go
package main

import "fmt"

// 定义一个结构体类型
type Person struct {
    Name string
    Age  int
}

func main() {
    // 创建一个结构体实例
    person := Person{Name: "John", Age: 30}

    // 访问结构体字段
    fmt.Println("Name:", person.Name) // 输出: Name: John
    fmt.Println("Age:", person.Age)   // 输出: Age: 30
}

```

### 代码解析
1.  Person 是一个结构体类型，包含两个字段：Name 和 Age，分别表示一个人的名字和年龄。
2. 在 main 函数中，使用 Person 类型创建了一个结构体实例 person，并通过点操作符访问其字段。
3. fmt.Println 用于打印结构体的字段内容。

### 总结
1. struct 用于组合一组相关的字段，可以存储不同类型的数据。
2. 在 Go 中，struct 是面向对象编程中的核心，虽然 Go 没有类和继承，但可以通过 struct 和方法==实现面向对象的设计==。
3. 结构体实例通过点操作符访问字段。
