# MVC 与 Vue

本文写于 2020 年 7 月 27 日

Vue 是这五六年来最流行的前端框架之一，但是很多前端学习者并不具备良好的计算机基础知识（比如我这种设计出身的），对于框架他们的态度经常是会用就行。

但是流行技术的变化太快了，每个月都有新的变化，所以很多人都会抱怨：“啊！好多要学的呀！AWSL！”。可是那些计算机大神们，是如何学习这么快的呢？

那是因为他们理解了编程和计算机的核心思想，而流行的技术无非是这些思想的变化罢了。因此在我们的学习中，只有不断的从不断变化的框架中提炼出不变的思想，才能让我们免于疲于奔命式的学习，甚至于“学习至死”。

MVC 就是一个非常经典的设计模式。一个 MVC 模块是三个对象的合体：M, V, C：

- M，即为 Model，代表数据；
- V，即为 View，代表视图；
- C，即为 Controller，代表控制（业务逻辑）。

看到这里，涉及到视图层，就应该明白这个设计模式对于前端的意义有多么重大。

严格来说……MCV 没有严格来说，它的定义并不明确，可以说 MVC 代表的就是视图和业务逻辑互不干扰的代码结构。

首先说说一个经常争论的问题：Vue 是 MVC 还是 MVVM 框架？

维基百科告诉我们：MVVM 是 PM 的变种，而 PM 又是 MVC 的变种。所以一定程度上来说，不管 Vue 是 MVC 还是 MVVM 或者都不是，它的思想方向与这些设计模式的方向是大体相同的。

并且 Vue 的官网中也说道：“虽然没有完全遵循 MVVM 模型，但是 Vue 的设计也受到了它的启发。”

这个问题网上吵得非常多，各有各的观点，本文并不是来讨论这个问题的，而是面是向初学者浅浅的分析一下老大哥 **MVC 的思想在 Vue 中的体现**。

## 0 新手的困惑

大学时候专业里前后开了几门网页课，先是教授 HTML、CCC；后来一门课教了 JS；最后有一门教授 Vue 的课。

由于我大学读的并不是计算机专业，而是艺术类的数字媒体艺术专业。所以大家对于编程的热情度几乎是负的。上学期的 JS 都没学好，一听说要学 Vue，大家的内心自然是崩溃的。课程上来就是一段代码：

```javascript
let app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
});
```

大家一开始的心声就是这样的：什么？！这是什么？谁看得懂！

并且不光是初学者，就算是一些写了一段时间 Vue 的人，懂得 el 是是什么、data 是什么，但可能也不清楚**为什么 Vue 要这么来组织代码——除非他学过 MVC**。

## 1 一个简单的 MVC 计数器

还是那句话，放码过来。我们先实现一个非常常见的例子：加按钮与减按钮。

### 普通版本 JS 计数器

```html
<div id="app">
  <span>0</span>
  <button id="add">+</button>
  <button id="minus">-</button>
</div>
```

在当前的 HTML 代码下，我们希望的结果是：当我们点击 + 号时，`<span>` 中的数字就会 +1，点击 - 号时，同理就会 -1。

这种 JS 代码想必应该是信手拈来的，对吧。

```javascript
const numberWrapper = document.querySelector('#app span');
const addBtn = document.querySelector('#add');
const minusBtn = document.querySelector('#minus');

addBtn.addEventListener('click', () => {
  const newNumber = parseInt(numberWrapper.innerText) + 1;
  numberWrapper.innerText = newNumber.toString();
});

minusBtn.addEventListener('click', () => {
  const newNumber = parseInt(numberWrapper.innerText) - 1;
  numberWrapper.innerText = newNumber.toString();
});
```

但这只是初学者都会写的普通版，接下来让我们用 MVC 的方式来一步步的重构这个代码。

### MVC 版本 JS 计数器

