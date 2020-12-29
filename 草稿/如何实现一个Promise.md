# 如何实现一个 Promise

本文写于 2020 年 12 月 25 日

## Promise 的关键点

### 状态

Promise 拥有三个状态：`pending`, `fulfilled`, `rejected`。

- `pending` 为待定状态，`Promise` **初始时**就应该处于该状态，可以被 `settled` 成为 `fulfilled` 或者 `rejected` 状态；
- `fulfilled` 为兑现状态，表示执行成功，该状态**不可改变**，且有一个私有的不可变的值 `value`；
- `rejected` 为拒绝状态，表示执行失败，该状态**不可改变**，有一个私有的不可变的原因 `reason`。

### then

Promise 对象需要提供一个 `then` 方法来访问当前或者最终的 `value` 或 `reason`。

```js
p.then(onSuccess, onFail);
```

- `then` 方法接受两个函数作为参数，并且都为可选参数；
- 两个函数都是异步执行；
- 当调用 `onSuccess` 时，会将 `Promise` 对象的 `value` 值作为参数传入；
- 当调用 `onFail` 时，会将当前 `Promise` 对象的 `reason` 作为参数传入；
- `then` 函数的返回值为 `Promise`；
- `then` 可以被同一个 `Promise` 多次调用。

### Promise 的解决过程

Promise 的解决过程是一个抽象操作，接受一个 Promise 和一个 x。

如果 x 等于 Promise，则抛出 TypeError，reject Promise。

如果 x 为 promise 的实例，那么继续等到 x resolve 或者 reject，否则根据 x 的状态 resolve 或是 reject Promise。

（完）
