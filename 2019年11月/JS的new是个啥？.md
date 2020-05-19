# JS 的 new 是个啥？

本文写于 2019 年 11 月 25 日

`new`关键字在很多语言里面，总是用于把类实例化，可是 JS 之前就没有“类”这个概念呀。

那 JS 的`new`有啥用？

这就要从 JS 的面向对象开始谈起了。

我学习 JS 基本上是从 ES6 开始的，所以一直没太管之前的面向对象。

最近在看 2012 年出版的红宝书用来复习的时候，突然发现原来的面向对象，其实还是蛮恶心的。

并且即使是 ES6 的面向对象，他也是基于原型链的，这个包装严格来说，**只是语法糖**而已。

## OOP 是啥

首先简单介绍一下 OOP，面向对象。

面向对象编程（Object Oriented Programming，缩写为 OOP）是目前主流的编程范式。它将真实世界各种复杂的关系，抽象为一个个对象，然后由对象之间的分工与合作，完成**对真实世界的模拟**。

这个学过 Java 或者 C#之类的同学应该都懂。

首先有两点：

- 对象是单个实物的抽象
  一本书、一辆汽车、一个人都可以是对象。当实物被抽象成对象，实物之间的关系就变成了对象之间的关系，从而就可以模拟现实情况，针对对象进行编程。

- 对象是一个容器，封装了属性（property）和方法（method）
  属性是对象的状态，方法是对象的行为（完成某种任务）。比如，我们可以把动物抽象为 animal 对象，使用“属性”记录具体是那一种动物，使用“方法”表示动物的某种行为（奔跑、捕猎、休息等等）。

可以说，OOP 是目前为止，最符合人类思维方式的编程了。

## 没有 class 的 JS

曾经的 JS 根本没有 class 这个说法，那 JS 是怎么批量创建对象的吗？

```javascript
let a = {
  name: 'a',
  age: 18
}

let b = {
  name: 'b',
  age: 14
}

let c = {
  name: 'c',
  age: 20
}
```

难道会像这样一个一个的创建吗？

那也太愚蠢了。并且一旦需要修改，就要修改**每一项**！

那怎么改呢？

可以利用原型——公共的一块内存。

```javascript
function createPerson(name) {
  const obj = Object.create(createPerson.personPrototype)
  obj.name = name
  return obj
}

createPerson.personPrototype = {
  sayName() {
    return `你好，我叫${this.name}。`
  },
  constructor: createPerson
}
```

这套操作就非常神奇了！

一旦我们有一个数组：`['Jack', '二狗', 'Rose', '李铁蛋']`。

该数组需要被创建成 Person 对象，就可以这么操作了：

```javascript
const arr = ['Jack', '二狗', 'Rose', '李铁蛋']
const person = []

for (let name of arr) {
  const p = createPerson(name)
  console.log(`我的创造者是：${p.constructor}`)
  person.push(p)
}
```

我们在 personPrototype 中，记录了构造函数；在构造函数中，利用 personPrototype 构造了新的对象。

所以我们可以随时通过构造函数构造新对象；也可以随时查看对象的构造函数！

非常 amazing 呀！

既然这个方法这么好，所以，JS 的`prototype`就是如此运作的！

接下来看一下，具体的细节。

## 构造函数

面向对象编程的第一步，就是要生成对象。（要是还找不到对象，就 new 一个好了。）

前面说过，对象是单个实物的抽象。**通常需要一个模板**，表示某一类实物的共同特征，然后对象根据这个模板生成。

典型的面向对象编程语言（比如 C++ 和 Java），都有“类”（class）这个概念。

所谓“类”就是对象的模板，对象就是“类”的实例。

但是，JavaScript 语言的对象体系，不是基于“类”的，**而是基于构造函数（constructor）和原型链（prototype）**。

JavaScript 语言使用构造函数（constructor）作为对象的模板。

所谓”构造函数”，就是专门用来生成实例对象的函数。

它就是对象的模板，描述实例对象的基本结构。一个构造函数，可以生成多个实例对象，这些实例对象都有相同的结构。

构造函数就是一个普通的函数，但是有自己的特征和用法：

```javascript
let Vehicle = function () {
  this.price = 1000
}
```

上面代码中，Vehicle 就是构造函数。为了与普通函数区别，构造函数名字的**第一个字母通常大写**。

构造函数的特点有两个。

- 函数体内部使用了 this 关键字，代表了所要生成的对象实例。
- 生成对象的时候，必须使用 new 命令。

## new 一个对象

new 命令的作用，就是执行构造函数，返回一个实例对象。

```javascript
let Vehicle = function () {
  this.price = 1000
}

let v = new Vehicle()
v.price // 1000
```

上面代码通过 new 命令，让构造函数 Vehicle 生成一个实例对象，保存在变量 v 中。

这个新生成的实例对象，从构造函数 Vehicle 得到了 price 属性。

new 命令执行时，构造函数内部的 this，就代表了新生成的实例对象，this.price 表示实例对象有一个 price 属性，值是 1000。

使用 new 命令时，根据需要，构造函数也可以接受参数：

```javascript
let Vehicle = function (p) {
  this.price = p
}

let v = new Vehicle(500)
```

new 命令本身就可以**执行**构造函数，所以后面的构造函数可以带括号，也可以不带括号。

（这里我的编辑器会强行给我补上括号，如果发布的文章，俩都有括号，代表我**不小心格式化了这篇文章**）

下面两行代码是等价的，但是为了表示这里是函数调用，推荐使用括号。

