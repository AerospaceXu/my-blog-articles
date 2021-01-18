# GO 语言入门

本文写于 2020 年 1 月 18 日

Go 由 Google 工程师 Robert Griesemer，Rob Pike 和 Ken Thompson 设计的一门编程语言。它是一种**静态类型**的 **并发型编译语言**，并具有**垃圾回收**功能。第一个版本于 2012 年 3 月作为开源发布。

Go 语言支持**交叉编译**，即可在 Windows 上编译 Linux 版本、Mac 上编译 Windows 版本。

Go 的语法接近 C 语言。与 C++相比，Go 并**不包括如枚举、异常处理、继承、泛型、断言、虚函数等功能**，但增加了**切片(Slice) 型、并发、管道、垃圾回收功能、接口等特性**的语言级支持。

Go 2.0 版本将支持泛型，但对于断言的存在，则持负面态度，同时也为自己不提供类型继承来辩护。

不同于 Java，Go 原生提供了哈希表。

## 安装与配置

首先于官网下载对应系统的 Go，并配置环境变量。

环境变量的配置主要有两点：`GOPATH` 与 `GOROOT`。

`GOROOT` 是 Go 的路径。

Go 中的**工作空间**由环境变量「GOPATH」定义。Go 希望你写的任何代码都将写在此工作区内。

## 模块化

Go 的模块化声明类似于 Java。

Go 项目必须拥有一个**入口文件**，声明为 `main`：

```go
// main.go
package main
```

与 Java 的万物皆对象类似，我们需要引入一些包实现具体的功能。

例如打印：

```go
// main.go
package main

import "fmt"

func main(){
  fmt.Println("Hello World!")
}
```

`fmt` 是 Go 中的内置包，它实现了格式化 I/O 的功能。

如果需要引入多个包时，可以多次书写 `import`，或者是分行写：

```go
import "a"
import "b"
import "c"
// 或者是
import (
  "a"
  "b"
  "c"
)
```

`func` 是 go 的函数生命语句，`main` 则是主函数，是 go 运行时第一个执行的函数。

在函数外，go 只能执行少量的语句，例如声明变量、声明 package、import 包……

## 运行 go

我们可以通过两种方式运行 Go 命令。

`go run main.go` 可以直接运行 Go 程序。

但 Go 是一种编译语言，所以我们首先需要在执行之前编译它。上面这种方法只是帮我们省略了编译而已。

`go build main.go` 会创建一个二进制可执行文件 main，在 Windows 上创建的应该是 `main.exe`。我们运行该可执行文件，即可运行 Go 程序。

## 变量

Go 是一种静态类型的语言，这意味着在声明变量时我们就需要明确变量的类型。

一般一个变量的定义如下:

```go
var a int
```

这样我们就定义好了一个 int 类型的变量 a。默认 a 会被赋值成 0。

使用以下语法可以初始化改变变量的值:

```go
var a = 1
```

这里我们没有制定变量 a 的类型，但是在我们给它初始化为 1 时，它就自动被定义成了 int 类型的变量。

或者我们认为 `var` 过于麻烦，就可以用 `:=` 来进行更简单的定义与赋值。

```go
message := "hello world"
```

声明多个同类型变量：

```go
var (
  a int
  b string
  c = "hello"
)
```

## 常量

常量与变量类似，除了声明方法为 `const`。

```go
const pi = 3.14
```

与变量的区别在于，常量一旦声明就无法改变。

## 数据结构与变量类型

跟任何其他编程语言一样，Go 语言支持各种不同的数据结构。

### 基础数据类型

先看看以下几种：`Number`, `String`, `Boolean`。

一些受支持的 number 存储类型是：`int`, `int8`, `int16`, `int32`, `int64`,
`uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr` ……

string 类型存储一些列的字节。它用关键字 `string` 表示和声明。

boolean 使用关键字 `bool` 存储布尔值。

Go 还支持复数类型数据类型，可以使用 complex64 和 complex128 声明。

```go
var a bool = true
var b int = 1
var c string = "hello world"
var d float32 = 1.222
var x complex128 = cmplx.Sqrt(  -5 + 12i)
```

### 数组、切片、Maps

Go 拥有数组，切片，以及 Maps 等原生数据结构。

#### 数组

```go
var arr [5]int
var multiArr [2][3]int
```

当数组的值在运行时，**长度不能进行更改**，并且数组也不提供获取子数组的能力。

#### 切片

为此，Go 有一个名为切片的数据类型。切片存储一系列元素，可以随时扩展。切片声明类似于数组声明——没有定义容量：

```go
var noLength []int
```

这将创建一个零容量和零长度的切片。

切片也可以定义容量和长度，使用以下语法：

```go
// 切片的初始长度为 5，容量为 10。
numbers := make([]int,5,10)
```

**切片是数组的抽象。**切片使用数组作为底层结构。切片包含三个组件：容量、长度和指向底层数组的指针.

通过使用 `append` 或 `copy` 函数可以增加切片的容量。

`append` 函数可以为数组的末尾增加值，并在需要时增加容量。

```go
numbers = append(numbers, 1, 2, 3, 4)
```

（完）
