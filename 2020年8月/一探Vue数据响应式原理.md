# 一探 Vue 数据响应式原理

本文写于 2020 年 8 月 5 日

相信在很多新人第一次使用 Vue 这种框架的时候，就会被其修改数据便自动更新视图的操作所震撼。

Vue 的文档中也这么写道：

> Vue 最独特的特性之一，是其非侵入性的响应式系统。数据模型仅仅是普通的 JavaScript 对象。而当你修改它们时，视图会进行更新。

单看这句话，像我这种菜鸟程序员必然是看不懂的。我只知道，在 `new Vue()` 时传入的 `data` 属性一旦产生变化，那么在视图里的变量也会随之而变。

但这个变化是如何实现的呢？接下来让我们，一探究竟。

## 1 偷偷变化的 data

我们先来新建一个变量：`let data = { msg: 'hello world' }`。

接着我们将这个 data 传给 Vue 的 data：

```JavaScript
let data = { msg: 'hello world' }

/*****留空处*****/

new Vue({
  data,
  methods: {
    showData() {
      console.log(data)
    }
  }
})
```

这看似是非常平常的操作，但是我们在触发 showData 的时候，会发现打出来 data 不太对劲：

```JavaScript
msg: (...)
__ob__: Observer {value: {…}, dep: Dep, vmCount: 1}
get msg: ƒ reactiveGetter()
set msg: ƒ reactiveSetter(newVal)
__proto__: Object
```

它不仅多了很多没见过的属性，还把里面的 `msg: hello world` 变成了 `msg: (...)`。

接下来我们尝试在留空处打印出 data，即在定义完 data 之后立即将其打印。

但是很不幸，打印出来依然是上面这个不对劲的值。

可是很明显，当我们不去 `new Vue()`，并且传入 data 的时候，data 的打印结果绝对不是这样。

所以我们可以尝试利用 `setTimeout()` 将 `new Vue()` 延迟 3 秒执行。

这个时候我们就会惊讶的发现：

1. 当我们在 3s 内点开 console 的结果时，data 是普通的形式；
2. 当我们在 3s 后点开 console 的结果时，data 又变成了奇怪的形式。

**这说明就是 `new Vue()` 的过程中，Vue 偷偷的对 data 进行了修改！正是这个修改，让 data 的数据，变成了响应式数据。**

## 2 `(...)` 的由来

为什么好好的一个 `msg` 属性会变成 `(...)` 呢？

这就涉及到了 ES6 中的 getter 和 setter。（如果理解 getter/setter，可跳至下一节）

一般我们如果需要计算后的值，会定义一个函数，例如：

```javascript
const obj = {
  number: 5,
  double() {
    return this.number * 2;
  }
};
```

在使用的时候，我们写上 `obj.double(obj.number)` 即可。

但是函数是需要加括号的，我太懒了，以至于括号都不想要了。

于是就有了 getter 方法：

```JavaScript
const obj = {
  number: 5,
  get double() {
    return this.number * 2;
  }
};

const newNumber = obj.double;
```

这样一来，就能够不需要括号，就可以得到 return 的值。

setter 同理：

```JavaScript
const obj = {
  number: 5,
  set double(value) {
    if(this.number * 2 != value;)
    this.number = value;
  }
};

obj.double = obj.number * 2;
```

由此我们可以看出：通过 setter，我们可以达到**给赋值设限**的效果，例如这里我就要求新值必须是原值的两倍才可以。

但经常的，我们会用 getter/setter 来**隐藏一个变量**。

比如：

```JavaScript
const obj = {
  _number: 5,
  get number() {
    return this._number;
  },
  set number(value) {
    this._number = value;
  }
};
```

这个时候我们打印出 obj，就会惊讶的发现 `(...)` 出现了：

```javascript
number: (...)
_number: 5
```

现在我们明白了，Vue 偷偷做的事情，就是把 data 里面的数据全变成了 getter/setter。

## 3 利用 `Object.defineProperty()` 实现代理

这个时候我们想一个问题，原来我们可以通过 `obj.c = 'c';` 来定义 c 的值——即使 c 本身不在 obj 中。

但如何定义一个 getter/setter 呢？答：使用 `Object.defineProperty()`。