```javascript
// 推荐的写法
let v = new Vehicle()
// 不推荐的写法
let v = new Vehicle
```

**那如果忘了使用 new 命令，直接调用构造函数会发生什么事？**

这种情况下，构造函数就变成了普通函数，并不会生成实例对象。而且由于后面会说到的原因，**this 这时代表全局对象**，将造成一些意想不到的结果。

```javascript
let Vehicle = function () {
  this.price = 1000
}

let v = Vehicle()
v // undefined
price // 1000
```

上面代码中，调用 `Vehicle` 构造函数时，忘了加上 `new` 命令。结果，变量 `v` 变成了 `undefined`，而 **price 属性变成了全局变量**。

因此，应该非常小心，避免不使用 `new` 命令、直接调用构造函数。

为了保证构造函数必须与 `new` 命令一起使用，一个解决办法是，构造函数内部使用严格模式，即第一行加上 `use strict`。

这样的话，一旦忘了使用 `new` 命令，直接调用构造函数**就会报错**。

（其实有了新的 ES6 语法，这就没有必要了）

```javascript
function Fubar(foo, bar) {
  'use strict'
  this._foo = foo // 这种写法，是用来伪装一个私有变量的，但其实现在可以用Symbol对象完成
  this._bar = bar
}

Fubar()
```

上面代码的 Fubar 为构造函数，`use strict` 命令保证了该函数在严格模式下运行。

由于严格模式中，函数内部的 `this` 不能指向全局对象，默认等于 `undefined`，导致不加 `new` 调用会报错。（JavaScript 不允许对 `undefined` 添加属性）

另一个解决办法，构造函数内部判断是否使用 new 命令，如果发现没有使用，则直接返回一个实例对象。

```javascript
function Fubar(foo, bar) {
  if (!(this instanceof Fubar)) {
    return new Fubar(foo, bar)
  }

  this._foo = foo
  this._bar = bar
}

Fubar(1, 2)._foo(
  // 1
  new Fubar(1, 2)
)._foo // 1
```

上面代码中的构造函数，不管加不加 `new` 命令，都会得到同样的结果。

**ES5 的语法是不是很变态？**我很庆幸在 ES6 之后学习的 JS（笑）。

## new 命令的原理

使用 `new` 命令时，它后面的函数依次执行下面的步骤：

1. 创建一个空对象，作为将要返回的对象实例；
2. 将这个空对象的原型，指向构造函数的 `prototype` 属性；
3. 将这个空对象赋值给函数内部的 `this` 关键字；
4. 开始执行构造函数内部的代码。

也就是说，构造函数内部，`this` 指的是一个新生成的空对象，所有针对 `this` 的操作，都会发生在这个空对象上。

构造函数之所以叫“构造函数”，就是说这个函数的目的，就是操作一个空对象（即 `this` 对象），将其“构造”为需要的样子。

如果构造函数内部有 `return` 语句，而且 `return` 后面跟着一个对象，new 命令会返回 `return` 语句指定的对象；否则，就会不管 `return` 语句，返回 `this` 对象。

```javascript
let Vehicle = function () {
  this.price = 1000
  return 1000
}

new Vehicle() === 1000 // false
```

上面代码中，构造函数 `Vehicle` 的 `return` 语句返回一个数值。

这时，`new` 命令就会忽略这个 `return` 语句，返回“构造”后的 this 对象。

但是，如果 return 语句返回的是一个跟 `this` 无关的新对象，`new` 命令会返回这个新对象，而不是 `this` 对象。**这一点非常非常的诡异！**

```javascript
let Vehicle = function () {
  this.price = 1000
  return { price: 2000 }
}

new Vehicle().price
// 2000
```

上面代码中，构造函数 `Vehicle` 的 `return` 语句，返回的是一个新对象。**new 命令会返回这个对象，而不是 this 对象。**

另一方面，**如果对普通函数（内部没有 this 关键字的函数）使用 new 命令，则会返回一个空对象。**

```javascript
function getMessage() {
  return 'this is a message'
}

let msg = new getMessage()

msg // {}
typeof msg // "object"
```

上面代码中，`getMessage` 是一个普通函数，返回一个字符串。对它使用 `new` 命令，会得到一个空对象。

这是因为 `new` 命令总是返回一个对象，要么是实例对象，要么是 `return` 语句指定的对象。

`但是在例子中，return` 语句返回的是字符串，所以 `new` 命令就忽略了该语句。

**`new` 命令简化的内部流程，可以用下面的代码表示。**

```javascript
function _new(constructor, params) {
  let args = [].slice.call(arguments)
  let constructor = args.shift()
  let context = Object.create(constructor.prototype)
  let result = constructor.apply(context, args)
  return typeof result === 'object' && result != null ? result : context
}

// 实例
let actor = _new(Person, '张三', 28)
```

结论，ES6 真好！可是这些我们一定要了解，因为 ES6 只是语法糖，这个才是本质。

最后，写一个简单例子：

使用 ES6 的 class 方法来做一个手机类：

```javascript
class Phone {
  constructor(brand, price) {
    this.brand = brand
    this.price = price
  }

  call() {
    return `我使用了${this.brand}手机打电话！`
  }
}

let a = new Person('苹果', '100')
```

使用原型方法来实现：

```javascript
function phone(brand, price) {
  this.brand = brand
  this.price = price
}

phone.prototype.call = function () {
  return `我使用了${this.brand}手机打电话！`
}

let a = new phone('华为', '200')
```

（完）
