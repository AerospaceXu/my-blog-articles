# JS 进阶与查缺（上）

本文写于 2020 年 3 月 3 日

基础知识、常用方法+ES6 的一些新知识。并不全面，但是是我觉得容易遗漏的一些知识。

## 01 变量的声明

以前我们在声明变量时只有一种方法，就是使用 var 来进行声明，甚至于都没有常量的声明。

而 ES6 对声明的进行了扩展，现在可以有三种声明方式了——`var`、 `let`、 `const`

先稍微解释一下：

1. `var`：很明显，它是 variable 的简写，可以理解成变量的意思；
2. `let`：也可以理解为一种声明的意思，即为“让”xxx 变量存放 xxx 值；
3. `const`：在英文中是常量的意思，在 ES6 也是用来声明常量的。

### 1.1 var

var 在 ES6 里和之前是一样的，**他会升级全局变量**。（这个升级可不是好事，和游戏里的打怪升级是不一样的）

那什么是升级全局变量呢？

我们可以先作一个最简单的实例，用 var 声明一个变量 a，然后用`console.log`进行输出：

```javascript
var a = 'hello world'
console.log(a)
```

我们可以看到`hello world`在控制台已经被打印出来了。

那么接下来，我们使用匿名函数给他进行一个包裹，然后再在匿名函数中调用这个 a 变量，看看能不能调用到：

```javascript
var a = 'hello world'
// 这里尽量不要使用window.onload，因为这样如果同时声明了两处，会导致冲突
// 所以能用addEventListener就用
window.addEventListener('load', function () {
	console.log(a)
})
```

可以看到控制台输出了"hello world"，这证明**var 确实是全局的**。

我们再试试：

```javascript
var a = 2
{
	var a = 3
}
console.log(a)
```

这时打印出来的值是多少呢？对，应该是 3，因为 var 是全局声明的！！！

### 1.2 let

基本上是个敲过两天代码的都看得出来，var 没有块级作用域是多么的恶心人，所以我们引入了 let。

我们来试着在区块里用 let 声明：

```javascript
var a = 2
{
	let a = 3
}
console.log(a)
```

这时候控制台打印出来的值就是 2 了！

但是如果我们只在区块里声明，不在外部声明，我们打印 a 时就会报错，显示找不到变量：

```javascript
{
	let a = 3
}
console.log(a)
```

用 var 声明的循环：

```javascript
for (var i = 0; i < 10; i++) {
	console.log('循环体中:' + i)
}
console.log('循环体外:' + i)
```

如果在浏览器里粘贴这段代码，就能看到，最后一条输出居然是“循环体外：10”！

这两个例子说明了**let 是局部变量声明，只在区块内起作用，外部是不可以调用的**。它不会像 var 一样疯狂污染你的全局命名空间。

所以我们应该用 let 声明，彻底代替 var 声明（除非万不得已），去避免污染全局空间。

### 1.3 const

在程序开发中，有些变量是希望声明后在业务层就不再发生变化了。简单来说就是从声明开始，这个变量始终不变，那么就需要用 const 进行声明。

我们线来一段用 const 声明错误的代码：

```javascript
const a = 'hello world'
var a = 'hello China'
console.log(a)
```

在编译这段代码的过程中，你就会发现已经报错，无法编译了！

原因就是我们 const 声明的变量是不可以改变的。

---

## 02 函数

JS 中函数属于 JS 中的“上流社会”，具有很强的抽象能力，我们可以像变量一样去使用她。

### 2.1 函数的定义

**方式一**

```javascript
function abs(x) {
	if (x >= 0) {
		return x
	} else {
		return -x
	}
}
```

**方式二**

```javascript
let abs = function (x) {
	if (x >= 0) {
		return x
	} else {
		return -x
	}
}
```

**方式三：Arrow Function**

箭头函数，顾名思义，就是看起来像个箭头：`x => x * x`

`x => x * x` 等价于：`function (x) { return x * x; }`

1. 参数等于一个，可以省略括号；
2. 语句等于一条，可以省略大括号。

**关于箭头函数的 this**

箭头函数的 this 指向和之前的`function() {}`声明不一样，function 的 this 指向是：

1. this 指向调用此函数的对象；
2. 如果函数用作构造函数，那么 this 指向构造函数创建的对象实例。

箭头函数则是**不创建 this**：在声明时，捕获作用域内的 this（就近原则）。

### 2.2 函数的调用

我们都知道立即执行函数：

