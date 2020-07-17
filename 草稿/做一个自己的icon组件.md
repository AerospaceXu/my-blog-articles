# 封装一个自己的 icon 组件

本文写于 2020 年 7 月 8 日

正常情况下你会如何封装自己的 icon？

是不是这样：

```html
<div>
  <img src="/long/path/to/your/svg.svg" />
  <span>首页</span>
</div>
```

但是**我们与重复不共戴天**！

如果能做成这样：

```html
<Icon icon-name="home" />
```

如果我们只需要把 icon 名字输入，连路径都不是必须的。岂不美哉？

这就是我们比较新的一种引入 icon 的方式，**svg symbols**。

首先下载 `svg-sprite-loader`。
