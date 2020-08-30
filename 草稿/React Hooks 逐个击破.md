# React Hooks 逐个击破

本文写于 2020 年 8 月 23 日

最近忙于实习，是在是没什么时间写博客写自己的项目，也就周末空闲一点罢。

这让我想到之前说的一个梗，问：大厂程序员怎么学习？答：大厂程序员天天加班做业务改需求，没时间学习。

看来是真的。

最近在实习的过程中接手的项目有大量的 React Hooks 代码，但说实话我对于 Hooks 的了解仅仅停留在能用的阶段，甚至于很多不常规的钩子，例如 `useMemo`, `useReducer` 等等都不太了解。所以这个周末恶补了一下相关的知识。

鉴于 React 文档的中文写的跟英文似的，就写篇“中文”博客供大家参考吧。

## 1 useState

对于函数组件而言，是没有 state 的，但是 state 常常是不可或缺的，所以 React 提供了 `useState` 钩子。

`useState` 接受一个参数，返回一个数组，这个数组的第一个元素是你想要的 state，第二个参数则是改变这个 state 的方法。

### 1.1 传入普通数据类型

还是经典的计数器场景：

```JavaScript
import React, { useState } from "react";

function Count() {
  const [n, setN] = useState(0);

  const handleClick = () => {
    // 这里 React 更推荐我们写成 setN(n => n + 1)，具体会在后面详细介绍
    // 此处是为了方便理解，所以直接写了 n+1
    setN(n + 1);
  }

  return (
    <div>
      <span>{n}</span>
      <button onClick={handleClick}>+1</button>
    </div>
  );
}

export default Count;
```

例如计数器中，我们写上了 `const [n, setN] = useState(0);`。这里的 `n` 就是我们想要的 state，`setN` 就可以用来改变 `n` 的值。

### 1.2 传入引用数据类型

但如果我们传入的是一个对象的话，`useState` 就会出现很多情理之中意料之外的操作。

```JavaScript
const [user, setUser] = useState({
  name: "徐航宇",
  age: 18,
  nickName: "我欲乘风归去"
})

setUser({
  nickName: "又恐琼楼玉宇"
})
```

这是一个常见的操作，用户修改自己的昵称。**但是这个写法是非常错误的。**一旦你这么写了，整个 `user` 都会变成 `setUser` 中的模样，也就是说，你的 `name` 和 `age` 属性都会消失不见。

所以我们只能这么写：`setUser({...user, nickName: "又恐琼楼玉宇"})`。那么这种情况下，我们的 UI 就不会局部更新了！因为你直接换掉了对象的地址。

### 1.3 传入函数

`const [n, setN] = useState(() => 1);` 这种写法和 `const [n, setN] = useState(1)` 是完全等价的。

为什么需要函数呢？

因为可能我们的初始值生成的步骤会比较复杂，使用函数后 `return` 最后的值会更方便简单。

### 1.4 setN 使用函数参数

在 1.1 的例子中，我们在注释中写道：“希望使用函数参数”。

为什么要使用函数参数呢？

可以尝试一下这个例子：

```JavaScript
const handleClick = () => {
  setN(n + 1);
  setN(n + 1);
}
```

如果我们点击后，触发这个按钮会发生什么？

**n 只会加 1！**不会加 2。

**这是因为当我们 setN 之后，n 的值是没有变的，n 的值会异步更新**（具体我在另一篇文章「盲猜 useState 的实现」中解释过）。

可是我们紧接着又操作了一次 `setN(n + 1);`，那自然只有第二次生效啦。

如果我们使用 `setN(n => n + 1);` 就不一样了，为了避免歧义，这里也可以写成 `setN(bilibili => bilibili + 1);`。

这是让 `setN` 接受了一个操作，这个操作请求它将 `n` 加 1。这样 `n` 就会及时的发生改变。

## 2 useEffect

`useEffect()` 用来引入具有副作用的操作.

`useEffect()` 的用法如下。

```JavaScript
useEffect(() => {
  // 一些操作
}, [dependencies])
```

`useEffect()` 接受两个参数：第一个参数是一个函数，异步操作的代码放在里面；第二个参数是一个数组，用于给出依赖项，只要这个数组发生变化，`useEffect()` 就会执行。

第二个参数可以省略，这时每次组件渲染时，就会执行 `useEffect()`。

最常见的就是向服务器请求数据。例如以前放在 `componentDidMount()` 里面的代码，现在可以就放在 `useEffect(() => {}, [])`。

其实很多人喜欢用 `useEffect()` 来模拟原来的生命周期函数，但其实我以为 React 推出 Hooks 的理由之一就是不太喜欢生命周期函数，所以我们需要转换思想，从生命周期函数的框框里跳脱出来。

来个例子，还是计数器。

```JavaScript
useEffect(() => {

}, [n])
```

## 3 useReducer

React 只是一个 UI 库，这点官网本身就是这么说的：“用于构建用户界面的 JavaScript 库”。

React 本身不提供状态管理功能，通常需要使用外部库。这方面最常用的库是 Redux。

Redux 的核心概念是，组件发出 action 与状态管理器通信。状态管理器收到 action 以后，使用 Reducer 函数算出新的状态，Reducer 函数的形式是 `(state, action) => newState`。

`useReducer` 看名字就知道，和 Reducer 很像，所以我们是这么用的：

`const [state, dispatch] = useReducer(reducer, initialState);`
