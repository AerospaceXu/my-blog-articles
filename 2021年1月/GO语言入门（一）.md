# GO 语言入门（一）

本文写于 2020 年 1 月 18 日

Go 由 Google 工程师 Robert Griesemer，Rob Pike 和 Ken Thompson 设计的一门编程语言，第一个版本于 2012 年 3 月作为开源发布。

它是一种**静态类型**的**并发型编译语言**，并具有**垃圾回收**功能。

Go 语言支持**交叉编译**，即可在 Windows 上编译 Linux 版本、Mac 上编译 Windows 版本。

Go 的语法接近 C 语言。与 C++相比，Go 并**不包括如枚举、异常处理、继承、泛型、断言、虚函数等功能**，但增加了**切片(Slice) 型、并发、管道、垃圾回收功能、接口等特性**的语言级支持。

## 安装与配置

首先于官网下载对应系统的 Go，并配置环境变量。

环境变量的配置主要有两点：`GOPATH` 与 `GOROOT`。

`GOROOT` 是 Go 的路径。而 Go 中的**工作空间**由环境变量「GOPATH」定义。Go 希望你写的任何代码都将写在此工作区内。

在新版本中，GOPATH 可以有多个，加上 GO111MODULE 的加入，让初学者超级摸不着头脑。

因此我们可以先不管 GO111MODULE 和多个 GOPATH 一说，只需要建立一个 GOPATH 用作学习即可。

```bash
export PATH="PATH-TO-YOUR-GO/bin:$PATH"
export GOPATH="自定义一个文件夹，文件夹下新建 src bin pkg 三个文件夹"
export PATH="$GOPATH/bin:$PATH"
```

## 模块化

Go 的模块化声明类似于 Java，都是以 `package xxx` 声明的。

项目必须拥有一个**入口文件**，声明 `package` 为 `main`：

```go
// main.go
package main
```

与 Java 的万物皆对象类似，我们需要引入一些包实现具体的功能。例如控制台输出：

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

我们可以通过两种方式运行 Go 命令，run 与 build。

`go run main.go` 可以直接运行 Go 程序。

但 Go 是一种编译语言，所以我们首先需要在**执行之前编译**它。

上面这种方法并非没有编译，只是帮我们省略了编译而已。

`go build main.go` 会帮我们编译代码，创建一个二进制可执行文件 `main`——在 Windows 上创建的应该是 `main.exe`。我们运行该可执行文件，即可运行 Go 程序。

## 变量声明

Go 是一种静态类型的语言，这意味着在声明变量时我们就需要明确变量的类型。

### var 声明

一般一个变量的定义如下:

```go
var a int
```

这样我们就定义好了一个 int 类型的变量 a。默认 a 会被赋值成 0。

### 自动类型推断

使用以下语法可以初始化改变变量的值:

```go
var a = 1
```

这里我们没有制定变量 a 的类型，但是在我们给它初始化为 1 时，它就自动被定义成了 int 类型的变量。

### := 操作

或者我们认为 `var` 过于麻烦，就可以用 `:=` 来进行更简单的定义与赋值。

```go
message := "hello world"
```

### 声明多个变量

声明多个变量：

```go
var (
  a int
  b string
  c = "hello"
)
```

## 常量

常量与变量的声明方法类似，除了声明方法为 `const`。

```go
const pi = 3.14
```

与变量的区别在于，**常量一旦声明就无法改变**。

（完）
