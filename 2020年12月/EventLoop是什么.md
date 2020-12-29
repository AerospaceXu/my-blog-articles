# Event Loop 是什么？

本文写于 2020 年 12 月 6 日

广义上来说 Event Loop 并不是 JavaScript 独有的概念，他是一个计算机的通用概念。

狭义上来说，**只有 Node.js 才有 Event Loop，浏览器并没有。**

## 一个场景引发的困惑

为什么需要 Event Loop 呢？先看一个常见的场景，如果我们同时执行了三种不同的异步事件：

```js
setTimeout(foo, 100);
fs.readFile('./README.md', bar);
server.on('close', doSth);
```

我们知道在计算机中，操作系统会帮我们新建一个「进程」使应用程序可以执行，但是**一个进程在一个时刻只能执行一个任务**，如果我们需要同时执行这三个任务，有三个方法：

1. 新建一个进程；
2. 新建一个线程；
3. 排队执行。

如果不太了解，可以看我之前的一篇文章，[「进程与线程」](https://www.cnblogs.com/xhyccc/p/14014909.html)。

JavaScript 作为一门**单线程**的语言，肯定不能分出三条线程去执行这三条语句。那么假设计时器结束的一瞬间，三个回调函数**同时触发**了，Node 会怎么处理呢？

_PS：Node.js 读取文件用的是 libuv，自己并不会去读写文件，所以单线程也可以异步的读写文件_

我们可以推测出以下两点：

1. Node.js 肯定会以某种顺序执行；
2. 这种顺序应该是规定好的（优先级）。

## Event Loop 的解释

回到 Event Loop 本身，我们拆开来看看。

什么叫做 Event？Event 就是事件，比如回调函数的触发就是一个事件。

什么叫做 Loop？Loop 就是循环，比如 for 循环 while 循环。

**事件是有优先级的，所以处理时候是分先后的。**Node.js 按照顺序去“询问”每个 Event，并且循环往复的去“询问”：

```
Event1 => Event2 => Event3 => Event1 => Event2 => ...
```

这就变成了 **「poll（轮询）」**。

**操作系统触发事件，JavaScript 处理事件，Event Loop 就是事件处理顺序管理的「解决方案」。**

维基是这么解释的：Event Loop 是一个程序结构，用于等待和发送消息和事件。

简单说，就是在程序中设置两个线程：

- 一个负责程序本身的运行，称为"主线程"；
- 另一个负责主线程与其他进程的通信，被称为 **「Event Loop 线程」**，也有人叫他「消息线程」。

## Node 中的 Event Loop

```
     |-----------------------|
---->|         timer         |
|    |-----------------------|
|                |
|                |
|    |-----------------------|
|    |    close callbacks    |
|    |-----------------------|
|                |
|                |
|    |-----------------------|
|    |     idle, prepare     |
|    |-----------------------|
|                |
|                |                 |------------------|
|    |-----------------------|     |     incoming:    |
|    |          poll         |<----|    connections,  |
|    |-----------------------|     |     data, etc.   |
|                |                 |------------------|
|                |
|    |-----------------------|
|    |         check         |
|    |-----------------------|
|                |
|                |
|    |-----------------------|
-----|    close callbacks    |
     |-----------------------|
```

这是 Node.js 官方文档上的示意图，我们来分析一下。

- 首先第一步，Node 会先去寻找是否存在计时器；
- 然后在看有没有其他的 I/O 相关的回调函数；
- idle 和 prepare 阶段根据字面意思推断，应该是清理一下，休息一下；
- 下一步进入轮询的阶段，检查系统事件；
- check 阶段检查 setImmediate 回调；
- close callbacks 就是处理类似 socket 关闭的事件。

这里可以看到 check 阶段检查的是 setImmediate 回调，有的面试题目可能会问 `setImmediate(fn)` 和 `setTimeout(fn, 0)` 谁先执行，这里我们就能回答了：不确定，因为 Event Loop 是个圈。

另外，setImmediate 不是一个标准特性，MDN 不建议我们在生产环境中使用它，**浏览器只有新版的 IE 支持**。

在 Node 的 Event Loop 中，最常用的就是：

- timer 检查；
- poll 轮询；
- check 检查。

我们先当作其他阶段都不存在了，只把这三个阶段作为循环。

因为 timer 阶段处理定时器函数回调，check 阶段处理 setImmediate 回调，所以如果这两个执行栈没有任务的话，我们就不会走这两个阶段了。

Node.js 会很智能的在其他阶段空闲时只停留在该阶段。

并且**大部分时间，Node.js 都停留在 poll 阶段**，文件请求、网络请求的事件处理也多半在这个阶段进行。

**官方例子：**

```js
const fs = require('fs');

// 假设花费 95ms 读取文件
function someAsyncOperation() {
  fs.readFile('/path/to/file', callback);
}

const timeoutScheduled = Date.now();

setTimeout(() => {
  const delay = Date.now() - timeoutScheduled;
  console.log(`${delay}ms 后执行了 setTimeout 的回调函数`);
}, 100);

// 95ms 的异步操作
someAsyncOperation(() => {
  const startCallback = Date.now();
  // 10ms 的同步操作
  while (Date.now() - startCallback < 10) {
    // nothing to do
  }
});
```

这里我们花 95ms 读完文件后又做了 10ms 的同步操作。但问题是在 95ms 结束后 5ms 就需要执行定时器了呀，这里硬生生被 `while` 的同步操作卡住了 5ms，Node.js 在这个过程中是如何处理的呢？

1. 当我们的 Event Loop 进入 poll 阶段，发现 poll 的队列是空的，因为文件没有读完。
2. 于是 Event Loop 检查了一下最近的计时器，发现还需要 100ms，于是决定这段时间就停留在 poll 阶段。
3. 在 poll 阶段停留了 95ms 之后，`readFile` 操作完成，耗时 10ms 的同步操作被系统放入 poll 队列，执行完毕之后 poll 队列为空。
4. Event Loop 紧接着又去看了一眼计时器，发现已经超时了 5ms 了。于是由经 check 阶段，close callbacks 阶段，Event Loop 又回到了 timer 阶段执行了超时计时器的回调函数。

这里如果我们一直占着 poll 阶段做同步任务会怎么样呢？

Node.js 做了限制，会有最长占用时长的，根据操作系统而定。

## Event Loop 例题

我们来做几道例题吧：

### 例题一

```js
fs.readFile('xx.txt', () => {
  console.log('fs');
  setTimeout(() => {
    console.log('setTimeout');
  }, 0);
  setImmediate(() => {
    console.log('setImmediate');
  });
});
```

他们的输出顺序该是如何？

因为 readFile 是读取文件，该操作既不是 timer，也不是 setImmediate，所以在 poll 阶段进行。

poll 之后是 check，check 之后才是 timer。所以先执行 setImmediate，后执行 setTimeout。

### 例题二

```js
setImmediate(() => {
  console.log('setImmediate');
  setTimeout(() => {
    console.log('setTimeout in setImmediate');
  }, 0);
});

setTimeout(() => {
  console.log('setTimeout');
  setImmediate(() => {
    console.log('setImmediate in setTimeout');
  });
}, 0);
```

这题应该分情况讨论。

情况一：

如果是 timer 先执行，那我们必然会先执行外层的 setTimeout 的回调函数。输出完 `'setTimeout'` 之后，将 setImmediate 的回调放入 check 阶段——注意，这里 check 的执行栈中已经有了第一个 setImmediate 了。

执行结束后，进入 check 阶段，执行第一个 setImmediate，输出 `'setImmediate'`，然后执行第二个 setImmediate，输出 `'setImmediate in setTimeout'`。再将 setTimeout 放入 timer 队列。执行结束。

进入 timer 队列，输出 `'setTimeout in setImmediate'`。

情况二：略

### 例题三

```js
console.log(1);

setTimeout(() => {
  console.log('setTimeout1');
  Promise.resolve('promise').then((res) => {
    console.log(res);
  });
}, 0);

setTimeout(() => {
  console.log('setTimeout2');
}, 0);
```

这里我们会发现一个问题，刚刚 Node.js 的官方图**没有给出任何关于 Promise 的细节**，甚至于在原文中搜索 promise 也是一无所获。

那是因为 Node.js 的 Promise 是以 nextTick 为基础实现的。

nextTick 触发的时机是在**当前任务队列结束之后立即执行**。这里应该先输出 `'setTimeout1'`，再输出 `'promise'`，最后输出 `'setTimeout2'`。

## 浏览器的 Event Loop

浏览器只有宏队列和微队列，比 Node.js 简单很多。（注意，这都不是 JS 引擎提供的，而是 Node 和浏览器提供的）。

- 宏列队：用来保存待执行的 macrotask （宏任务），比如：timer / DOM 事件监听 / ajax 回调；
- 微列队：用来保存待执行的 microtask（微任务），比如：Promise 的回调（then 方法） / MutationObserver 的回调。

_在 Promise/A+的规范中，Promise 的实现可以是微任务，也可以是宏任务。但是普遍的共识表示（至少 Chrome 是这么做的），Promise 应该是属于微任务的。_

当前执行栈执行完毕时，会立刻先处理所有「微任务」队列中的事件，然后再去「宏任务」队列中取出一个事件。也就是说**同一次 Event Loop 中，微任务永远在宏任务之前执行。**

说到这里其实很明白了，Node.js 中的 Event Loop 有很多阶段，不停的循环；而浏览器中只有两个阶段，分别查看宏任务和微任务队列。

看个例子：

```js
setTimeout(() => console.log(1), 0);

new Promise((resolve) => {
  console.log(2);
  resolve('promise');
}).then((res) => {
  console.log(res);
});

console.log(3);
```

- 当我们执行 setTimeout 的时候，将回调函数存入宏队列；
- 执行 `new Promise` 时，执行函数中的代码，将 then 任务存入微队列；
- 执行 `console.log(3)`，当前执行栈执行完毕；
- 执行微队列；
- 执行宏队列。

## 做道题总结一下

OK，我们再看一道题目：

```js
const foo = async () => {
  console.log('foo');
  await bar();
  console.log('bar end');
};

const bar = async () => {
  console.log('bar');
};

foo();

new Promise((resolve) => {
  console.log('promise');
  resolve('promise down');
}).then((res) => {
  console.log(res);
});
```

首先第一步：因为 `async` `await` 是 Promise 的语法糖，所以需要将思维转化成 Promise，不可以真的把这段代码当成同步代码。

- 所以先输出 `'foo'` 是没问题的，这相当于 `new Promise(() => { console.log('foo') })` 一样；
- 然后输出 `'bar'` 也是没问题的，相当于在第一次 `new` 的 `Promise` 中又 `new` 了一次；
- 接着输出 `'bar end'` 的语句应该被丢进微队列了；
- 执行下方的 `Promise`，输出 `'promise'`，将 `then` 丢进微队列；
- 执行微队列：先输出 `'bar end'`，后输出 `'promise down'`。

（完）
