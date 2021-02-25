# 一次 HTTP 请求就需要一次 TCP 连接吗？

本文写于 2021 年 2 月 9 日

太长不看版本：**短连接需要，长连接不需要。**

- [一次 HTTP 请求就需要一次 TCP 连接吗？](#一次-http-请求就需要一次-tcp-连接吗)
  - [TCP 的连接与断开](#tcp-的连接与断开)
  - [可以一次性发送多个 HTTP 请求吗？](#可以一次性发送多个-http-请求吗)
  - [浏览器对同一 host 的 TCP 连接上限](#浏览器对同一-host-的-tcp-连接上限)

## TCP 的连接与断开

现代浏览器在与服务器建立了一个 TCP 连接后是否会在一个 HTTP 请求完成后断开呢？

如果会，那什么情况下会断开？

在 HTTP/0.9 版本中，HTTP 请求是以短连接进行的，因此在发送完 HTTP 的响应之后，服务器就会断开 TCP 连接。

可是这样是一件很耗资源、很耗时间的事情，所以在 1.0 版本中，新增了 keep-alive 字段，让长连接被 HTTP 支持了（此时默认还是不会开启长连接）。

所谓长连接，就是完成这个 HTTP 请求之后，**不要断开 HTTP 请求使用的 TCP 连接**。好处是连接可以被重新使用，之后发送 HTTP 请求的时候就不需要重新建立 TCP 连接了，以及如果维持连接，那么 SSL 的开销也可以避免。

好处如此之多，所以 HTTP/1.1 就把 `Connection: keep-alive` 头写进了标准，并且默认开启持久连接。

你必须在请求中声明：`Connection: close` 才会让每次 HTTP 请求都重新建立 TCP 连接。

因此我们有了答案：

1. 如果是「短连接」，那么一次 TCP 连接就只能对应一次 HTTP 请求；
2. 如果是「长连接」，那么一次 TCP 连接就可以发送多个 HTTP 请求了。

## 可以一次性发送多个 HTTP 请求吗？

在 HTTP/1.1 协议中存在一个问题：**单个 TCP 连接在一个时刻只能处理一个请求。**

也就是说你一个请求处理完了才能处理下一个请求。

这就要看看 `Pipelining` 了，所谓 pipe 就是管道，而 `Pipelining` 是 HTTP/1.1 规范中的字段，意为 HTTP 流水线（英语：HTTP pipelining）。

**它能将多个 HTTP 请求整批提交，在发送过程中不需先等待服务器的回应。**

但唯一的问题在于，这个东西在浏览器中是默认关闭的。

因为该技术存在很多问题：

- 一些代理服务器不能正确的处理 HTTP Pipelining；
- 正确的流水线实现是复杂的；
- Head-of-line Blocking 连接头阻塞：在建立起一个 TCP 连接之后，假设客户端在这个连接连续向服务器发送了几个请求。按照标准，服务器应该按照收到请求的顺序返回结果，假设服务器在处理首个请求时花费了大量时间，那么后面所有的请求都需要等着首个请求结束才能响应。

但是，HTTP2 提供了 `Multiplexing` 多路传输特性，让我们可以在一个 TCP 连接中同时完成多个 HTTP 请求。

所以，在 HTTP/1.1 时代，浏览器提高页面加载效率的方法主要有下面两种：

- 维持和服务器已经建立的 TCP 连接，在同一连接上顺序处理多个请求；
- 和服务器建立多个 TCP 连接。

## 浏览器对同一 host 的 TCP 连接上限

假设我们还处在 HTTP/1.1 时代，那个时候没有多路传输。

当浏览器拿到一个有几十张图片的网页该怎么办呢？

肯定不能只开一个 TCP 连接顺序下载，那样用户肯定等的很难受。但是如果每个图片都开一个 TCP 连接发 HTTP 请求，那电脑或者服务器都可能受不了——要是有 1000 张图片的话总不能开 1000 个 TCP 连接吧。

所以浏览器允许我们对同一 host 开启多个 TCP 连接，每个浏览器的数量是不一样的。Chrome 最多允许对同一个 Host 建立六个 TCP 连接。

如果图片都是 HTTPS 连接并且在同一个域名下，那么浏览器在 SSL 握手之后会和服务器商量能不能用 HTTP2，如果能的话就使用 Multiplexing 功能在这个连接上进行多路传输。

不过也未必会所有挂在这个域名的资源都会使用一个 TCP 连接去获取，但是可以确定的是 Multiplexing 很可能会被用到。

如果发现用不了 HTTP2 呢？或者用不了 HTTPS（现实中的 HTTP2 都是在 HTTPS 上实现的，所以也就是只能使用 HTTP/1.1）呢？

那浏览器就会在一个 HOST 上建立多个 TCP 连接，连接数量的最大限制取决于浏览器设置。这些连接会在空闲的时候被浏览器用来发送新的请求。

如果所有的连接都正在发送请求呢？那其他的请求就只能等等了。

参考：

[https://w.cnblogs.com/williamjie/p/11075565.html](https://w.cnblogs.com/williamjie/p/11075565.html)
[https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Connection_management_in_HTTP_1.x](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Connection_management_in_HTTP_1.x)
[https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html)

（完）
