# gRPC 入门

gRPC 是一个高性能、开源和通用的 RPC 框架。

是面向移动和 HTTP/2 设计的，提供了诸如双向流、流控、头部压缩、单 TCP 连接上的多复用、请求等特等特性。

这些特性让 gRPC 在移动设备上表现非常好，**更省电和节省空间占用**。

## 简单说说

gRPC 采用 HTTP/2 协议做为传输协议，proto buffer 作为编码协议。

pb 通过 `message` 关键字声明消息结构体，通过 `service` 关键字声明服务。

```proto
message Xxxxx {
  int32 latitude = 1;
  int32 longitude = 2;
}

service Yyyyy {
  rpc xxx(bbb) returns (aaa) {}
}
```

## gRPC 的四种方法

官方文档是这么说的：

> gRPC lets you define four kinds of service method.

刚开始这个看的我是很懵逼的
