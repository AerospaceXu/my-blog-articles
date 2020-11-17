# 一个关于 useState 的误解

本文写于 2020 年 11 月 17 日

前两天有人问了我一个问题，他有一段这样的代码：

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <div>
      <h1>{n}</h1>
      <button onClick={() => setN(x => x + 1)}>+1</button>
      <button onClick={() => setTimeout(() => console.log(n), 3000)}>log</button>
    </div>
  )
}
```

如果他先点击 “+1” 按钮，再点击 log 按钮，控制台就会在 3s 后输出 `h1` 内显示的值——即 +1 后的数字。

但是如果他**先log，再点击 “+1”，获得的却还是上一次的数值，并不是 `h1` 显示的值。**

这是为什么？

我之前写过一篇文章，是关于 useState 的原理分析。

**setState 本质上不是改变了 state 的值，而是有了一个新的 state。**React 重新执行了函数（所以之前的 n 是在旧函数里，新的 n 在新的函数里），将原来的值进行处理之后赋给了新的值——所以原来的值永远是原来的值。

setTimeout 是在旧的函数里触发的，3s 后读取的就是旧的函数里的 n 值写。

（完）