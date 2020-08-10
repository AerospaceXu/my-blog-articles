# 盲猜 useState 的实现

本文写于 2020 年 8 月 10 日

以下是一段非常简单的 React 代码：

```JavaScript
function App() {
  const [n, setN] = useState(0);
  return (
    <div>
      {n}
      <button onClick={() => setN(x => x + 1)}>+1</button>
    </div>
  )
}

React.render(<App />, rootElement)
```

对于这段代码来说，当我们第一次运行时，React 会进行首次渲染，即 `render(<App />, ...)`。

在此过程中，会先调用 `App()`，之后便会得到虚拟 DOM，再创建真实的 Div。

当我们触发点击事件时，会调用 `setN`，再次 `render()`。之后调用 `App()`，然后得到新的虚拟 DOM，进行 diff 算法，根据 diff 算法的结果去更新新的 Div。

而不管是第一次渲染，还是第二次调用，都会调用 `useState()`。

**但是我们写的是 `useState(0)` 啊，两次调用明明是一样的代码，为何 n 的值不同？**
