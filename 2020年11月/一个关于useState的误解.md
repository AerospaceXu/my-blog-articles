# 一个关于 useState 的误解

本文写于 2020 年 11 月 17 日

前两天有人问了我一个问题，他有一段这样的代码：

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <div>
      <h1>{n}</h1>
      <button onClick={() => setN((x) => x + 1)}>+1</button>
      <button onClick={() => setTimeout(() => console.log(n), 3000)}>
        log
      </button>
    </div>
  );
}
```

如果他先点击 “+1” 按钮，再点击 log 按钮，控制台就会在 3s 后输出 `h1` 内显示的值——即 +1 后的数字。

但是如果他先 log，再点击 “+1”，获得的却还是上一次的数值，并不是 `h1` 显示的值。

这是为什么？

**因为 setState 不是改变了 state 的值，而是有了一个新的 state。**

React 重新执行了组件函数，将原来的值进行处理之后赋给了新的值——所以**原来的值永远是原来的值**。

换个角度说，假设你有一个函数如下：

```js
function foo() {
  const a = 1;
}
```

如果 foo 执行了两次，那么这两次创建的 a 会是一个 a 吗？当然不是呀。

此时在该函数里放一个闭包：

```js
function foo() {
  const a = 1;

  setTimeout(() => consol.log(a), 1000);
}
```

第二次执行的 setTimeout 可能访问到第一次执行时创建的 a 变量吗？

因此，`setTimeout` 是在旧的函数里触发的，3s 后读取的就是旧的函数里的 `n` 值。

（完）
