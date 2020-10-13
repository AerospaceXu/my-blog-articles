# TS 自学笔记（二）装饰器

本文写于 2020 年 9 月 15 日

上一篇 TS 文章已经是很久之前了。这次来讲一下 TS 的装饰器。

对于前端而言，装饰器是一个陌生的概念，但是对于 Java、C# 等语言来说装饰器这一概念并不陌生。

所谓装饰器，就是一种特殊的**类型声明**，它可以被附加到「属性」、「类声明」、「方法」、「方法参数」上。

他的好处就是可以**编写元信息以内省代码**。看不懂？没关系，继续往下看。

_我知道，网上的很多文章都看的让人十分痛苦，但我觉得我的文章并不会这样，我会尽量由浅入深的_

## 装饰器模式

在说 TS 和 JS 的装饰器之前，我想先说说设计模式的一种——装饰器模式。

装饰器模式允许像一个现有的对象添加新的功能，但是又不改变其结构，是作为对现有类的一个 Wrapper（一个包裹）。

因为设计模式是在面向对象编程中发展出来的，而在 OOP 的代码中，我们理应遵循「多用组合，少用继承」的原则，所以装饰器模式通过动态的为对象添加额外的职责会比继承生成子类更为灵活。

接下来我会像 Angular 文档一样，说一个 Hero 的例子。

### Hero 父类

首先我们需要一个 Hero 类：

```js
class Hero {
  constructor() {}

  attack() {}
}
```

### 创建英雄

接着我们创建具体的英雄，我们就叫他 Hasaki 吧。

```js
class Hasaki extends Hero {
  // 这里重写了 Hasaki 的攻击方法
  attack() {
    console.log('ha-sa-ki');
  }
}
```

### 英雄变身

我们设定英雄 Hasaki 拥有两个形态，并且每个形态拥有不同的技能与伤害。该怎么写代码？

难道写两个子类继承自 Hasaki 类？当然不

我们可以用依赖注入的思想：

```js
class RedHasaki {
  constructor(hasaki) {
    this.hasaki = hasaki;
  }

  attack() {
    console.log('red!!! ha-sa-ki!!!');
  }
}
```

```js
class BlueHasaki {
  constructor(hasaki) {
    this.hasaki = hasaki;
  }

  attack() {
    console.log('blue!!! ha-sa-ki!!!');
  }
}
```

这样一来，我们在 Hasaki 变身之后只需要这么写就可以了：

```js
const redHasaki = new RedHaski(hasaki);
```

## 函数运行时间检测器

接下来我们再来写一个东西，来看看 Functional Programming 中的装饰器咋用。

先来制作一个时间检测器吧，用来检测函数的执行时间。

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

## 装饰器的用法

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

最后，总结一下：

1. 装饰器是对类、函数、属性之类的一种装饰，可以针对其添加一些额外的行为；
2. 通俗的来说，就是在原有代码外层包装了一层处理逻辑；
3. 装饰器是一种解决方案，而并非是狭义的 `@xxxx`；
4. 装饰器通常可以用于：类型判断、对返回值的排序、过滤，或者对函数添加节流、防抖等其他的功能性代码，各种各样的与函数逻辑本身无关的、重复性的代码。

PS：core-decorators 是一个封装了常用装饰器的 JS 库，它归纳了很多常用装饰器，可以参考一下，例如：

- `autobind`：自动绑定 `this`，告别箭头函数和 bind；
- `readonly`：将类属性设置为只读；
- `override`：检查子类的方法是否正确覆盖了父类的同名方法；
- `debounce`：防抖函数；
- `throttle`：节流函数；
- `enumerable`：让一个类方法变得可枚举；
- `nonenumerable`：让一个类属性变得不可枚举；
- `time`：打印函数执行耗时。

（完）
