# 最强大的 CSS 布局方案 - Grid 布局

Grid 布局是最强大的 CSS 布局方案，他可以轻松的对网页进行栅格化布局。

现在最流行的布局方式当然是 Flex，和 Flex 相比，Grid 布局更为强大。

Flex 使用的是针对轴线的布局，也就意味着他其实是比较一维的布局。（不是说没有二维，而是二维定制性很弱，比如如何做一个像华容道一样的布局？）

华容道那样的布局就是 Grid 的拿手好戏，它将网页分为了行和列，画出了很多单元格后分别制定每个 html 元素所占据的单元格。

## 1. 基本概念

### 1.1 容器与项目

- 网格布局的区域，称之为 `容器(container)`
- 容器内部采用网格定位的字元素，成为 `项目(item)`

```HTML
<div class="grid">
  <div>RED</div>
  <div>GREEN</div>
  <div>
    <p>BLUE</p>
  </div>
</div>
```

这上面的代码中，如果只有外层的 `div` 采用了 `display: grid;` ，那么它就是容器，里面的三个 `div` 就是项目。**但是第三个 `div` 中的 `p` 则不是项目。**

Grid 布局只会对项目生效。

### 1.2 行与列与网格线与单元格

容器中行与列分别叫做 `row` 与 `column`，英文直译。

其实我们可以把 Grid 布局想像成网格。`row` 与 `column` 重叠的区域，就是单元格，N \* M 的网格会产生 MN 列。而包裹这些单元格的就是网格线。

这些概念顾名思义，不多解释。

## 2. 容器属性

Grid 布局的属性和 Flex 一样，一类在容器上（容器属性）、一类在项目上（项目属性）。

### 2.1 display

这是肯定需要的，在父元素的 CSS 中写上 `display: grid;` 即可。

而这个 grid 默认是块级元素，但我们也可以改成行内元素：`display: inline-grid;`。

**重要：**

> 注意，设为网格布局以后，容器子元素（项目）的 `float`, `display: inline-block`, `display: table-cell`, `vertical-align` 等设置都将失效。

### 2.2 gird-template-columns 与 gird-template-rows

其实看名字就能理解啦，容器指定了 grid 布局之后就要划分行和列了。

`gird-template-columns` 和 `gird-template-rows`分别可以定义列的宽度和行的高度。

比如：

```CSS
.container {
  display: grid;
  grid-template-columns: 100px 50% 100px;
  grid-template-rows: 30% 100px 100px;
}
```

这就规定了一个 3\*3 的网格，并且从左往右、从上往下指定。

#### repeat()

如果你每一行每一列的数值都是一样的，那就可以使用 `repeat()`。

repeat()接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。

```CSS
.container {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-template-rows: repeat(3, 33.33%);
}
```

不仅如此，`repeat()` 甚至可以重复某种模式！

`grid-template-columns: repeat(2, 100px 20px 80px);`

#### auto-fill

有时候单元格大小其实固定了，但是容器大小完全不知道——比如我们父元素的宽度不知道，我们该怎么写 `repeat(n, 100px)` 里的 n 呢？

这个时候就可以使用 `auto-fill` 来代替 n，`grid-template-columns: repeat(auto-fill, 100px);`。

它表示每列宽度 100px，并且自动填充，**直到容器不能放置更多的列**。

#### fr

那我不想固定宽度，我只想固定比例怎么办呢？

利用 `fr` 关键字。`grid-template-columns: 1fr 2fr;` 就可以将后一列的宽度设置为前一列的两倍。

`fr` 还能和 `px` 混合使用：

```CSS
.container {
  display: grid;
  grid-template-columns: 150px 1fr 2fr;
}
```

这样以来，他就会固定最左列 150px，剩下的部分会被 1:2 的瓜分。

#### minmax()

`minmax()` 函数会提供一个长度范围，表示该长度就在这个范围中。他可以接受两个参数，分别作为最大值和最小值。

`grid-template-columns: 1fr 1fr minmax(100px, 1fr);`

没错，这个也可以混用 `px` 与 `fr`。

这句话表示：不小于 100px，也不大于 1fr。

#### auto

`auto` 关键字表示由浏览器自己决定长度。

`grid-template-columns: 100px auto 100px;`

该行代码使得第二列的宽度等于该列单元格能达到的最大宽度（除非单元格内容设置了 `min-width`），且这个值大于最大宽度。

#### 起个名字

`grid-template-columns` 属性和 `grid-template-rows` 属性里面还可以使用方括号指定每一根网格线的名字，方便以后的引用。

```CSS
.container {
  display: grid;
  grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
  grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
}
```

上面代码指定网格布局为 3 x 3，因此有 4 根垂直网格线和 4 根水平网格线。方括号里面依次是这八根线的名字。

**网格布局允许同一根线有多个名字**，比如[fifth-line row-5]。

### 2.3 grid-row-gap / grid-column-gap / grid-gap

gap 在英文里面就是空白的意思。这些属性也就顾名思义，用来设置行（列）的间距。

```CSS
.container {
  grid-row-gap: 20px;
  grid-column-gap: 40px;
}
```

这段代码就可以设置行间距为 20px；列间距为 40px。

**xx-gap 是不算外边距的，也就是说如果有三行，只有两个间距而不是四个。**

`grid-gap` 属性是 `grid-column-gap` 和 `grid-row-gap` 的合并简写形式，语法如下：

`grid-gap: <grid-row-gap> <grid-column-gap>;`

和 `padding`, `margin`一样，如果忽略第二个值
