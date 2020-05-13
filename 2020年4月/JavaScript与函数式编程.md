# JavaScript 与函数式编程

本文写于 2019 年 4 月 19 日

绝大多数编程语言都会有函数的概念（或者说所有的？我不太确定），他们都可以做出类似的操作：

```javascript
function(x) {
  return x * x
}
```

但是 Javascript 更适合函数式编程，因为函数对于 js 来说，是**一等公民**。

我们可以把匿名函数赋值给一个变量，比如：

```javascript
let pow = function (x) {
	return x * x
}
```

然后我们可以将这个函数赋值给另一个变量：

```javascript
let comeon = pow
comeon(x)
```

这样做和直接调用`pow(x)`是一样的效果。

甚至于我们可以将函数作为参数传入另一个函数，这样，诸多小函数可以汇聚在一起，变得异常强大！

## filter()

OK，我们接下来看一些比较基础的例子。

首先是`filter()`，这是我最喜欢的函数之一。`filter()`方法会**创建一个新的数组**，并且我们可以传入一个判断函数，将符合条件的元素，放入新的数组。

现在我们有一个数组，里面存放了很多游戏，每个元素都记录了该游戏需要花多少钱：

```JavaScript
let games = [
  {
    name: '英雄联盟',
    cost: 45
  },
  {
    name: '穿越火线',
    cost: 888
  },
  {
    name: '魔兽世界',
    cost: 75
  },
  {
    name: '征途',
    cost: 1000000
  }
]
```

然后我们需要找出，花费不超过一百元的游戏，该怎么做呢？

可能是这样：

```javascript
let target = []
for (let i = 0; i < games.length; i++) {
	if (games[i].cost <= 100) {
		target.push(games[i])
	}
}
```

这是大家从大学开始学 C 语言的时候就会用的方法。但是我现在想用`filter()`方法重写它：

```javascript
let target = games.filter((game) => {
	return game.cost <= 100
})
```

Wow! Awesome!

我稍微解释一下，防止有同学没有看懂，这里传入了一个函数，函数接收了一个参数，这个参数就是 games 的每一个元素依次传入的值，在每一次传入之后，我们都返回一个逻辑值，这个逻辑值取决于该游戏的`cost`是否小于 100，如果返回了`true`该元素就会被放到新的数组里去，反之同理。

**注意：**

- `filter()`不会对空数组进行检测
- `filter()`不会改变原始数组

实际上，filter 内部的处理方法可能和我们使用 for 循环一模一样！但是我们利用函数式编程，写了更少的代码、更少的逻辑。Less code! Less time! Less bug!

这就是函数式编程的美妙之处。

## map()

我们现在先看回之前写的数组：

```javascript
let games = [
	{
		name: '英雄联盟',
		cost: 45,
	},
	{
		name: '穿越火线',
		cost: 888,
	},
	{
		name: '魔兽世界',
		cost: 75,
	},
	{
		name: '征途',
		cost: 1000000,
	},
]
```

现在我们要把每一款游戏的名字都拿出来，组成一个新的数组。

这种操作是非常常见的，比如我们使用 React，向服务器请求了 JSON 数据，接下来需要在这里渲染名字，那里渲染价格……等等。

```javascript
let gameName = games.map((item) => {
	return item.name
})
```

我们甚至可以做出更多的骚操作，比如说：

```JavaScript
let gameName = games.map((item) => {
  return `${item.name}是一个${item.cost > 100? '坑钱游戏':'良心游戏'}`
})
```

得到结果`["英雄联盟是一个良心游戏", "穿越火线是一个坑钱游戏", "魔兽世界是一个良心游戏", "征途是一个坑钱游戏"]`

Wow! Awesome! 只能用优雅两个字来形容！

## reduce()

`reduce()`单词本身是减少的意思，但是实际上你可以将`reduce()`理解为求和（事实并不如此，reduce 更加强大且灵活，但是此时可以暂时这么理解，更多特性可以在下一节看到）。

语言太过苍白无力，我们来看看代码：

```javascript
let numbers = [1, 2, 3, 4, 5]
let sum = numbers.reduce((total, item) => total + item)
```

_这里是利用了箭头函数可以省略 return 的特性。_

这里的传入的函数接收了两个参数（实际上可以接收四个，但这里不需要后面两个），这两个参数通过英文应该就可以看懂。

`reduce()`方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。

**注意:** `reduce()`对于空数组是不会执行回调函数的。

这里可以给一个初始值：

```javascript
let numbers = [1, 2, 3, 4, 5]
let sum = numbers.reduce((total, item) => {
	return total + item
}, 10)
```

之前的和是 15，这次加上了一个初始值，就是 25 了。

## 实践

假设我们有一个 TXT 文件。

```
徐航宇	游泳	4分钟
徐航宇	跑步	1分钟
徐航宇	跳远	3米
刘好	游泳	5分钟
刘好	跑步	2分钟
刘好	跳远	1米
```

我们用 node 去读取他，让它变成一个 JSON 对象，就像这样：

```JavaScript
{
  "徐航宇": [
    {
      "项目": "游泳",
      "时间": "4分钟"
    },
    {
      "项目":"跑步",
      "时间":"1分钟"
    }
  ],
  "刘好": [
    // ...
  ]
}
```

上代码！

```JavaScript
import fs from 'fs'

let output = fs.readFileSync('data.txt', 'utf-8')
	.trim() // 去掉字符串头尾的空格，返回新数组
	.split('\n') // 在换行处截断，组成数组
	.map(line => line.split('\t')) // 每一行根据制表符断开，组成数组
```

这一步之后，我们应该得到什么？

```JavaScript
[
  ["徐航宇", "游泳", "4分钟"],
	["徐航宇", "跑步", "1分钟"],
	["徐航宇", "跳远", "3米"],
	["刘好", "游泳", "5分钟"],
	["刘好", "跑步", "2分钟"],
	["刘好", "跳远", "1米"]
]
```

接下来想要变成对象，该怎么做呢？

首先第一步，我们想一想，要想变成最终的 JSON 数据，我们需要对每一项进行处理：

- 把每一个数组的第一个作为对象的属性名
- 把每一个数组的二三项组成新的对象，放入该属姓名的值中

接下来就该`reduce()`出场了：

```javascript
.reduce((customer, line) => {
  	// 提取每个数组的第一项，作为传入对象的属姓名
  	// 如果该项是以前没有的，那么将其初始化为空数组
  	// 如果该项以前有，那么就不动他
    customer[line[0]] = customer[line[0]] === undefined ? [] : customer[line[0]]
		customer[line[0]].push({
      name: line[1],
      time: line[2]
    })
    return customer
  }, {})
```

这样就成功了！

## 总结

总而言之，函数式编程如果归根结底，和直接写没有任何区别。但是它提供给了我们一种**写更少的代码**，完成更多的事情的方法。

人是难免会出错的，代码量越大、错误可能就会越多，所以更少代码的函数式编程，往往意味着：更少的 bug。

（完）
