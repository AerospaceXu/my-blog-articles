# React Hooks 逐个击破

本文写于 2020 年 8 月 23 日

最近忙于实习，是在是没什么时间写博客写自己的项目，也就周末空闲一点罢。

这让我想到之前说的一个梗，问：大厂程序员怎么学习？答：大厂程序员天天加班做业务改需求，没时间学习。

看来是真的。

最近在实习的过程中接手的项目有大量的 React Hooks 代码，但说实话我对于 Hooks 的了解仅仅停留在能用的阶段，甚至于很多不常规的钩子，例如 `useMemo`, `useReducer` 等等都不太了解。所以这个周末恶补了一下相关的知识。

鉴于 React 文档的中文写的跟英文似的，就写篇“中文”博客供大家参考吧。

## useState

对于函数组件而言，是没有 state 的，但是 state 常常是不可或缺的，所以 React 提供了 useState 钩子。

还是经典的计数器场景：

```JavaScript
import React, { useState } from "react";

function Count() {
  const [n, setN] = useState(0);

  const handleClick = () => {
    // 这里 React 更推荐我们写成 setN(n => n + 1)
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

`useState` 接受一个参数，返回一个数组，这个数组的第一个元素是你想要的 state，第二个参数则是改变这个 state 的方法。

例如计数器中，我们写上了 `const [n, setN] = useState(0);`。这里的 `n` 就是我们想要的 state，`setN` 就可以用来改变 `n` 的值。

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
