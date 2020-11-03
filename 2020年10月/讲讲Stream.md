# 讲讲 Stream

本文写于 2020 年 11 月 3 日

提到 stream 你会想到什么？流动？小溪？还是媒体？流媒体？

## Live Stream 与直播

如果有和讲英文的网友有过交流的话，应该会发现他们将直播叫做“Live Stream”。

因为自从进入互联网互联网时代之后 Stream 一词对于广大网友来说，其主要含义就从“溪流”变为了 **“播放”**。

所谓直播，即为无需下载、在线播放、边上传边播放……因此叫做“Live Stream”，指其好像溪流一样，延绵不断。同时这种技术也被称作 Streaming。

## 编程语言中的 Stream

Stream 是一个抽象接口，编程语言通常都会有很多对象实现这个接口。

比如，我们可以用它来读/写文件。

```js
const fs = require('fs');

const readerStream = fs.createReadStream('./README.md');
readerStream.setEncoding('UTF8');

readerStream.on('data', function (chunk) {
  data += chunk;
});

readerStream.on('end', function () {
  console.log(data);
});

readerStream.on('error', function (err) {
  console.log(err.stack);
});

console.log('程序执行完毕');
```

这就是一个 Node.js 的 Stream 读取文件的示例代码。

和直接读取文件的不同在于：Stream 像是一个水龙头，他可以控制文件的流速，让程序以较小的内存读写较大的文件。

同样一个文件，如果直接读取可能需要 300M 内存，使用 Stream 就仅仅只需要 20 或 30M。

（完）
