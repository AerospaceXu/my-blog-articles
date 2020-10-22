# 简单的useState实现

本文写于 2020 年 10 月 21 日

以下是一段非常简单的 React 代码：

```JavaScript
const App = () => {
  const [n, setN] = useState(0);
  return (
    <div>
      {n}
      <button onClick={() => setN(x => x + 1)}>+1</button>
    </div>
  );
}

React.render(<App />, rootElement)
```

这样的用法和以往的 `setState` 是有明显的不同的，他看起来更像 redux——我们初始化一个 `state`，然后 `dispatch` 一个 `action`，再由 `reducer` 改变 `state` 后返回新的 `state`。

## Redux 思想实现 useState

既然我们觉得它像，那我们就来自己实现一个吧。

_不熟悉 Redux 思想的同学请自行阅读文档_

```javascript
const useState = (initialValue) => {
  let state = initialValue;
  const dispatch = (newState) => {
    state = newState;
    render(<App />, document.getElementById('root'));
  };
  return [state, dispatch];
};
```

然后我们用这个自定义的 `useState` 代替 React 的 `useState`——就会发现我们失败了，`setN` 无论如何都不会有任何反应。

这是因为我们每次重新 render 的时候都重新执行了函数，于是我们**总是会重新赋值**。

## 为什么不会重新赋值？

对于这段 React 代码来说，当我们第一次运行时，React 会进行首次渲染，即 `render(<App />, ...)`。

在此过程中，会先调用 `App()`，之后便会得到虚拟 DOM，再创建真实的 Div。

当我们触发点击事件时，会调用 `setN`，再次 `render()`。之后调用 `App()`，然后得到新的虚拟 DOM，进行 diff 算法，根据 diff 算法的结果去更新新的 Div。

而不管是第一次渲染，还是第二次调用，都会调用 `useState()`。

**但是我们写的是 `useState(0)` 啊，两次调用明明是一样的代码，为何 n 的值不同？**怎么解决这个问题呢？

很简单，闭包嘛。

```js
const createUseState = () => {
  let state;
  const useState = (initialValue) => {
    if (!state) {
      state = initialValue;
    }
    const dispatch = (newState) => {
      state = newState;
      render(<App />, document.getElementById('root'));
    };
    return [state, dispatch];
  };
};
```

这样就解决了重新赋值的问题。

## 多次调用

但是我们需要多次调用 `useState` 呀，不可能只用一次的。

于是我们将 `state` 改为一个数组：`const state = [];`。

```js
const createUseState = () => {
  const state = [];
  let index = 0;
  return (initialValue) => {
    state[index] = state[index] || initialValue;
    const currentIndex = index;
    const dispatch = (newState) => {
      state[currentIndex] = newState;
      // 重点
      index = 0;
      ReactDOM.render(
        <React.StrictMode>
          <App />
        </React.StrictMode>,
        rootElement
      );
    };
    return [state[index++], dispatch];
  };
};
```

我们创建了一个 index 变量来控制索引。它需要我们保证每次重新渲染 App 传入数组的元素是一样的——这就是为什么我们不可以将 useState 写在 if 判断中。

在上述代码中有一处重点，在于我们**需要在每次 set 之后将索引归零 `index = 0`。**

因为每次 `render` 结束后，React 都会重新执行该函数。

（完）
