# 你不知道的 JavaScript 基础数据类型

## JS 的 8 种数据类型数据类型

JavaScript 拥有 8 种数据类型，分别是：

- undefined
- Null
- Boolean
- String
- Number
- BigInt
- Symbol
- Object
  - Array
  - Function
  - RegExp
  - Date
  - ...

其中除了 **Object 是引用类型**，其他的皆为基础类型。

各种 JavaScript 的数据类型最后都会在初始化之后放在不同的内存中，因此上面的数据类型大致可以分成两类来进行存储：

1. **基础类型存储在「栈内存」**，被引用或拷贝时，会创建一个完全相等的变量；
2. **引用类型存储在「堆内存」**，存储的是地址，多个引用指向同一个地址，这里会涉及一个“共享”的概念。

所谓共享，就是说如果你让 `arr2 = arr1`，那么 `arr2` 和 `arr1` 其实代表的就是同一个数组。如果你修改 `arr1`，`arr2` 的值也会随之改变。

## 数据类型检测

我们都知道 JS 拥有 `typeof` 和 `instanceof` 方法可以检测类型，但是实际上运用中往往不会这么容易。

所以在这里，我来介绍三种常用方法。

### typeof

```js
typeof 1; // 'number'
typeof '1'; // 'string'
typeof undefined; // 'undefined'
typeof true; // 'boolean'
typeof Symbol(); // 'symbol'
typeof []; // 'object'
typeof {}; // 'object'
typeof console; // 'object'
typeof console.log; // 'function'
typeof null; // 'object'
```

这里需要注意的是最后一行，`typeof null;`。

我们会发现返回值是一个 `object`，但实则不然，这只是 JS 的 Bug 之一罢了。

所以如果你需要判断目标是否为 `null`，只需要 `=== null` 即可。

另外 `typeof console.log;` 的返回值是 `function`，但并**不代表函数就是一种单独的类型**了。

因此我们发现，`typeof` 其实存在很大的弊端，它虽然可以判断基础数据类型（`null` 除外），但是引用数据类型中，除了 `function` 以外，其他的无法判断。

### instanceof

通过 `instanceof` 我们能判断这个对象是否是某个构造函数生成的对象。

例如

```js
console.log instanceof Function; // true

let Car = function () {};
let benz = new Car();
benz instanceof Car; // true
let car = new String('Mercedes Benz');
car instanceof String; // true
let str = 'Covid-19';
str instanceof String; // false
```

其实我们也可以自己实现一个 `instanceof` 方法。

```js
function myInstanceof(left, right) {
  // 这里先用typeof来判断基础数据类型，如果是，直接返回false
  if (typeof left !== 'object' || left === null) return false;
  // getProtypeOf是Object对象自带的API，能够拿到参数的原型对象
  let proto = Object.getPrototypeOf(left);
  while (true) {
    //循环往下寻找，直到找到相同的原型对象
    if (proto === null) return false;
    if (proto === right.prototype) return true; //找到相同原型对象，返回true
    proto = Object.getPrototypeof(proto);
  }
}
// 验证一下自己实现的myInstanceof是否OK
console.log(myInstanceof(new Number(123), Number)); // true
console.log(myInstanceof(123, Number)); // false
```

`instanceof` 可以准确地判断复杂引用数据类型，但是不能正确判断基础数据类型。

### Object.prototype.toString

`toString()` 是 Object 的原型方法，调用该方法，可以统一返回格式为 `“[object Xxx]”` 的字符串（**第一个首字母是大写，而 typeof 返回的则是小写**）。

```js
Object.prototype.toString({}); // "[object Object]"
Object.prototype.toString.call({}); // 同上结果，加上call也ok
Object.prototype.toString.call(1); // "[object Number]"
Object.prototype.toString.call('1'); // "[object String]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(function () {}); // "[object Function]"
Object.prototype.toString.call(null); //"[object Null]"
Object.prototype.toString.call(undefined); //"[object Undefined]"
Object.prototype.toString.call(/123/g); //"[object RegExp]"
Object.prototype.toString.call(new Date()); //"[object Date]"
Object.prototype.toString.call([]); //"[object Array]"
Object.prototype.toString.call(document); //"[object HTMLDocument]"
Object.prototype.toString.call(window); //"[object Window]
```

因此我们可以实现一个 `getType` 方法：

```js
const getType = (obj) => {
  return Object.prototype.toString
    .call(obj)
    .replace(/^\[object (\S+)\]$/, '$1'); // 注意正则中间有个空格
};

getType([]); // "Array" typeof []是object，因此toString返回
getType('123'); // "string" typeof 直接返回
getType(window); // "Window" toString返回
getType(null); // "Null"首字母大写，typeof null是object，需toString来判断
getType(undefined); // "undefined" typeof 直接返回
getType(); // "undefined" typeof 直接返回
getType(function () {}); // "function" typeof能判断，因此首字母小写
getType(/123/g); //"RegExp" toString返回
```

除了这三种方法之外，我们还有其他的一些方法，例如 `Array.isArray()` 来判断对象是否是数组。

（完）