首先我们想，这样写的一个计数器，如果需要修改，那我一方面要改 HTML 文件、一方面还要修改 JS 文件，何其麻烦！

写到一起来吧：

```javascript
const app = document.querySelector('#app');

const html = `
  <span>0</span>
  <button id="add">+</button>
  <button id="minus">-</button>
`;
const counter = document.createElement('div');
counter.innerHTML = html;
app.appendChild(counter);
```

那么我们来梳理一下现在的代码：

1. 首先我们需要创建 HTML 元素；
2. 然后通过 CSS 选择器找到对应的 DOM 元素；
3. 再对他们添加各种监听事件与操作。

那我们可以大胆的猜测一下嘛，如何使用 MVC 思想呢？

首先新建一个对象叫做 view 吧，再将我们的 html 代码放进去：

```javascript
const view = {
  html: `
    <span>0</span>
    <button id="add">+</button>
    <button id="minus">-</button>
  `
};
```

还有我们用来新建 div、将 html 代码放入 div、再将 div 放进 app 的操作，应该也是属于视图层。

所以我们给 view 对象添加一个 render 方法：

```javascript
const view = {
  // ...html...
  render() {
    const counter = document.createElement('div');
    counter.innerHTML = view.html;
    app.appendChild(counter);
  }
};

view.render();
```

这样我们就搞定了 V，然后看看 C。除了视图和数据，其他的东西应该都属于 C，所以 DOM 元素的获取放在 C 里、事件绑定也放在 C 里。

```javascript
const controller = {
  ui: {},
  bindEvents() {}
};
```

这里我们准备将 DOM 元素放在 ui 对象里，但是这里需要脑子转一下。

一旦我们在这里写了 `querySelector`，那么必然是找不到元素的，因为我们还没有 render，根本没有那些按钮和数字。

所以我们得在里面写一个 init 函数，这样我们执行初始化之后，他就会先去获取 DOM、再去绑定事件：

```javascript
init() {
  this.ui = {
    numberWrapper: document.querySelector('#app span'),
    addBtn: document.querySelector('#add'),
    minusBtn: document.querySelector('#minus')
  };
  controller.bindEvents();
},
```

绑定事件的写法就非常简单了：

```javascript
bindEvents() {
  controller.ui.addBtn.addEventListener('click', () => {
    const newNumber = parseInt(controller.ui.numberWrapper.innerText) + 1;
    controller.ui.numberWrapper.innerText = newNumber.toString();
  });
  controller.ui.minusBtn.addEventListener('click', () => {
    const newNumber = parseInt(controller.ui.numberWrapper.innerText) - 1;
    controller.ui.numberWrapper.innerText = newNumber.toString();
  });
}
```

接下来就是一个转折点了，**我们要创建一个 model 对象来保存数据**。

```javascript
const model = {
  data: {
    number: 100
  }
};
```

这个时候不知道大家有没有领悟到一些东西。

**既然已经有了 model，我们何必还去操作 DOM 获取数据呢？**

直接操作 model 多优雅呀！

所以 bindEvents 可以改成这样：

```javascript
controller.ui.addBtn.addEventListener('click', () => {
  model.data.number += 1;
});
controller.ui.minusBtn.addEventListener('click', () => {
  model.data.number -= 1;
});
```

那我们的 view 对象也需要修改，他也应该从 model 中获取数据：

```javascript
const view = {
  html: `
    <span>{{number}}</span>
    ......
  `,
  render() {
    const counter = document.createElement('div');
    counter.innerHTML = view.html.replace('{{number}}', model.data.number);
    app.appendChild(counter);
  }
};
```

但是我们这样操作虽然说修改了数据，可是并没有重新渲染到页面上呀。所以每次提交之后需要重新 render。

此时问题出现了：点击 + 或者 - 后，数字只会变化一次，第二次点击便毫无用处！

这是为什么呢？

很简单，因为我们重新 render，导致俩**绑定了事件的 button 全都不是曾经的那个他了**。