```javascript
function foo() {

}()
```

只需要在后面加上括号即可。

同样的，如果说我们在绑定 click 事件时：

```javascript
xxxx.addEventListener('click', (function () {})())
```

他也会立即执行，任何时候，加一个括号，都会立即执行！

### 2.3 函数的参数

JS 的函数调用允许传入**任意多的参数**——无论是比定义时多还是少，都不影响函数调用。

例如：

```javascript
let abs = (x) => x * 50
abs(2) // 100
abs(2, 5, 'hello') // 100
abs() // NaN
```

为了避免得到一个 NaN，可以对参数进行检查：

```javascript
function abs(x) {
	if (isNaN(x)) {
		throw 'Not a number' // 自定义一个报错
		// 或者return "wrong"
	}
}
```

#### 2.3.1 arguments

**在传入参数之后，JS 会帮我们将所有的参数都放进一个类似数组的东西里面，她就是 arguments。**

_他酷似数组，但不是数组。_

```javascript
function abs() {
	console.log(arguments)
}

abs() // Arguments [callee: ƒ, Symbol(Symbol.iterator): ƒ]
abs(10, 'hello') // Arguments [10, "hello", callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

这也就意味着，我们可以在**不定义参数**的情况下，拿到传入的所有参数，并通过`arguments[i]`的形式来调用。

arguments 最常见的用法，是利用他完成**类似重载**的功能——因为 JS 没有重载这个概念。

```javascript
function abs(a, b, c) {
	if (arguments.length == 1) {
		console.log(a)
	} else if (arguments.length == 2) {
		console.log(a, b)
	} else if (arguments.length == 3) {
		console.log(a, b, c)
	} else {
		console.log('lalala')
	}
}
```

#### 2.3.2 rest

**我们如何拿到函数调用时多的参数？**

```javascript
function foo(a, b) {
	let i
	let rest = []
	if (arguments.length > 2) {
		for (i = 2; i < arguments.length; i++) {
			rest.push(arguments[i]) // 如果多出两个参数，就把多出来的放入rest数组
		}
	}
}
```

这样非常的麻烦，庆幸的是在 ES6 标准中我们引入了**rest**参数：

```JavaScript
function foo(a, b, ...rest) {
    console.log(rest);
}

foo(1, 2, 3, 4, 5); // Array [ 3, 4, 5 ]

foo(1); // Array []
```

rest 参数只能作为**最后一个参数**，前面用`...`标识。

**扩展运算符**

看着`...rest`，有没有很好奇`...`究竟是什么？

在 Swift 中，`...`用来表示一个闭区间，`a...b`表示 [a,b] 区间。

而 JS 中，`...`叫做扩展运算符，是 ES6 的新特性，可以非常好的增强代码的可读性。

下面来看几个例子。

**1. 函数传参**

```javascript
function foo(a, b, c) {
	console.log(a)
	console.log(b)
	console.log(c)
}

let arrray = [1, 2, 3]
foo(...array) // 等价于foo(array[0], array[1], array[2])
```

**2. 数组和对象的拷贝**

_扩展运算符大幅度减少拷贝所需代码量。_

**数组：**

```javascript
let a = [1, 2, 3]
let b = [...a]

b[0] = 8
console.log(a) // [1, 2, 3]
console.log(b) // [8, 2, 3]
```

**对象：**

```javascript
let a = {
	name: 'lalala',
	age: 12,
	parents: ['dad', 'mom'],
}
let b = { ...a }

b.parents[0] = 'dilidili'
b.age = 100
console.log(a) // age:12, parents: ["dilidili"]
console.log(b) // age:100, parents: ["dilidili"]
```

这里我们可以看到，扩展运算符其实**只遍历一层**，如果有多层嵌套，第二层往里就会变成浅复制。

**3. 构造字面量数组**

在没有展开语法的时候，只能组合使用 push, splice, concat 等方法，来将已有数组元素变成新数组的一部分。

利用扩展运算符，让数组的拼接一目了然：

```javascript
let arr1 = [1, 2, 3]
let arr2 = [4, 5, ...arr1] // [4, 5, 1, 2, 3]
arr1 = [...arr1, ...arr2] // [1, 2, 3, 4 , 5, 1, 2, 3]
```

**4. 拆分字符串**

`...`也可以将字符串拆分成单个字符。

```javascript
let str = 'hello'
let a = [...str] // ["h", "e", "l", "l", "o"]
```

**5. 作为 rest 参数和扩展运算符时易搞混**

```javascript
let foo = (...m) => fn(...m)
let fn = (a, b) => console.log(a + b)
foo(1, 2)
```

其中第一个`...m`收集了传入的参数，将 1 和 2 放入了数组`m = [1, 2]`。

第二个`...m`将`m = [1, 2]`，进行扩展运算，将 1 和 2 作为形参传入 fn 函数。

#### 2.3.4 默认参数

若传入该参数，则取传入的值；若为传入该参数，则取默认值。

```javascript
let fn = (a, b = 5, c = 12) => {
	console.log(a)
	console.log(b)
	console.log(c)
}

