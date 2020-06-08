# JS 异步与 Promise

本文写于 2020 年 6 月 8 日

## 1. 同步与异步与回调函数

Promise 现在是前端面试必考题呀，但是先不急着看 Promise，我们首先来看看什么是异步。

———— 所谓异步，就是不是同步（笑）。

咳咳，假设我们去商场吃饭，但是商场的餐厅大家都知道，非常的火爆，常常需要排队一个多小时。

这时候大家除非特别累了，不然一定**不会原地等待，而是选择去其他楼层转一转**。

这就是异步啦。同步又是什么呢？

比如我们去银行取钱，在工作人员将钱交给我们之前，我们必须站在窗口前等待 —— 直到拿到钱。

回到刚刚商场餐厅的例子。

当我们排了很久的队，终于轮到了的时候，餐厅会如何通知呢？或者说餐厅不通知我们，我们该如何直到可以吃饭了呢？

有很多种方法嘛，比如每十分钟回去看一眼，或者分一个队友守在餐厅门口，等队伍快排到的时候就给我们打电话。

前一种方法叫做**轮询**，后一种方法就叫做**回调**。

**回调回调，就是回头（一会儿）再调用你。**

上代码：

```JavaScript
function fn1() {}

function fn2(fn) {
	fn()
}
fn2(fn1)
```

我们先声明一个 `fn1` 函数，然后声明一个能接受一个函数作为参数的 `fn2` 函数。最后调用 `fn2` 函数，传入 `fn1` 作为参数。

`fn1` 就是回调函数。因为并**不是我们调用的它，而是 `fn2` 调用的它**。

一般来说，如果函数不是我们调用的，那就是一个回调函数。

我们常见的回调函数一般和这些东西有关：`setTimeout`, `AJAX`, `AddEventListener`。它们都是会调用别的函数，而被调用的函数就是**回调函数**了。

那现在讲明了“同步”、“异步”、“回调”。请问三者之间有什么关系吗？

回调函数是异步函数的专属嘛？当然不是啦，我们刚刚举的例子不就是同步编程吗？一样用到了回调函数。

还有很常见的 `arr.forEach(item => item + 1)`，`forEach` 里面的函数，它也是一个同步函数。

所以说我们得到了这么两条结论：

1. 异步任务拿不到结果（比如商场餐厅排队），一般需要依赖与回调函数；
2. 回调函数可以在异步任务结束时执行，也可以在同步任务中使用。

## 2. 我们为什么需要 Promise？

刚刚我们说了异步编程，异步任务结束之后可以用回调函数可以拿到最后的结果。

但是如果有俩结果呢？比如获取成功和获取失败。

那就传入两个参数啰，Node.js 都这么用。

```JavaScript
fs.readFile('./data.txt', (error, data) => {
	if(error) {
		console.log(error)
		return
	}
	console.log(data.toString())
})
```

或者还可以用两个回调函数：

```JavaScript
ajax('GET', '/data.json', (data) => {}, (error) => {})
```

但是这些方法其实都不有些不足。

比如：

1. 名称五花八门不规范，可以用 success+error；可以用 success+fail；可以用 done+fail……
2. 回调地狱，一层套一层套一层，无限缩进；
3. 难以进行错误处理。

所以程序员必须解决这些问题。首先我们需要**规范回调的名字和顺序**；其次必然的，要**拒绝回调地狱，增强代码可读性**；最后还要让我们**方便的捕捉错误**。

于是就有了 Promise。

**但是注意，Promise 不是 ES6 发布的时候才有的。**

1976 年，就有人提出了 Promise 思想。前端只是结合了 Promise 与 JavaScript，制定了 Promise/A+规范

> Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了`Promise`对象。 ——《ES6 标准入门》

## 3. Promise 的基本用法

> 所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。 ——《ES6 标准入门》

ES6 规定，`Promise`对象是一个构造函数，用来生成`Promise`实例。

下面代码创造了一个`Promise`实例：

```javascript
const promise = new Promise((resolve, reject) => {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value)
  } else {
    reject(value)
  }
})
```

`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。

`resolve`函数的作用是：将`Promise`对象的状态从“未完成”变为“成功”（pending -> fulfilled），它在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。

`reject`函数的作用是：将`Promise`对象的状态从“未完成”变为“失败”（pending -> rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。

```javascript
promise.then(
  function (value) {
    // success
  },
  function (error) {
    // failure
  }
);
```

`then`方法可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用，第二个回调函数是`Promise`对象的状态变为`rejected`时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受`Promise`对象传出的值作为参数。

**看不懂？没关系，我们来做一个小模拟。**

现在假设我们要吃饭，那么有三个步骤：

1. 洗菜切菜做饭；
2. 盛菜端饭坐下吃饭；
3. 收拾桌子洗碗。

试想一下，如果没有做饭，可以直接跳跃到第二步吃饭吗？

可以，可以喝西北风。

所以这个过程是有顺序的，必须上一步执行完成，才能顺利进行下一步。

**ES6 的写法：**

首先有一个状态值，表示我们目前是在第一步第二步还是第三步：`let state = 1`

第一步，写一个洗菜切菜做饭函数：

```javascript
function step1(resolve, reject) {
  console.log('洗菜！切菜！做饭！');
  if (state == 1) {
    resolve('洗菜切菜做饭complete！');
  } else {
    reject('洗菜切菜做饭Failed……');
  }
}
```

_这里看不懂没关系，就当作是抛出一个消息即可。_

第二步，开始吃饭：

```JavaScript
function step2(resolve, reject) {
  console.log("吃饭啦！")
  if(state == 1) {
    resolve("吃饭成功！")
  } else {
    reject("吃饭失败……")
  }
}
```

第三步，吃完了该洗碗去了：

```javascript
function step3(resolve, reject) {
  console.log('吃完了洗碗！');
  if (state == 1) {
    resolve('洗完啦！');
  } else {
    reject('洗碗失败……');
  }
}
```

接下来，我们就需要写`Promise`对象了：

```javascript
new Promise(step1)
  .then(function (val) {
    console.log('val', val);
    return new Promise(step2);
  })
  .then(function (val) {
    console.log(val);
    return new Promise(step3);
  })
  .then(function (val) {
    console.log(val);
    return val;
  });
```

相信只要懂小学二年级英语的同学，就能大概看懂这段代码的意思了：

1. `new` 一个 `Promise` 对象，然后传入一个函数，也就是第一步的洗菜函数；
2. 函数内部有一个 `resolve` 和一个 `reject`，分别是请求成功接收的消息，和请求被拒绝后接收的消息；
3. 接着是一个`then`，他也传入一个函数，接收上一步抛出的值，也就是 `resolve` 或者是 `reject`；
4. 再 `return` 一个新的 `Promise` 对象，用作下一步的操作。

## 4. Promise 的特点

`Promise`对象有以下两个特点。

1. **对象的状态不受外界影响。**

   `Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都无法改变这个状态——这就是`Promise`这个名字的由来，所谓“承诺”，即其他手段无法改变。

2. **一旦状态改变，就不会再变，任何时候都可以得到这个结果。**

   `Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`；从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果！这时就称为 resolved（已定型）。如果改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

`Promise`对象让我们可以将异步操作以同步操作的流程表达出来，避免了“回调地狱”。

并且此外，`Promise`对象提供统一的原生接口，使得 JS 控制异步操作更加容易！

但是`Promise`也有缺点：

1. 无法取消`Promise`，一旦新建它就会立即执行，无法中途取消（axios 可以取消）；
2. 如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部；
3. 当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

（完）
