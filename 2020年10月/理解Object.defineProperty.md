# 理解 Object.defineProperty

本文写于 2020 年 10 月 13 日

Object.defineProperty 用于在一个对象上定义新的属性或修改现有属性并返回该对象。

什么意思呢？先不慌着理解，来一个例子看看再说。

```javascript
const obj1 = {};

Object.defineProperty(obj1, 'property1', {
  value: 42,
  writable: false,
});

obj1.property1 = 77;

console.log(obj1.property1); // 42
```

上过小学二年的同学都能看懂这段英文，说的是

1. 我们在 `obj1` 上定义一个 property（可以理解是一个属性或者元素）；
2. 这个 property 的名字叫做 `property1`；
3. `property1` 的值为 `42`；
4. 她不可以被改写。

因此我们访问 `obj1` 这个空对象的 `property1` 属性可以得到结果，但是一旦我们想要修改对应的值，会发现修改失败。

## 语法

`Object.defineProperty(obj, prop, descriptor);`

- `obj`

  要操作的对象，也就是 target。

- `prop`

  要新增或者修改的属性名称（可以是 Symbol）。

- `descriptor`

  一个对象，用于描述你的新增或是修改。

  具体属性如下：

  ```ts
  interface Descriptor {
    configurable?: boolean;
    enumerable?: boolean;
    value?: any;
    writable?: boolean;
    get?: () => any;
    set?: (value: any) => void;
  }
  ```

_上面使用了一下 TS 的接口写法，不理解的同学可以看一下文档。_

总的来说，如果我们简单的使用 `const obj = { a: 1 };` 来定义对象的属性，那么该属性是可以修改或做出其他很多操作的；但如果使用 `Object.defineProperty()`，在不添加 `descriptor` 属性的前提下，该属性就是**immutable（不可修改）的**。

## Descriptor

descriptor 拥有 6 个属性，可以分为两大类：**数据描述符**和**存取描述符**。

很容易猜到，`get` 与 `set` 就是存取描述符，其他的 4 个都是数据描述符。

让我们逐一击破。

### configurable

顾名思义，指的是该属性**是否可以进行配置**，也就是说只有该选项为 `true`，属性值才能被改变或者删除。

该属性默认为 `false`。

### enumerable

该属性译为「数不清的」，因此我们能猜到，该属性和**枚举**相关。

当它为 `true` 时，该属性才会出现在对象的枚举属性中。

默认为 `false`。

### value

不多解释，可以为任何有效的 JavaScript 数据，是该属性对应的值。

默认为 `undefined`。

### writable

描述该属性是否可以修改，与 `configurable` 不同的在于，即使 `writable` 设置为 `false`，该属性也可以做出删除或其他修改以外的操作。该选项只对**赋值运算符**起作用。

默认为 `false`。

### get

配置了该属性的 `getter` 函数。每当我们访问这个属性，就会调用 get。比如 `console.log(obj.a)`。该函数配置的返回值就将会是访问获得的值。

该函数不接受任何参数，但是存在 `this` 对象（但是不确定会指向谁）。

默认值为 `undefined`。

### set

配置该函数的 `setter` 函数。当属性值被修改时，就会调用该函数。

函数接收一个参数，该参数就是即将修改的新值。同样，该函数也存在 `this`。

默认值为 `undefined`。

（完）
