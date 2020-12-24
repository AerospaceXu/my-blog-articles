# 用 Node.js 实现一个简单的 webpack

## 模块化

模块化是一个几乎必备的概念，但是如果一门语言没有模块化规范，我们如何自己做出模块化呢？

这就是 webpack 早期解决的问题。

我们先不去想如何对代码进行编译，先去想想编译的结果应该是什么？

### 如何创造不存在的函数与变量？

```js
const counter = require('./counter.js');
const count = counter();
exports.count = count;
```

这是一段常见的 js 程序，导入导出看起来和 Node.js 的 common js。

但是鉴于我们要自己实现 webpack，require、exports 并不是直接使用 node 提供给我们的，所以要自己解决这个问题。

最简单的思路就是在**处理该文件的时候将所有内容包裹进入一个函数**：

```js
function foo(require, exports) {
  const counter = require('./counter.js');
  const count = counter();
  exports.count = count;
}
```

传入一个 `require` 函数，和一个 `exports` 对象，就可以让函数中的代码运行啦。_因为对象是引用值，所以我们在执行 `foo()` 之后将传入的对象修改后，原始对象也会被修改。_

### 多文件

但是在打包的时候，我们绝大多数的情况不可能是一个文件（如果只有一个文件也没有打包的必要了），文件数量一旦不固定，我们怎么给函数起名字呢？

所以将他们合并为一个对象不失为一个好选择：

```js
const modules = {
  123: function (require, exports) {},
  999: function (require, exports) {
    const { creator } = require('path/to/file');
    creator();
  },
  // ......
};
```

然后我们再定义一个方法作为执行函数：

```js
const exec = function (id) {
  const fn = modules[id];
  const exports = {};
  const require = function (path) {
    // return 文件路径的处理结果
  };
  fn(require, exports);
  return exports;
};
```

这样在每次需要的时候，就可以使用 `exec(id)` 了。

此时我们想一想，执行 `exec(999)` 之后会发生什么？

1. 找到 modules 的 `999` 属性，将函数取出来；
2. 定一个空对象叫做 `exports`；
3. 定义一个 `require` 函数，函数会返回 path 的处理结果；
4. 执行函数，传入 `require` 和 `exports`。

这样一来，我们很容易去执行任何一个文件所包裹而成的函数（简称：文件函数）了。

对于 webpack 来说，我们第一个执行的就是 `entry` 入口文件函数。

### require 如何处理路径

我们的 `exec` 函数处理的只是一个 id，我们根据 id 直接去 `modules` 对象中拿去文件函数即可。

但是 `require` 函数接受的是一个路径呀，这我们怎么处理呢？

第一种方法：

可以将 `modules` 进行一番改造嘛。把 `modules` 的每一项变成一个数组，数组的第一项是文件函数，第二项则是存放一个对象，该对象记录了每一个文件路径与 id 的对应。

```js
const modules = {
  333: [
    function () {
      const { counter } = require('./counter.js');
      const { creator } = require('../../../creator.js');
    },
    {
      './counter.js': 543,
      '../../../creator.js': 222,
    },
  ],
};
```

这种方法需要我们在生成 modules 的时候就解析一下文件内容，找到对应的文件路径——**其实这就是将文件路径编码与解码的一个过程**。

我们完全可以将文件路径编码结果作为 `key` 值，在 `require` 的时候也只是再次调用编码函数，得到 `id` 值罢了。

第二种方法：

```js
function encode(filePath) {
  // doSomething...
  return hashId;
}

function require(filePath) {
  const hashId = encode(filePath);
  return exec(hashId);
}

const exec = function (hashId) {
  const fn = modules[hashId];
  const exports = {};
  fn(require, exports);
  return exports;
};
```

**这就是我们希望的编译结果。**最后只需要将 `modules` 和这些函数作为字符串写到一个新的 `.js` 文件里面，就可以直接在浏览器运行了！

而这个过程，就是我们要实现的微型 webpack。
