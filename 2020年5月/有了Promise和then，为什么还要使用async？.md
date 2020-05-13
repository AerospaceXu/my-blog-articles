# 有了 Promise 和 then，为什么还要使用 async？

本文写于 2020 年 5 月 13 日

最近代码写着写着，我突然意识到一个问题——我们既然已经有了 Promise 和 then，为啥还需要 async 和 await？

这不是脱裤子放屁吗？

比如说我们需要一段请求服务器的代码：

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => {
    const res = '明月几时有'
    if (1 > 2) {
      resolve(res)
    } else {
      reject('我不知道，把酒问青天吧')
    }
  }, 1500)
}).then(
  (res) => {
    console.log(`成功啦！结果是${res}`)
  },
  (err) => {
    console.log(`失败了。。。错误是${err}`)
  }
)
```

如果看不懂，可以看我之前写的一篇，[什么叫做 Promise？](https://aerospacexu.github.io/2020/03/08/%E4%BB%80%E4%B9%88%E5%8F%AB%E5%81%9APromise%EF%BC%9F/)

这段代码，简洁漂亮，但是如果用上了 async 和 await，就需要写成下面这样：

```javascript
function ask() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const res = '明月几时有'
      if (1 > 2) {
        resolve(res)
      } else {
        reject('我不知道，把酒问青天吧')
      }
    }, 1500)
  })
}

async function test() {
  try {
    const res = await ask()
  } catch (err) {
    console.log(err)
  }
}

test()
```

竟然还需要一个 try catch 来捕捉错误！越写越像 Java 呀。

MDN 给我们了一些解释：

> 如果你在代码中使用了异步函数，就会发现它的语法和结构会更像是标准的同步函数。

说白了，这种写法的一部分原因，就是为了“讨好” Java 和其他的一些程序员。

另一方面呢，也是增强可读性，虽然说 async await 的写法**比较丑**，但是毫无疑问，可读性远远高于 Promise then。

最为重要的呢，是 Promise 可以无限嵌套，而 async await 只能处理一个 Promise，无法继续嵌套。

所以一旦需要使用多次连续回调，async await 就乏力了。

其实也可以，通过 await 一个`Promise.all()`来实现。

（完）
