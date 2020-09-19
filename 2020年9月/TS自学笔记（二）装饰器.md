# TS 自学笔记（二）装饰器

本文写于 2020 年 9 月 15 日

上一篇 TS 文章已经是很久之前了。这次来讲一下 TS 的装饰器。

对于前端而言，装饰器是一个陌生的概念，但是对于 Java、C# 等语言来说装饰器这一概念并不陌生。

所谓装饰器，就是一种特殊的**类型声明**，它可以被附加到「属性」、「类声明」、「方法」、「方法参数」上。

他的好处就是可以**编写元信息以内省代码**。看不懂？没关系，继续往下看。

_我知道，网上的很多文章都看的让人十分痛苦，但我觉得我的文章并不会这样，我会尽量由浅入深的_

## 函数运行消耗时间检测器

我们先来制作一个时间检测器，用来检测函数的执行时间。

```TypeScript
const startTime = +new Date();
doSomeThing();
const endTime = +new Date();
console.log(endTime - startTime);
```

当我们中间函数不是异步函数时，就可以通过 `endTime` 和 `startTime` 的差值计算出该函数操作消耗的时间。

当然了，这是最简陋的做法，我们应该去封装一下，来个高级点的做法，比如使用一个函数：

```TypeScript
const timeRecorder = (fn) => {
  const startTime = +new Date();
  fn();
  const endTime = +new Date();
  console.log(endTime - startTime);
}

timeRecorder(doSomething);
```

这样虽然封装了，可我们不满足，因为这样一来我们根本没有自己来调用 `doSomething` 呀！

**或者，我们可以更高级一点**，让函数 `return` 一个函数吧：

```TypeScript
const timeRecorder = (fn) => {
  return () => {
    const startTime = +new Date();
    fn();
    const endTime = +new Date();
    console.log(endTime - startTime);
  }
}

const doSomething = timeRecorder(foo);
doSomething();
```

这个时候我们其实就可以叫 `timerRecorder` 的这种写法为装饰器模式了。

## JS 的装饰器写法

我们上面这种写法虽然好，但是这么好的写法语言当然要自己自带一下啦！

TS 装饰器使用 `@expression` 这样的形式：

```TypeScript
function timerRecorder(fn: Function) {
  ...
}

@timerRecorder
function doSomething() {
  ......
}
```

这就等价于我们上一段代码。

**组合装饰器**

装饰器还可以组合使用：

```TypeScript
@foo
@bar
function doSomething() {
......
}
```

这个效果就相当于我们先使用 bar 装饰器，然后将返回的函数传入 foo 装饰器。

**装饰器工厂**

我们偶尔会看到有些装饰器后面会带上小括号，就像这样：`@foo()`。

我们知道，装饰器的**内部是一个函数**，那么我们完全可以使用一个工厂函数去创建装饰器，就像这样：

```TypeScript
function decoratorFactory() {
  return function(fn: Function) {
    // 这里是装饰器
  }
}

@decoratorFactory()
```

这样我们的 `@decoratorFactory()` 就成为了一个装饰器。

**那我们为什么需要装饰器工厂呢？**

因为我们的装饰器可能会接受参数。写过 Angular 的同学应该知道，通常他的写法都是：

```TypeScript
@NgModule({
  imports: [...],
  ...
})
```

如果是单纯的装饰器，我们是没办法传参数的，因此我们通过工厂函数+闭包来解决这一问题。

```TypeScript
function decoratorFactory(param1: any, param2: any) {
  return function(fn: Function) {
    doSomething(param1, param2);
    fn()
  }
}
```

另外，写在类上的装饰器，装饰器**会作用，且仅会作用于**构造函数 `constructor`。

```TypeScript
@foo
class Phone() {
  constructor() {

  }
  ......
}
```

也就是说，类装饰器可以用来「监视」、「修改」或者替换类的构造。

**`@expression` 求值后必须是一个函数，他会在运行时被调用，装饰器的声明信息作为「参数」被传入。**

最后，总结一下：装饰器就是在不修改函数代码的情况下往函数中添加某些操作，例如打印日志、计时。

（完）
