# this 去哪？

本文写于 2020 年 4 月 26 日

```javascript
let obj = {
  foo() {
    console.log(this)
  }
}
let bar = obj.foo
obj.foo() // 打印出的 this 是 obj
bar() // 打印出的 this 是 window
```

最后两行函数的值为什么不一样？？？

this 关键字是 JavaScript 最复杂的机制之一，它是一个**特别的关键字**。

之前关于函数的文章里写过了，`let bar = obj.foo`可以让`bar()`和`obj.foo()`等价，那为什么 this 指向不一样？

在学 React 的时候，很多人会发现有个很烦人的操作，就是在`constructor`里面，需要`bind(this)`。

**首先需要从函数的调用开始讲起。**

```javascript
foo(p1, p2)
obj.foo(p1, p2)
foo.call(obj, p1, p2)
/*
 *  或者apply或者bind，这三个都是用来绑定this的
 */
```

这几种其实都是调用函数。那有什么区别呢？

乍看之下，前两种简单，后面一种复杂。很多人就选择完全不使用后面的`call()`。

但实际上，后面的方法是用来绑定 this 的，前面的方法属于“随缘绑定”，但都可以等价的变成该种写法：

```javascript
func.call(context, p1, p2)
```

`foo(p1, p2)` 等价于 `foo.call(undefined, p1, p2)`
`obj.foo(p1, p2)` 等价于 `obj.foo.call(obj, p1, p2)`

**我们来实现一个简单的 bind**

```javascript
function bind(fn, obj) {
  return function () {
    return fn.apply(obj, arguments)
  }
}

bin(foo, obj)
```

很多人特别喜欢死记硬背这个 this 的指向，“谁调用 this 就指向谁”blablabla……

但实际上，this 是要为我们所用的，我想让你指向谁，你就要指向谁。我们不应该被它牵着走。

先看`foo(p1, p2)`中的 this 如何确定。

当你写下面代码时：

```javascript
function foo() {
  console.log(this)
}
foo()
```

等价于

```javascript
function foo() {
  console.log(this)
}
foo.call(undefined) // 可以简写为 foo.call()
```

按理说打印出来的 this 应该就是 undefined 了吧，但是浏览器里有一条规则：

**如果你传的第一个参数是`null`或者`undefined`， window 对象就是默认的**。因此上面的打印结果是 window。

如果希望这里的 this 不是 window 该怎么做呢？

很简单：

```javascript
func.call(obj) // 那么里面的 this 就是 obj 对象了
```

再看`obj.foo(p1, p2)` 里的 this 如何确定：

```javascript
let obj = {
  foo() {
    console.log(this)
  }
}
obj.foo()
```

他本身的指向就是 obj 自己，所以写成`call()`也就是：`obj.foo.call(obj)`

回到最上面的代码。

```javascript
let obj = {
  foo() {
    console.log(this)
  }
}
let bar = obj.foo
obj.foo() // 转换为 obj.foo.call(obj)，this 就是 obj
bar()
// 转换为 bar.call()
// 由于没有传 context
// 所以 this 就是 undefined
// 最后浏览器给你一个默认的 this —— window 对象
```

提一个有趣的用法：如果把函数放到数组里，然后数组[]调用函数，this 指向哪里呢？

```javascript
function fn() {
  console.log(this)
}
let arr = [fn, fn2]
arr[0]()
```

这里面的 this 是什么呢？

我们可以把`arr[0]()`理解为`arr.0()`，虽然后者的语法错了，但是形式与转换代码里的`obj.foo(p1, p2)`对应上了！

`arr[0]()` => `arr.0()` => `arr.0.call(arr)`

那么里面的 this 就是 `arr` 了。

总之，this 就是你 call 一个函数时，传入的第一个参数。

（完）
