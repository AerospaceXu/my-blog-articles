# Go 语言并发入门

本文写于 2021 年 2 月 1 日

因为我之前并没有怎么接触过并发编程，C 和 Python 都只是做了一点学术用途，真正用于生产环境的 JS 又没有并发可言，所以这次来细细的学习一下 Golang 的并发。

- [Go 语言并发入门](#go-语言并发入门)
  - [简单介绍 Go 的并发](#简单介绍-go-的并发)
  - [Go 开启协程](#go-开启协程)
  - [使用 Channel 同步状态](#使用-channel-同步状态)
    - [无缓冲的 Channel](#无缓冲的-channel)
    - [有缓冲的 Channel](#有缓冲的-channel)
    - [关闭 Channel](#关闭-channel)
    - [select](#select)
  - [锁机制](#锁机制)

## 简单介绍 Go 的并发

Go 语言生来就是容易实现并发的，我们只需要通过 `go` 关键字来开启 `goroutine` 即可。

同一个程序中的所有 `goroutine` 共享同一个地址空间。

`goroutine` 是**轻量级线程，叫做协程**，其调度是由 Golang 运行时进行管理的。也就是说两个协程可能是两个线程，也可能是一个线程。

这通常也称为 M:N 线程模型——因为我们有 M 个应用线程（ 协程 ）运行在 N 个操作系统线程上（在现代的硬件上，有可能拥有数百万个协程）。但是为什么呢？为什么不用线程用协程呢？

因为虽然线程保证了操作系统级别的并发需求，但是**线程切换毕竟是有成本的**。打个比方，假设你的 CPU 使用率 100%，那可能 40% 的性能都用去切换线程了，浪费至极。

因此 Go 创造了一个协程调度器，在一个线程上自己调度多个协程，从而降低多线程的切换成本。

## Go 开启协程

Go 语言只需要用一个 `go` 关键字即可开启一个协程：

```go
go foo(...params)
```

以下是一个很简单的例子：

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

这样一条程序就会是由主线程和 `goroutine` 同时执行的程序。他会在一条 `say` 的时间里，输出两条 `say` 的内容。

接下来我们尝试做一个小小的修改：删掉 `Sleep`。

再运行程序。

我们惊讶的发现，一旦删除了 `Sleep`，整个程序就会出现问题：**输出的次数不对**。

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

那么我们并发执行的时候就会发现**次数不对**，他居然只执行了十次！

可我们的并发执行不应该是 20 次吗？就算不并发也应该是 20 次呀。

更奇怪的是当我们将次数增加到 100 次的时候，又会发现他确实是执行了 100+ 次数，但是明显不足 200。

这是为什么呢？

**因为 go 程序会在主线程结束之后结束，届时就算 goroutine 没有结束，他也得被干掉了。**

**主进程在退出前不会等待全部协程执行完毕。**

## 使用 Channel 同步状态

在上面一个例子中，我们知道一旦主线程结束，程序就会结束，协程尚未完成也不可能继续执行。

但是 Go 中提供了一种叫做 Channel 的变量，可以用于协程之间的交流与同步。

```go
func say() {
  for i := 0; i < 10; i++ {
    fmt.Println("你好")
  }
}

func main() {
  go say()
  fmt.Println("main 结束")
}
```

如果我们的程序是这样，那么最后的输出一般只会有一句：main 结束。

但如果我们这么写：

```go
func say(ch chan int) {
	for i := 0; i < 10; i++ {
		fmt.Println("你好")
	}
	ch <- 100
}

func main() {
	ch := make(chan int)
	go say(ch)
	res := <-ch
	fmt.Println(res)
	fmt.Println("main 结束")
}
```

在 `say` 函数的参数中加入一个 channel 类型的变量，然后在 main 中声明后传入。

运行程序我们发现。go 为我们先输出了 10 次“你好”，然后输出了 100，最后输出了 “main 结束”。

这是因为 channel 如果在两个协程之间传输了数据，**那么协程之间会自己阻塞，以保证用正确顺序传递 channel 数据**。

### 无缓冲的 Channel

上面的例子就是无缓冲的 Channel。

**这就好像是接力一样，不管是谁先到接力点，都必须完成接力棒的交接才能够离开。**不能说你看上一棒的选手尚未到场，就火急火燎的自己先走了。

### 有缓冲的 Channel

有缓冲的 Channel 也可以通过 `make` 来创建，但是需要带上缓冲区大小：`ch := make(chan int, 3)`。

缓冲区就像是一个队列，上一棒的选手不断的送接力棒到队列里。**和无缓冲的区别是：上一棒不需要管下一棒是否接到了。**

下一棒会自己在缓冲区中取出上一棒放入的接力棒。

但如果说，此时缓冲区的长度只有 3，我们可以尝试放入 4 个数据吗？

是可以的。但是需要等待前面缓冲区的值被取出至少一个，能够空出下一次传入的空间才可以。

在这种时候，传入放就会被阻塞，不能再像之前一样，放到缓冲区就可以离开了。

另一种情况是拿取方拿取数据时发现缓冲区是空的，那么此时，也会阻塞该协程。

### 关闭 Channel

close 方法可以关闭一个 Channel，被关闭的 Channel 无法再传递数据。

```go
data, ok := <- ch

if !ok {
  fmt.Println("读取失败")
}
```

可以用以上代码判断是否读取成功。

```go
func changeChannel(ch chan int) {
	for i := 0; i < 3; i++ {
	  ch <- i
	}
  close(ch)
}

func main() {
	ch := make(chan int, 3)
	go say(ch)
  // 这里运用了 range 关键字处理 channel，注意：如果使用 range 关键字，必须使用 close 方法关闭 channel，不然会报错
	for data := range ch {
		fmt.Println(data)
	}
	fmt.Println("main 结束")
}
```

### select

`select` 可以帮助我们处理多个 Channel。

```go
select {
case <- ch1:
  // doSomething()
case ch2 <- 666:
  // doSomething()
default:
  // doSomething()
}
```

如果从 ch1 读取数据成功，就进入第一个逻辑；如果没有触发，整个 select 都会阻塞；如果触发失败，则会进入 `ch2 <- 666` 那一行的逻辑判断。

如果都没有触发，就会执行默认逻辑。

select 很像 switch。一般情况下，我们会用一个无限的 for 循环套住他：

```go
for {
	select {
	case currentItem := <-ch:
		fmt.Printf("a: %d\n", a)
		a += currentItem
	case <-quit:
		fmt.Println("I quit")
		fmt.Printf("a: %d\n", a)
		break
	default:
		fmt.Println("default")
	}
}
```

## 锁机制

我们想像一个场景：有两个协程在同时读写一个数字，分别对它进行了 `+1` 和 `-1` 操作。

在操作的时候，我们会先去读取这个值，然后进行加减，最后把新值扔回去。

这就引发了一个问题：如果两个协程同时读取了最初始的数据，那么他们计算出的值就会分别是 `2` 和 `0`。那么谁后把值扔回去，该数字就会是哪个值。

这就需要一个锁的概念。

每当一个值**被一个协程所读取，就会上锁**，接下来不管谁读取都会遇到这个锁而无法读取。当操作完毕后，再解开这个锁即可。

编写并发代码要求特别注意在何时何处读取和写入一个值。

（完）
