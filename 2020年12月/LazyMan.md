# LazyMan

本文写于 2020 年 12 月 22 日

看到一个非常有意思的题目，叫做 LazyMan：

实现一个 LazyMan，可以按照以下方式调用：`LazyMan("Hank");`。输出：`Hi! This is Hank!`。

要求：

```js
LazyMan('Hank').sleep(10).eat('dinner');
// Hi! This is Hank!
// 等待10秒..
// Wake up after 10
// Eat dinner~

LazyMan('Hank').eat('dinner').eat('supper');
// Hi This is Hank!
// Eat dinner~
// Eat supper~

LazyMan('Hank').sleepFirst(5).eat('supper');
// 等待5秒
// Wake up after 5
// Hi This is Hank!
// Eat supper~
```

经过初步的分析，这道题的考点应该有两点：

- 链式调用
- 队列
- Event Loop

## 如何实现链式调用？

在 jQuery 的设计中就存在大量的链式调用：`$('#app').addClass('xxx').xxxx()`。

如果 `$` 函数长这样：

```ts
function $(selector: string) {
  const doms = document.querySelectorAll(selector);

  return {
    doms,
    addClass: () => {
      // ...
    },
    xxxx: () => {
      // ...
    },
  };
}
```

**这样子我们是没办法使用链式调用的，**

因为在调用完了 `addClass` 函数之后返回的是 `undefined`，我们无法在 `undefined` 上调用 `xxxx`。

所以我们需要返回一个 **Api 对象**。

```ts
function $() {
  const api = {
    doms,
    addClass: () => {
      // ,,,
      return api;
    },
    xxxx: () => {
      // ...
      return api;
    },
  };
  return api;
}
```

所以很显然，我们的 LazyMan 函数应该长这样：

```js
const LazyMan = () => {
  const eat = () => {
    console.log('我吃饭了');
    return api;
  };
  const sleep = () => {
    console.log('我睡觉了');
    return api;
  };
  const sleepFirst = () => {
    console.log('我先睡了');
    return api;
  };

  const api = {
    eat,
    sleep,
    sleepFirst,
  };
};
```

## 队列

分析题目，我们发现 `sleep` 函数很好实现，但 `sleepFirst` 并不方便——我们不可能让之后执行的函数超越时间的阻隔，跑到最前面执行——这是违背规律的。

所以我们必然需要创建一个队列，通过维护队列的顺序来达到插队的目的。

```js
const LazyMan = () => {
  const queue = [];
  // ...
};
```

该队列存放的就是每次的任务函数，调用一般的 api 时，往队伍末尾加上任务即可，而 `sleepFirst` 做的只是把任务“插队”，放到队列开头。

所以我们的顺序就是：执行 `LazyMan` => 调用 api 时将函数加入队列 => 如果是 `First` 就需要插队 => 任务调用结束后执行队列下一项 => 开始 => 执行队列第一项。

## Event Loop

但是我们在这里发现一个问题。

如果说，我们是这么调用的：`LazyMan('Hans').eat().sleepFirst().end()`，拥有一个 `end()` 方法收尾，是非常容易去完成这个函数的——所有方法只负责维护队列，`end` 方法执行队列即可。

但是不行呀，我们没有 `end` 方法的空间。

**这就要运用到 JavaScript 的异步机制了。**

不论是 Node.js 还是浏览器，我们总是在执行完当前的任务栈之后，才会去执行我们异步任务的回调函数。详情请看[EventLoop 是什么](https://www.cnblogs.com/xhyccc/p/14093525.html)。

当我们执行：

```ts
LazyMan('Hans').eat().sleepFirst();
```

的时候，我们只需要让内部的「执行队列」的方法处于异步队列就可以了！只要我们这几个方法全部都是同步的，他**总是会在执行完这些之后再去执行队列**。

## 完整代码

```ts
const LazyMan = (name: string) => {
  const queue: any[] = [];

  queue.push(() => {
    console.log(`你好，${name}`);
    next();
  });

  const next = () => {
    const task = queue.shift();
    task?.();
  };

  const eat = () => {
    const action = () => {
      console.log(`${name} 吃饭了`);
      next();
    };
    queue.push(action);

    return api;
  };

  const sleep = (n: number) => {
    const action = () => {
      setTimeout(() => {
        console.log(`睡了 ${n} 秒，我睡醒了`);
        next();
      }, n * 1000);
    };
    queue.push(action);
    return api;
  };

  const sleepFirst = (n: number) => {
    const action = () => {
      setTimeout(() => {
        console.log(`睡了 ${n} 秒，我睡醒了`);
        next();
      }, n * 1000);
    };
    queue.unshift(action);
    return api;
  };

  const api = {
    eat,
    sleep,
    sleepFirst,
  };

  setTimeout(() => next(), 0);

  return api;
};

LazyMan('Han').sleep(3).eat();
```

（完）
