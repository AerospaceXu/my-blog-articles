# 浅析 JS 中的 Proxy

本文写于 2020 年 10 月 6 日

Proxy 是代理的意思。

ES6 新增了代理操作。所谓代理操作，就是能让我们用自己的东西，代替原本的东西去执行一些操作。这就是“元编程”的概念。

简单来说呢，就是语言提供给我们了一些操作，可以改变语言的默认行为——去编程编程语言。

例如我们的读取操作：`console.log(a);`，通过元编程我们可以让 a 不管等于多少，都会读取到 3。

```JavaScript
// 不要试图理解下面的代码，就当英文读就好了
const target = {
  a: 1
};

const handler = {
  get() {
    return 3;
  }
};

const proxy = new Proxy(target, handler);

console.log(proxy.a); // 3
```

好吧，我知道这样操作非常的无聊，但这也何尝不是一种很好理解的方式呢？我们**代理了「读取」这个操作**，让所有的读取操作都要使用我们**自定义**的 `get` 方法，从而实现了劫持数据。

## 创建代理

接下来进入语法层面。

### 最简单的代理就是空代理

最简单的代理就是空代理，也就是我们虽然代理它，但啥也不干。

代理的创建需要使用 `Proxy` 构造函数：`const p = new Proxy(target, handler);`。

其中 target 参数代表目标对象，handler 参数代表处理对象。缺少任何一个参数都会报错，如果需要创建空代理，要做的就是将空对象作为 handler 参数传入。

```JavaScript
const target = {
  a: 1,
};

const p = new Proxy(target, {});
```

（完）
