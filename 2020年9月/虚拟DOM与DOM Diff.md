# 虚拟 DOM 与 DOM Diff

本文写于 2020 年 9 月 12 日

虚拟 DOM 在今天已经是前端离不开的东西了，因为他的好处实在是太多了。

在《高性能 JavaScript》一书中，提到过 DOM 操作很慢。但实际上这句话没有任何前提条件，也没有对比谁慢，纯粹属于“话术”。

的确，DOM 操作比 JS 原生 API 是要慢很多的，因为 DOM 操作是**跨线程**的。

**但是并没有我们想象的那么慢。** 从使用上来说，你写网页不可能不操作 DOM，不操作 DOM 就没办法写各种效果了，就算是现在的 Vue/React 不用操作 DOM，但底层也是 Vue/React 帮我们操作了 DOM。

但是 Vue/React 采用的是虚拟 DOM，而不是普通的操作 DOM。**虚拟 DOM 相对于 DOM 而言，效率更好、速度更快。**

## 虚拟 DOM 的优点

### 减少 DOM 操作

刚刚我们说过了，DOM 操作是跨线程的，性能并不好，但写网页又不得不操作 DOM（除非你想写一个 20 年前的网页）。

所以我们首先能想到，如果能减少 DOM 操作的次数，那不就变相的提高了 DOM 操作的性能嘛？

**方法一：虚拟 DOM 可以将多次 DOM 操作合并为一次操作。**

比如你有 1000 个 DOM 节点，通过浏览器提供的 DOM API 可能需要操作 1000 次，但是虚拟 DOM 可以合起来只操作 1 次。

**方法二：DOM Diff 算法可以摒除多余的 DOM 操作**

DOM Diff 算法，顾名思义就是看对比前后两次 DOM 树的区别，然后只去更新有变化的 DOM 节点。

_经测试，在 8 代 i7+16g 内存的 MacBookPro 上，虚拟 DOM 写入 1000 个 DOM 节点需要 1.58ms；DOM API 写入耗时 18.55ms_

_虚拟 DOM 写入 10,000 个 DOM 节点需要 16.34ms；DOM API 写入耗时 512.47ms_

_虚拟 DOM 写入 50,000 个 DOM 节点需要 128.97ms；DOM API 写入耗时 1874.72ms_

**_但一旦 DOM 节点数非常高，DOM API 反而会快于虚拟 DOM，因为虚拟 DOM 自身存在大量计算_**

### 你的虚拟 DOM，不止是 DOM

虚拟 DOM 并不是 DOM，它只是一个 JS 对象罢了。

**那这么一来他就不仅仅只是 DOM 了，我们还可以用它在安卓、iOS 各个平台上构建界面！**

## 虚拟 DOM 的“模样”

来看看 React 的虚拟 DOM 是什么样子的：

```JavaScript
const vNode = {
  key: null,
  props: {
    children: [
      {
        // 子元素
        key: ...,
        props: ...,
        children: [...]
        ...
      }
    ],
    className: "",
    onClick: () => {}
  },
  ref: null,
  type: "div",
  ...
}
```

Vue 的虚拟 DOM 和 React 相比大同小异：

```JavaScript
const vNode = {
  tag: "div",
  data: {
    class: "red",
    on: {
      click: () => {}
    }
  },
  children: [
    {.......}
  ],
  ...
}
```

如果你对于 React 和 Vue 比较熟悉的话，相信不用我过多解释他们的含义。

## 如何创建一个虚拟 DOM

在 React 中有一个方法，叫做 `React.createElement()`。

我们可以这么使用，来创建一个虚拟 DOM：

```JavaScript
createElement(
  'div',
  {
    className: 'red',
    onClick: () => {}
  },
  [
    createElement('h1', {}, 'title'),
    createElement('span', {}, 'span')
  ]
)
```

Vue 的方法和 React 差不多，就不写了。

到了这你应该发现了一个问题，**这么写实在是太累了！**没人会这么写代码的。

React 选择了 JSX 来代替上面这种“讨厌”的写法。

```jsx
<div>
  <h1></h1>
  <span></span>
</div>
```

通过 babel，这段 JSX 代码就会变成 createElement 的形式。（babel 团队和 React 团队关系好）

Vue 的 template 语法可以通过 vue-loader 转化为自己的虚拟 DOM 创建方法。

## 虚拟 DOM 总结

总而言之，虚拟 DOM 就是一个抽象 DOM 树的对象，通常包含了「标签名」、「标签属性」、「事件监听」、「子元素」……

它可以减少不必要的 DOM 操作，还可以跨平台渲染。

但是虚拟 DOM 也有缺点，那就是他必须通过工厂方法来创建。但这个问题可以通过 JSX、template 等写法加上编译插件来解决。

## DOM Diff

虚拟 DOM 会记录现在的 DOM 树，和即将更新的 DOM 树。所以从算法角度来说，DOM Diff 就是对比两棵「树」的不同（包括每个节点的内部属性）。

逐层对比两棵树之后，该算法会找出需要更新的节点。如果节点是组件就看 Component Diff，如果是标签就看 Element Diff。

**所谓 Component Diff，就是针对组件的 Diff 算法。**

先看组件类型，如果组件类型不同就直接替换；如果类型相同就更新属性，之后逐层递归。

**所谓 Element Diff，就是针对标签的 Diff 算法**

标签名不一样直接替换，如果一样就替换属性，进而进行递归深入。

这里不得不提一句，**为什么 Vue 和 React 做列表渲染的时候都希望你添加一个 key 属性呢？**

我们简单看两个数组：`[1, 2, 3]` 和 `[1, 3]` 。

我们是通过删除了 2，得到的后面的一个数组。可是 DOM Diff 算法会认为，这是把 2 修改成了 3，然后把最后一个 3 删了得到的。

（完）
