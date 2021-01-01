# GET 与 POST 其实没有什么区别

本文写于 2020 年 12 月 30 日

GET 与 POST 是两种 **HTTP 方法**，并且是最常用的两种。

今天在使用 Postman 测试 api 的时候，突发奇想：在 Get 请求的请求体中写 Body 参数，在 Post 请求中写 Query 参数。

**居然完全可以运行！**

对比起我之前看过的一些文章，所谓的 GET 与 POST 的区别，可以说：网上大部分对二者的分析都是错的，**GET 和 POST 没有本质的区别**。

GET 的 HTTP 报文完全是这样的：

```http
GET /cats?id=1 HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Content-Length: 15

{
    "id": 1
}
```

GET 也可以是这样的：

```http
POST /cats?id=1 HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Content-Length: 15

{
    "id": 1
}
```

发现除了开头的方法不同，别的完全是一模一样的！

如果我们想要知道他们之间的区别，首先我们得明确：什么是 HTTP？什么是 HTTP 方法？

## 什么是 HTTP

**什么是 HTTP 呢？**

HTTP 是一种协议，他的设计目的是保证客户端与服务器之间的通信。

HTTP 的工作方式是客户端与服务器之间的「请求」与「应答」。（这个客户端可能是 web 浏览器，也可能是需要联网的本地应用程序）

但是这个「请求」与「应答」不能瞎来啊，我们互相之间得看得懂才行——这就是**协议**的作用：规定好应该如何请求、应该如何响应。

关于 HTTP 不多做解释，可以看之前一的一篇[用简单 Node.js 后台程序看 HTTP 请求](https://www.yuque.com/fuzaogonglibudushu/javascript/dt31ut)。

## HTTP 方法

而 HTTP 方法呢，顾名思义，就是指在客户端和服务器之间进行「请求-响应」时，被用到的方法。

**GET 和 POST 各用一句话来描述：**

- GET 从指定的资源请求数据
- POST 向指定的资源提交要被处理的数据

## 常见错误答案

常常会有人分析 GET 与 POST 的区别，这里说一下几个广为流传的“错误答案”，甚至有些 W3C 也是这么写的。

### GET 的长度有限制，POST 没有

GET 方法的长度限制是怎么回事？真的有限制吗？

**有限制的其实是 URL**，如果你愿意把参数写到 GET 请求的 Body 里去，那 GET 请求的长度和 POST 就是一样的了。

并且即使是 URL 的长度限制，那也不是 HTTP 协议的锅。**HTTP 协议没有 Body 和 URL 的长度限制，对 URL 限制的大多是浏览器和服务器的原因。**

浏览器原因就不说了，服务器是因为处理长 URL 要消耗比较多的资源，为了性能和安全（防止恶意构造长 URL 来攻击）考虑，会给 URL 长度加限制。

并且 POST 也不是没有限制的，只是比较大而已。

- 通常 GET 请求中的 Query 参数大小是以 k 为单位记录的，根据不同的浏览器和服务器有不同的数据；
- POST 请求的大小是以 M 为单位记录的，同样取决于服务器。

### POST 方法比 GET 方法安全

按照网上大部分文章的解释，POST 比 GET 安全，因为数据在地址栏上不可见。

然而，从传输的角度来说，**他们都是不安全的！！！**

因为 HTTP 在网络上是**明文传输**的，只要在网络节点上抓包，就能**完整地获取**数据报文。

要想安全传输，就只有加密，也就是 HTTPS。

### GET 的参数是固定写法

我们必须把 GET 的参数写在 ? 后面，用 & 分割吗？

根本不用。

因为解析报文的过程是通过获取 TCP 数据，用正则等工具从数据中获取 Header 和 Body，从而提取参数。

也就是说，**我们可以自己约定参数的写法，只要服务端能够解释出来就行**，一种比较流行的写法是 http://www.example.com/user/name/chengqm/age/22。

参考文章：

[W3C HTTP 方法：GET 对比 POST](https://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

[99%的人都理解错了 HTTP 中 GET 与 POST 的区别](https://mp.weixin.qq.com/s?__biz=MzI3NzIzMzg3Mw==&mid=100000054&idx=1&sn=71f6c214f3833d9ca20b9f7dcd9d33e4#rd)

[都 2019 年了，还问 GET 和 POST 的区别](https://blog.fundebug.com/2019/02/22/compare-http-method-get-and-post/)

（完）
