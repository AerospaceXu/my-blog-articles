# LazyMan

<!-- TODO: 本文写于 2020 年 12 月  日 -->

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
- 任务队列

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

## 任务队列

分析题目，我们发现 `sleep` 函数很好实现，但 `sleepFirst` 并不方便——我们不可能让之后执行的函数超越时间的阻隔，跑到最前面执行——这是违背规律的。

所以我们必然需要创建一个队列。

```js
const LazyMan = () => {
  const queue = [];
  // ...
};
```

该队列存放的就是每次的任务函数，调用一般的 api 时，往队伍末尾加上任务即可，而 `sleepFirst` 做的只是把任务“插队”，放到队列开头。

所以我们的顺序就是：执行 `LazyMan` => 调用 api 时将函数加入队列 => 如果是 `First` 就需要插队 => 任务调用结束后执行队列下一项 => 开始 => 执行队列第一项。

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
