<!--
 * @Author: your name
 * @Date: 2020-05-22 20:35:22
 * @LastEditTime: 2020-05-22 21:54:34
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /undefined/Users/xhy666/DOM事件与事件委托.md
-->

# DOM 事件与事件委托

## 01 点击事件

```HTML
<div class='grandpa'>
  <div class='father'>
    <div class='son'></div>
  </div>
</div>
```

现在分别给他们仨添加不同的三个事件监听。

因为**事件冒泡**，我们能知道他们**都会执行**，并且会按照一定顺序执行。

但是不同浏览器的**顺序是不一样的**。

IE 认为应该调用 `son` 的事件，网景认为应该调用 `grandpa`。

后来 2002 年，W3C 发布了标准：

- 先按照从外向内——事件捕获；
- 在按照从内向外——事件冒泡。

整个过程就是，有监听函数就调用，没有就跳过。

开发者自己选择把最外层的事件，放在捕获阶段还是冒泡阶段。

**`addEventListener`**

我们经常会用这个函数绑定事件，但是实际上，这个函数有 3 个参数。

我们一般可以在第三个参数，放置一个布尔值：`xxx.addEventListener('click', fn, bool)`

如果是`true`，则是捕获方式（从外向内）；如果不写，或者是`falsy`值，则是冒泡（从内向外）。

这里提一句，`e.target`和`e.currentTarget`是有区别的！

`target` 是被操作的元素，`currentTarget` 是被监听的元素，比如 `div` 中的 `span`。

也可以用`this`，this 代表 currentTarget。

**顺序问题**

上面说了，先捕获后冒泡。

那我同时给一个元素，先挂一个冒泡，再挂一个捕获——谁先触发？

谁先写，谁先触发，因为他们是**同级的**！

**可以取消吗？**

捕获不可以取消，但是冒泡可以取消。（有些事件不能取消，比如滚动事件）

`e.stopPropagation()`中断冒泡。

## 事件委托

**我们如何给一百个 button 添加点击事件？**

遍历？

那岂不是要添加一百个监听器？

我们可以直接给父元素添加事件。

利用之前说的`e.target`来获得用户点击的元素。

**JS 不支持事件**

支持浏览器 addEventListener 是浏览器的 DOM 提供的。