`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

例如我们上面写的 `obj.c = 'c';`，就可以通过

```JavaScript
const obj = {
  a: 'a',
  b: 'b'
}
Object.defineProperty(obj, 'c', {
  value: 'c'
})
```

`Object.defineProperty()` 接收三个参数：第一个是要定义属性的对象；第二个是要定义或修改的属性的名称或 Symbol；第三个则是要定义或修改的属性描述符。

在第三个参数中，可以接收多个属性，`value` 代表「值」，除此之外还有 `configurable`, `enumerable`, `writable`, `get`, `set` 一共六个属性。

这里我们只看 `get` 与 `set`。

之前我们说了，通过 getter/setter 我们可以把不想让别人直接操作的数据“藏起来”。

可是本质上，我们只是在前面加了一个 `_` 而已，直接访问是可以绕过我们的 `getter/setter` 的！

那么我们怎么办呢？

**利用代理。**这个代理不是 ES6 新增的 `Proxy`，而是设计模式的一种。

我们刚刚为什么可以去修改我们“藏起来”的属性值？

因为我们知道它的名字呀！如果我**不给他名字**，自然别人就不可能修改了。

例如我们写一个函数，然后把数据传进去：

```JavaScript
proxy({ a: 'a' })
```

这样一来我们的 `{ a: 'a' }` 就根本没有名字了，无从改起！

接下来我们在定义 `proxy` 函数时，可以**新建一个空对象**，然后遍历传入的值，分别进行 `Object.defineProperty()` 以**将传入的对象的 keys 作为 getter/setter 赋给新建的空对象**。

最后，我们 `return` 这个对象即可。

```JavaScript
let data = proxy({
  a: 'a',
  b: 'b'
});

function proxy(data) {
  const obj = {};
  const keys = Object.keys(data);
  for (let i = 0; i < keys.length; i++) {
    Object.defineProperty(obj, keys[i], {
      get() {
        return data[keys[i]];
      },
      set(value) {
        if (value < 0) return;
        data[keys[i]] = value;
      }
    });
  }
  return obj;
}
```

这样一来，我们一开始声明的 `data`，就是我们 `return` 的对象了。在这个对象里，没有原始的数据，别人无法绕过 getter/setter 进行操作！

**但是往往并没有这么简单，如果我一定需要一个变量名呢？**

```JavaScript
const sourceData = {
  a: 'a',
  b: 'b'
};

let data = proxy(sourceData);
```

如此一来，通过直接操作 `sourceData.a`，时可以直接绕过我们在 `proxy` 中设置的 `set a` 进行赋值的。这个时候我们怎么处理？

很简单嘛，当我们遍历传入的数据时，我们可以对传入的数据新增 getter/setter，此后**原始的数据就会被 getter/setter 所替代**。

在刚刚的代码中，我们在循环的刚开始添加这样一段代码：

```JavaScript
for(/*......*/) {
  const value = data[keys[i]];
  Object.defineProperty(data, keys[i], {
    get() {
      return value;
    },
    set(newValue) {
      if (newValue < 0) return;
      value = newValue;
    }
  });
  /*......*/
}
```

这是什么意思呢？

我们**利用了闭包，将原始值单独拎出来**，每一次对原始属性进行读写，其实都是 get 和 set 在读取闭包时被拎出来的值。

那么不管别人是操作我们的 `let data = proxy(sourceData);` 的 data，还是操作 sourceData，都会被我们的 getter/setter 所拦截。

## 4 回到 Vue

我们刚刚写的代码是这样的：

```JavaScript
let data = proxy({
  a: 'a'
})

function proxy(data) {

}
```

那如果我改成这样呢：

```JavaScript
let data = proxy({
  data: {
    a: 'a'
  }
})

function proxy({ data }) {
  // 结构赋值
}
```

是不是和 Vue 就非常非常像了！

`const vm = new Vue({ data: {} })` 也是让 `vm` 成为 `data` 的代理，并且就算你从外部将数据传给 data，也会被 Vue 所捕捉。

而在每一次捕获到你操作数据之后，就会对需要改变的 UI 进行重新渲染。

同理，Vue 对 computed 和 watch 也存在着各种偷偷的处理。