fn(1) // a = 1, b = 5, c = 12
fn(1, 2, 3) // a = 1, b = 2, c = 3
```

---

## 03 解构赋值

一般来说，如果我们`let a = [1, 2, 3]`，那么我们要将每一个元素分别赋值给不同的变量，我们需要：

```javascript
let a = [1, 2, 3]
let x = a[0]
let y = a[1]
let z = a[2]
```

**ES6 之后我们可以使用解构赋值来完成这些冗杂的操作。**

解构赋值注意事项：

1. 左右两边必须一致；
2. 声明必须合法；
3. 声明与赋值同步进行。

### 3.1 数组的解构赋值

```javascript
let a = [1, 2, 3]
let [x, y, z] = a
x // 1
y // 2
z // 3
```

如果有嵌套关系：

```javascript
let a = [1, [2, 3]]
let [x, [y, z]] = a
x // 1
y // 2
z // 3
```

### 3.2 对象的解构赋值

解构赋值可以用于对象：

```javascript
let a = {
	name: '孙悟空',
	age: 18,
}
let { age, name } = a
let { lala, lili } = a
name // "孙悟空"
age // 18
lala // undefined
lili // undefined
```

可以看出来，**对象的解构赋值是根据属性名来判断的**，如果属性名对不上。

#### 3.2.1 如果不希望用原属性名

```javascript
let person = {
	name: '小明',
	age: 20,
	gender: 'male',
	passport: 'G-12345678',
	school: 'No.4 middle school',
}

// 把passport属性赋值给变量id:
let { name, passport: id } = person
passport // undefined
id // 'G-12345678'
```

同时也可以看出，对象的解构赋值不仅不需要一一对应，而且不需要数量相同。

#### 3.2.2 可以直接赋值

```javascript
let person = {
	name: '小明',
	age: 20,
}