所以我们使用事件代理来解决这个问题——将事件绑定在外层的 div 上，然后判断点击对象的 id 即可。

写法如下：

```javascript
const compute = e => {
  switch (e.target.id) {
    case 'add':
      model.data.number += 1;
      break;
    case 'minus':
      model.data.number -= 1;
      break;
    default:
      return;
  }
  view.render();
};
```

接下来我们会在 view 对象中添加一个 el 属性，用来存储我们创建的外层 div。

```javascript
const view = {
  el: null,
  // ......
  render() {
    if (!view.el) {
      // 创建 div，并将 div 赋值给 el
    } else {
      // 将 el 的 innerHTML 更换为新的内容
    }
  }
};
```

最后我们再进行一步优化。

我们本身不应该知道在 render 时，应该 append 给哪一个元素。这个元素应该是别人传给我的，所以应该这么写：

总代码：

**MVC 之 V**

```javascript
const view = {
  el: null,
  html: `
    <span>{{n}}</span>
    <button id="add">+</button>
    <button id="minus">-</button>
  `,
  render(container) {
    if (!view.el) {
      const counter = document.createElement('div');
      view.el = counter;
      counter.innerHTML = view.html.replace(
        '{{n}}',
        model.data.number.toString()
      );
      container.appendChild(counter);
    } else {
      view.el.innerHTML = view.html.replace(
        '{{n}}',
        model.data.number.toString()
      );
    }
  }
};
```

**MVC 之 M**

```javascript
const model = {
  data: {
    number: parseInt(window.localStorage.getItem('number')) || 0
  },
  save() {
    window.localStorage.setItem('number', model.data.number.toString());
  }
};
```

**MVC 之 C**

```javascript
const controller = {
  init(container) {
    controller.ui = {
      container
    };
    view.render(container);
    controller.bindEvents();
  },
  bindEvents() {
    controller.ui.container.addEventListener('click', e => {
      switch (e.target.id) {
        case 'add':
          model.data.number += 1;
          break;
        case 'minus':
          model.data.number -= 1;
          break;
        default:
          return;
      }
      model.save();
      view.render();
    });
  }
};
```

使用方式：

```javascript
const app = document.querySelector('#app');

controller.init(app);
```

这个时候我们的程序已经是一个比较完整的 MVC 模式了，但直接全部 render 非常浪费性能。

所以 React 之类的框架会使用虚拟 DOM 和 diff 算法来只修改变化的 DOM。

总的来说，我们的 MVC 思想可以抽想成为一个公式：`view = render(data)`

**使用 class 来优化代码**

class 优化代码可以提升我们的代码复用程度，铭记：**程序员永远不要重复自己的操作**。

先看看 Model：

```javascript
class Model {
  constructor(options) {
    for (let key in options) {
      this[key] = options[key];
    }
  }

  save() {
    console.error('还未传入save函数');
  }
}

export default Model;
```

这个非常简单，我们想要传入任何的东西，都在这个 option 里面，就像这样：

```javascript
const model = new Model({
  data: {},
  save() {}
});
```

回想一下，我们使用 Vue 的时候，是不是也是如此？

```javascript
export default new Vue({
  data() {
    return {
      msg: 'hello world'
    };
  },
  methods: {}
});
```

我没读过 Vue 的源码，不知道 Vue 是否是按照本文的思路构建代码的。

但是 Vue、React 等框架追根溯源都能找到 MVC 的身上。所以毫无疑问，MVC 的思想是每一个程序员都需要学习的一种设计模式。

初学程序，用了几个好用的框架与工具，不应该只沉迷于其方便的一面，要善于从工具的运用中寻找出其作者留下的蛛丝马迹，反推学习、多查资料，才能够慢慢进化成为不惧怕新技术、框架越来越多的大神程序员！

工具也许会一个月一变、一天一变，但是思维是永恒的。

（完）
