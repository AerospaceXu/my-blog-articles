# 一窥 AJAX

本文写于 2020 年 6 月 7 日

AJAX 这个词非常常见，如果使初学者，说不定还会非常害怕这个名字看起来非常高端、非常难的技术。

AJAX，全称 Async JavaScript And XML。它不是 JavaScript 的规范，它只是一个哥们“发明”的缩写，意思就是**用 JavaScript 执行异步网络请求**。

就算在当年 AJAX 刚刚出现的时候，它不是一个新技术，而是一个新名字。

技术领域其实经常这样——大家都在用某一个方法去写代码，突然有一天有个哥们儿觉得，要不咱给这个技术做个规范、起个名字吧！然后就有了一个新的技术名词，但其实并不是一个新的技术。

那么我们来思考一下：**AJAX 为什么需要存在呢？**

我最早自学前端的时候，听人家说：”JavaScript 有个技术叫做 AJAX 非常牛逼！“我就去学了。

可我那个时候还是一个”小白菜“（很菜的小白），怎么可能看两篇教程就能懂呢？于是我就放弃了。

可是一直到我学了 Vue, React 我都没有遇到过 AJAX 。

AJAX 已死？

那肯定是扯淡。这就是之前说的，**AJAX 不是个什么牛逼的新技术，他只是对已有技术的总结**。我们已经在 coding 中不知不觉用到了类似 AJAX 的代码。

Web 运作的原理就是一次 HTTP 请求对应一个页面。

但如果我非要让用户再不刷新的情况下体验新内容呢？我可不可以用 JS 和服务器后端交换数据，但是并**不去发送 HTTP 请求**，而是**用 JS 将新的数据动态写到 HTML 中**？

这样就完成了不刷新的页面和数据更新。

最早大规模使用 AJAX 的就是大名鼎鼎的 Gmail。Gmail 的页面在首次加载后，剩下的所有数据都依赖于 AJAX 进行更新。

**如何使用 AJAX？**

用 JavaScript 写 AJAX 并不复杂，唯一需要注意的就是 AJAX 是异步执行的，也就是说需要通过回调函数获得响应。

我们可以通过 `XMLHttpRequest` 构造函数来创建 `XMLHttpRequest` 对象来完成 AJAX。

什么？你问我什么是这个 `XML...bla..` 对象？在 JS 中，`Object()` 可以构造对象、`String()` 可以构造字符串、`Array()` 可以构造数组。`XMLHttpRequest` 也只是一个构造某种对象的构造函数罢了。

看代码：

```javascript
function success(text) {
  console.log(`成功啦，${text}`);
}

function fail(code) {
  console.log(`失败啦，${code}`);
}

const request = new XMLHttpRequest();

request.onreadystatechange = function () {
  if (request.readyState === 4) {
    if (request.status === 200) {
      return success(request.responseText);
    } else {
      return fail(request.status);
    }
  }
};

request.open('GET', '/index.html');
request.send();
```

接下来我们来一点点剖析代码。

首先我们看最后，`request.open()` 是规定了请求的方式与 URL 参数；最后一行的 `request.send()` 是真正发出请求；`request.onreadystatechange` 其实是对请求进行监听。

前两个肯定没有问题，问题就在于第三个，监听什么呢？

监听的是当前的状态。

> 官方解答：`XMLHttpRequest.readyState` 属性返回一个 `XMLHttpRequest` 代理当前所处的状态。

完美看不懂。

其实本质上就是说，当前你进行到哪一个阶段了。

如果你 `new XMLHttpRequest()` ，那么这个时候你的 `request.readyState` 就会变成 0，并且 `onreadystatechange` 事件还会被触发，

当我们调用了 `request.open()` 之后， `request.readyState` 就会变成 1，同时触发 `onreadystatechange`。

以此类推。

| 值  | 状态             | 描述                                              |
| --- | ---------------- | ------------------------------------------------- |
| 0   | UNSENT           | 代理被创建，但尚未调用 open() 方法。              |
| 1   | OPENED           | open() 方法已经被调用。                           |
| 2   | HEADERS_RECEIVED | send() 方法已经被调用，并且头部和状态已经可获得。 |
| 3   | LOADING          | 下载中； responseText 属性已经包含部分数据。      |
| 4   | DONE             | 下载操作已完成。                                  |

此时，我们就能看懂这段代码了。

我们首先准备一个请求的包包，然后往包包里塞东西，并且每一个动作都有一个对应的数值、切换状态会自动触发一个事件。

也就是说，我们只需要：

1. 发出请求；
2. 接受请求；
3. 判断请求；
4. 处理请求结果；
5. 请求结束。

就完成了一个完美的 AJAX。
