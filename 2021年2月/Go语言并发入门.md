# Go 语言并发入门

本文写于 2021 年 2 月 1 日

因为我之前并没有怎么接触过并发编程，C 和 Python 都只是做了一点学术用途，真正用于生产环境的 JS 又没有并发可言，所以这次来细细的学习一下 Golang 的并发。

## 1 简单介绍 Go 的并发

Golang 在并发编程上有两大利器，分别是 channel 和 goroutine。所以我们先来聊聊这俩兄弟。

### 1.1 goroutine

Go 语言生来就是容易实现并发的，我们只需要通过 `go` 关键字来开启 `goroutine` （协程）即可。

同一个程序中的所有 `goroutine` 共享同一个地址空间。

`goroutine` 是**轻量级线程**，其调度是由 Golang 运行时进行管理的。

goroutine 语法格式：

```go
go foo(...params)
```

比如一个很简单的例子：

```go
package main

import (
  "fmt"
  "time"
)

func say(s string) {
  for i := 0; i < 5; i++ {
    time.Sleep(100 * time.Millisecond)
    fmt.Println(s)
  }
}

func main() {
  go say("world")
  say("hello")
}
```

这样一条程序就会是由主线程和 `goroutine` 同时执行的程序。

但是如果我们尝试做一个小小的修改呢？比如，删掉这一句 `Sleep`。

我们惊讶的发现，一旦删除了 `Sleep`，整个的就会出现问题：次数不对。

如果我们的函数是输出 10 次 xxx：

```go
func foo() {
  for i := 0; i < 100; i++ {
    fmt.Println("xxx")
  }
}

func main() {
  go foo()
  foo()
}
```

那么我们并发执行的时候就会发现**次数不对**，他居然只执行了十次。

可我们的并发执行不应该是 20 次吗？就算不并发也应该是 20 次呀。

但当我们将次数增加到 100 次的时候，又会发现他确实是执行了一百多次。

这是为什么呢？

**因为 go 程序会在主线程结束之后结束，届时就算 goroutine 没有结束，他也得被干掉了。**

### 1.2 channel

“使用通信来共享内存，而不是通过共享内存来通信。”是 Golang 领域的一句名言。

这句话有两层意思，Go 语言确实在 `sync` package 中提供了传统的「锁机制」，**但更推荐使用 `channel` 来解决并发问题**。

简单来说，channel 是一种 go 协程用以接收或发送消息的**安全的消息队列**，它就像是两个协程之间的导管，用于同步各种资源。

（完）
