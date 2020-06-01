<!--
 * @Author: Aero Xu
 * @Date: 2020-05-23 21:18:29
 * @LastEditors: Aero Xu
 * @LastEditTime: 2020-05-23 21:58:13
-->

# `prototype` 与`__proto__`

本文写于 2020 年 5 月 23 日

首先看一个问题：`let obj = { name: '孙悟空' }`的原型是谁？

是`obj.__proto__`。

`let arr = [1, 2, 3]`的原型是谁？

是`arr.__proto__`。

`Object`的原型谁是？

是`Object.__proto__`。

**他们都不是`prototype`，只是正好`__proto__`等价于某个函数的`prototype`。**

1. 对象的`__proto__`属性，就是其构造函数的`prototype`；
2. 任何函数的`__proto__`都是`Function`的`prototype`——包括`Object()`函数、`Array()`函数、`Function()`函数等等；
3. `Object.prototype`是所有对象的直接或者间接原型。

这里有个问题，如果说，`Function()`函数是造世的函数，那为什么说`Object.prototype`是所有对象的直接或者间接原型呢？

其实很简单，因为`Function()`只是构造出了`Object.prototype`（指针），但是并没有构造出——`Object.prototype`所指向的真正对象。

---

**让我们从头开始一下：**

当我们的 JS 代码**还没运行**的时候，JS 环境里已经有了一个 `window` 对象。

1. `window` 对象有一个 `Object` 属性，`window.Object` 是一个函数；
2. `window.Object` 这个函数有一个重要属性是 `prototype`；
3. `window.Object.prototype` 里面有这么几个属性 `toString`（函数）、`valueOf`（函数）。

然后我们写一点代码

```javascript
var obj = {}
obj.toString()
```

这句代码做了啥？

为什么 `obj` 有 `toString()` 属性？我们分明没有给她一个`toString()`方法呀！

**在 JS 中，这句话大概是让 `obj` 变量指向一个空对象，这个空对象有个 `__proto__` 属性指向 `window.Object.prototype`。**

这样你在调用 `obj.toString()` 的时候，`obj` 本身没有 `toString`，就会去 `obj.__proto__` 上面去找 `toString`！

所以调用 `obj.toString` 的时候，**实际上调用的是 `window.Object.prototype.toString`**。

**那么问题又来了：**

`window.Object.prototype.toString` 是怎么获取 `obj` 的内容的呢？

**因为 `obj.toString()` 等价于 `obj.toString.call(obj)`！**JS 又偷偷的帮我们做事了！

同时 `obj.toString.call(obj)` 等价于 `window.Object.prototype.toString.call(obj)`。

正是这样，JS 就悄咪咪的完成了 `obj.toString()`。

**再来看一下数组：**

```javascript
var arr = []
arr.push(1) // [1]
```

其实和对象是一样的，并且 JS 的数组本来就**是对象伪装的**！

`var arr = []` 会让 `arr` 指向一个空对象，然后 `arr.__proto__` 指向 `window.Array.prototype`。

（其实 `arr` 还有一个 `length: 0`属性）

所以我们在调用 `arr.push` 的时候，`arr` 发现自身没有 `push` 属性，就前往 `arr.__proto__` 上找 `push`。

因此 `arr.push` 实际上是 `window.Array.prototype.push`。

并且：

- `arr.push(1)` 等价与 `arr.push.call(arr,1)`
- `arr.push.call(arr,1)` 等价于 `window.Array.prototype.push.call(arr, 1)`

看，JavaScript 的原型模式其实很优美很简单！

---

**反证一下：**

假设我们把 `__proto__` 去掉，那么

```javascript
var obj = {}
obj.toString() // 报错，没有 toString 方法
```

所以只能这样声明一个对象：

```javascript
var obj = {
  toString: window.Object.prototype.toString,
  valueOf: window.Object.prototype.valueOf
}
obj.toString() // '[object Object]'
```

现在知道 `__proto__` 帮你省多少代码了吧？

假设我们删掉 `prototype`，包括 `window.Object.prototype` 和 `window.Array.prototype`。

那么 `window.Object.prototype.toString` 也一并被删除了。

然后我们基本就没法写代码了……全都得自己重新来做！

```javascript
var obj = {}
obj.toString() // toString 不存在，因为 toString 没有定义过啊
```

`prototype` 的意义就是把共有属性预先定义好，给之后的对象用。

**总结：**

1. `prototype` 指向一块内存，这个内存里面有共用属性，而 `__proto__` 指向同一块内存；
2. `prototype` 和 `__proto__` 的不同点在于：`prototype` 是**构造函数**的属性，而 `__proto__` 是对象的属性；
3. **如果没有 `prototype`，那么共用属性就没有立足之地；**
4. **如果没有 `__proto__`，那么一个对象就不知道自己的共用属性有哪些。**

（完）
