---
title: channel入门
createTime: 2025/12/15 23:38:35
permalink: /golang/2qzk1z31/
tags:
    - golang
    - channel
    - 并发
---

在Go语言中，channel是一种==管道==，是**多线程间进行数据通信**的方式。

### 初识管道

```go
package main

import "fmt"

func main() {
	ch := make(chan int)
	go func() {
		select {
			case v := <-ch:
			fmt.Println(v)
		}
	}()

	ch <- 1
}
```

上述代码中：
1. 主函数`main()`中通过`make`关键字创建一个`int`类型的管道，然后赋值给ch变量。make关键字类似于java中的new
2. 下面的==匿名函数==创建了一个`go`协程，通过`select`关键字==多路复用==接受ch管道传递来的数据，然后调用`fmt`包下的公有函数`Println()`打印。
3. 然后，通过`<-`运算符将整形数字1放入管道，注意，这里创建了==无容量的管道==，如果是有容量超出会==阻塞==。
4. 程序会开启一个新协程，这个协程阻塞等待`select`语句执行到这个case，也就是这个管道有数据才会停止阻塞。在`ch<-1`的时候就有数据了。
