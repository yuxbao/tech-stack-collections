---
title: error
createTime: 2025/12/20 00:16:48
permalink: /golang/xohzrwm8/
---

本文对golang的`error`进行一个简单学习。

## 前言

Go的错误处理一直以来为人诟病，但是这也是其设计哲学的一部分。

> if err != nil

error属于正常的程序执行流异常，而panic才是会让程序崩溃的异常。所以error的出现是可以被接受的，大多数情况应该显式进行处理。

让我们查看error的源代码：

```go
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
	Error() string
}
```

可以看出，error也是一个接口类型，其中需要实现`Error()`函数并返回一个字符串，表示一个错误信息。

## 创建错误

创建一个error变量可以有多种方法：

1. 使用`errors`包的`New`函数
```go
errors.New("this is an error")
```
2. 使用`fmt`包的`Errorf`函数
```go
fmt.Errorf("this is %d formatted error", 1)
```

error的常见使用场景都是自定义全局常量使用，比如：

```go
var (
  ErrInvalid = fs.ErrInvalid // "invalid argument"

  ErrPermission = fs.ErrPermission // "permission denied"
  ErrExist      = fs.ErrExist      // "file already exists"
  ErrNotExist   = fs.ErrNotExist   // "file does not exist"
  ErrClosed     = fs.ErrClosed     // "file already closed"

  ErrNoDeadline       = errNoDeadline()       // "file type does not support deadline"
  ErrDeadlineExceeded = errDeadlineExceeded() // "i/o timeout"
)
```

## 自定义error

只需要实现`Error`方法就可以。

```go
type MyError struct {
	Message string
}

func (e MyError) Error() string {
	return e.Message
}
```

## 错误链

调用的函数内部出现了error，可以选择将error作为返回值的一部分返回（函数==可以多返回值==），传递给上层调用函数处理，类似于java中的函数声明的抛出异常。

比如查看errors.Errorf()源码，发现其中：
```go
func Errorf(format string, a ...any) error {
    ...
    w := &wrapError{msg: s}
```

会自动检测它内部是不是一个error，如果是的话那就`wrapError`也就再包裹一层异常。

通过`errors.Unwrap()`可以解包异常。

类似的，通过`errors.Is()`判断其中错误链是否包含指定错误。此外，通过`errors.As(err error, target any)`找到异常链中第一个出现的指定异常并赋值。

:::go-repl
```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	err := errors.New("this is an error")
	err = fmt.Errorf("heelo %w", err)
	var target error
	as := errors.As(err, &target)
	fmt.Println(as)
	fmt.Println(target)
	err = errors.Unwrap(err)
	fmt.Println(err)

	if err != nil {
		fmt.Println(err)
	}
}

```
:::

## 小结

总体来说，golang的error机制需要显示处理，跟java不太一样，没有try-catch，但其调用链条可以自定义查找。注意不要滥用panic，尽可能使用error。使用函数间返回值传递和包装、判断、解包机制是一个好的范例。