// 如果person对象没有single属性，默认赋值为true:
let { name, single = true } = person
name // '小明'
single // true
```

#### 3.2.3 如果有嵌套关系

```javascript
let person = {
	name: '小明',
	age: 20,
	gender: 'male',
	passport: 'G-12345678',
	school: 'No.4 middle school',
	address: {
		city: 'Beijing',
		street: 'No.1 Road',
		zipcode: '100001',
	},
}
let {
	name,
	address: { city, zip },
} = person
```

### 3.3 另外

```javascript
let x, y;
{x, y} = { name: '小明', x: 100, y: 200};
```

会报错！

因为 JS 把 **{ }** 解析到了一起，代码就变成了：`x, y} = { name: '小明', x: 100, y: 200`

**解决方案：**添加括号。

```javascript
let x, y
;({ x, y } = { name: '小明', x: 100, y: 200 })
```

---

## 04 数组

### in 的用法

in 可以解决一个 ES5 判断的弊端。

以前会使用 length 属性进行判断，为 0 就表示没有数组元素。但是这并不准确。

```javascript
let arr = [, , , , ,]
console.log(arr.length) //5
```

上边的代码输出了 5，但是数组中其实全是空值，这就是一个坑！

用 ES6 的 in 就可以解决这个问题。

```javascript
let arr = [, , , , ,]
console.log(0 in arr) //false

let arr1 = ['xhy', 'lh']
console.log(0 in arr1) // true
console.log('xhy' in arr1) // false
```

**注意，这里表示脚标位置是否有元素**

也可以用来判断对象的 key 值：

```javascript
let obj = {
	a: 'xhy',
	b: 'lh',
}
console.log('a' in obj) // true
// 注意，不是判断值，而是key值
console.log('xhy' in obj) // false
```

### join()方法

```javascript
let arr = ['xhy', 'lh', 'lalalala']

console.log(arr.join('|'))
```

`join()`方法就是在数组元素中间，加了一些间隔，和 toString 方法比较像，但 toString 转换时是用逗号隔开的，join 我们可以自定义。

`toString()`方法：

```javascript
let arr = ['xhy', 'lh', 'forever']

console.log(arr.toString())
```

### Array.of()方法

它负责把一堆文本或者变量转换成数组。

在开发中我们经常拿到了一个类似数组的字符串，需要使用 eval 来进行转换，但是不得不说，eval 的效率是很低的，它会拖慢我们的程序。

这时候我们就可以使用`Array.of`方法。我们看下边的代码把一堆数字转换成数组并打印在控制台上：

```javascript
let arr = Array.of(3, 4, 5, 6)
console.log(arr)
```

当然它不仅可以转换数字，字符串也是可以转换的，看下边的代码：

```javascript
let arr = Array.of('xhy', 'lh', 'lalalala')
console.log(arr)
```

### find()实例方法

所谓的实例方法就是并不是以 Array 对象开始的，而是必须有一个已经存在的数组，然后使用的方法，这就是实例方法。

这里的 find 方法是从数组中查找。

在 find 方法中需要传入一个匿名函数，函数需要传入三个参数：

- value：表示当前查找的值。
- index：表示当前查找的数组索引。
- arr：表示当前数组。

在函数中如果找到符合条件的数组元素就进行 return，并停止查找。

我们来尝试一下：

```javascript
let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
console.log(
	arr.find(function (value, index, arr) {
		return value > 5 // 返回一个布尔值
	})
)
```

控制台输出了 6，说明找到了符合条件的值，并进行返回了，如果找不到会显示 undefined。

### fill()实例方法

`fill()`也是一个实例方法，它的作用是把数组进行填充。

它接收三个参数，第一个参数是填充的变量，第二个是开始填充的位置，第三个是填充到的位置。

```javascript
let arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
arr.fill('xhy', 2, 5)
console.log(arr)
```

上边的代码是把数组从第二位到第五位用 xhy 进行填充。

#### 数组的遍历

**for…of 循环：**

这种形式比 ES5 的 for 循环要简单而且高效。先来看一个最简单的 for…of 循环：

```javascript
let arr = ['xhy', 'lh', 'forever']
for (let item of arr) {
	console.log(item)
}
```

**for…of 数组索引：**

有时候开发中是需要数组的索引的，那我们可以使用下面的代码输出数组索引：

```javascript
let arr = ['xhy', 'lh', 'forever']
for (let index of arr.keys()) {
	console.log(index)
}
```

可以看到这时的控制台就输出了 0,1,2，也就是数组的索引。

**同时输出数组的内容和索引：**

我们用`entries()`这个实例方法，配合我们的 for…of 循环就可以同时输出内容和索引了。

```javascript
let arr = ['xhy', 'lh', 'forever']
for (let [index, val] of arr.entries()) {
	console.log(index + ':' + val)
}
```

`entries()`实例方式生成的是`Iterator`形式的数组，那这种形式的好处就是可以让我们在需要时用`next()`手动跳转到下一个值。我们来看下面的代码：

```javascript
let arr = ['xhy', 'lh', 'forever']
let list = arr.entries()
console.log(list.next().value)
console.log(list.next().value)
console.log(list.next().value)
```

**除了 for 循环的形式，更推荐使用以下的方法进行遍历：**

**1. forEach()**

```JavaScript
let arr=['xhy','lh','like'];

arr.forEach((val,index) => console.log(index,val));
```

forEach 循环的特点是会自动省略为空的数组元素，相当于直接给我们筛空了。_有时候很好，但有时候也会给我们帮倒忙。_

**2. filter()**

创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素：

```JavaScript
let arr=['xhy','lh','like'];

arr.filter(x => console.log(x));
```

**3. some**

some 会依次执行数组的每个元素：

- 如果有一个元素满足条件，则表达式返回 true , 剩余的元素不会再执行检测;
- 如果没有满足条件的元素，则返回 false。

```JavaScript
let arr=['xhy','lh','like'];

arr.some(x => console.log(x));
```

**4. map**

通过函数设定规则，根据规则替换：

```JavaScript
let arr=['xhy','lh','like'];

console.log(arr.map(x => 'web'));
```

我在网上看到过一张图，很好的说明了`map()` `filter()`之间的关系。

filter，相当于把你想要的菜挑出来；而 map 相当于把菜做成菜。

### JSON 数组格式转换

JSON 的数组格式就是为了前端快速的把 JSON 转换成数组的一种格式，我们先来看一下 JSON 的数组格式怎么写。

```javascript
let json = {
	'0': 'xhy',
	'1': 'lh',
	'2': 'forever',
	length: 3,
}
```

这就是一个标准的 JSON 数组格式，跟普通的 JSON 对比是在最后多了一个 length 属性。

只要是这种特殊的 json 格式都可以轻松使用 ES6 的语法转变成数组：

```javascript
let json = {
	'0': 'xhy',
	'1': 'lh',
	'2': 'forever',
	length: 3,
}

let arr = Array.from(json)
console.log(arr)
```

打印结果：`["xhy", "lh", "forever"]`

---

## 05 字符串

### 两个新方法

#### 1. starts With

```javascript
let str = 'https://www.baidu.com'
if (str.startsWith('https://')) {
	console.log('安全网址')
} else if (str.startsWith('http://')) {
	console.log('普通网址')
} else if (str.startsWith('git://')) {
	console.log('git地址')
} else if (str.startsWith('svn://')) {
	console.log('svn地址')
} else {
	console.log('其他')
}
```

判断该字符串开头是不是"https://"。

#### 2. endsWith

```javascript
let str = 'xhy.txt'
if (str.endsWith('.txt')) {
	console.log('文本文档')
} else if (str.startsWith('.jpg')) {
	console.log('jpg图片')
} else if (str.startsWith('git://')) {
	console.log('git地址')
} else if (str.startsWith('svn://')) {
	console.log('svn地址')
} else {
	console.log('其他')
}
```

### 字符串模板

```javascript
let a = 12
let str = `abc${a}dd`
```

可以用于连接字符串：

```javascript
let content = 'hello world'
let text = 'nihao'
let str = `<div>${content}</div>
					 <p>${text}</p>`
```

---

## 06 关于 JS 的数字

**二进制声明：**

二进制的英文单词是 Binary，二进制的开始是 0（零），然后第二个位置是 b（注意这里大小写都可以实现），然后跟上二进制的值就可以了。

```javascript
let binary = 0b010101
console.log(binary)
```

这时候浏览器的控制台显示出了`21`。

**八进制声明**

八进制的英文单词是 Octal，也是以 0（零）开始的，然后第二个位置是 O，然后跟上八进制的值就可以了。

```javascript
let b = 0o666
console.log(b)
```

这时候浏览器的控制台显示出了 438。

**如果，后面的数字超出了八进制的范围，浏览器会自动忽略前面的 0o，当作十进制处理！**

**数字验证 Number.isFinite(xx)**

可以使用`Number.isFinite()`来进行数字验证，只要是数字，不论是浮点型还是整形都会返回 true，其他时候会返回 false。

```javascript
let a = 11 / 4
console.log(Number.isFinite(a)) //true
console.log(Number.isFinite('jspang')) //false
console.log(Number.isFinite(NaN)) //false
console.log(Number.isFinite(undefined)) //false
```

**NaN 验证**

可以使用`Number.isNaN()`来进行验证。下边的代码控制台返回了 true。

```javascript
console.log(isNaN(NaN)) // true
console.log(isNaN('A')) // true
console.log(isNaN('1')) // false 因为会自动转化为数字
```

**判断是否为整数**

`Number.isInteger(xx)`判断是否为整数。

```javascript
let a = 123.1
console.log(Number.isInteger(a)) //false
```

**整数转换浮点型转换**

`Number.parseInt(xxx)`和`Number.parseFloat(xxx)`。

```javascript
let a = '9.18'
console.log(Number.parseInt(a))
console.log(Number.parseFloat(a))
```

**整数取值范围操作**

整数的操作是有一个取值范围的，它的取值范围就是 2 的 53 次方。我们先用程序来看一下这个数字是什么.

```javascript
let a = Math.pow(2, 53) - 1
console.log(a) //9007199254740991
```

在我们计算时会经常超出这个值，所以我们要进行判断，ES6 提供了一个常数，叫做最大安全整数，以后就不需要我们计算了。

**最大安全整数**

```javascript
console.log(Number.MAX_SAFE_INTEGER)
```

**最小安全整数**

```javascript
console.log(Number.MIN_SAFE_INTEGER)
```

**安全整数判断 isSafeInteger( )**

```javascript
let a = Math.pow(2, 53) - 1
console.log(Number.isSafeInteger(a)) //false
```
