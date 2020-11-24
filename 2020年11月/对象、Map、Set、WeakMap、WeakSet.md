# 对象、Map、Set、WeakMap、WeakSet

本文写于 2020 年 11 月 24 日

总的来说，Set 和 Map 主要的应用场景分别在于**数据重组**和**数据储存**。Set 是一种叫做「集合」的数据结构，Map 是一种叫做「字典」的数据结构。

## 太长不看版本

### Set

1. 成员不能重复；
2. 只有键值，没有健名，有点类似数组；
3. 可以被遍历，方法有 `add`, `delete`, `has`。

### WeakSet

1. 成员都是对象；
2. 成员都是弱引用，随时可以消失，因此可以用来保存 DOM 节点，不容易造成内存泄漏；
3. 不能遍历，方法有`add`, `delete`, `has`。

## Map

1. 本质上是键值对的集合，类似集合；
2. 是比原生对象更完善的 Hash 数据结构；
3. 可以遍历，方法很多，非常灵活，可以与各种数据格式转换。

## WeakMap

1. 只接受对象作为键名（null 除外），不接受其他类型的值作为键名；
2. 键名所指向的对象，不计入垃圾回收机制；
3. 不能遍历，方法有 `get`, `set`, `has`, `delete`。

---

## 对象与 Map

在 ES6 之前，我们经常会使用对象来保存结构化的数据，例如：`{ 1: 'Xhy', 2: 'Hyx', 3: 'Yxh' }`。

但这样做其实缺陷比较大，因为你必须使用字符串作为 key 值，即使我上面的代码使用的是 `number` 类型，但是 JS 一样会隐式的将其转换为 `string` 类型。

所幸在 ES2015 标准中，我们拥有了 Map 类型。Map 可以接受**任意类型**的值作为 key，包括 `boolean` 类型，甚至一个对象也可以成为 Map 的 key 值！**Map 是一种比原生 JS 对象更完善的 Hash 结构。**

## Map

Map 其实就是其他编程语言中常说的：字典。

`Map` 是一个构造函数，因此我们通过 `new Map()` 来实例化一个 Map 对象。

- `Map.prototype.set(key, value)` 新增元素；
- `Map.prototype.get(key)` 读取元素，读取不到就返回 `undefined`；
- `Map.prototype.has(key)` 查询元素是否存在，返回 `boolean` 值；
- `Map.prototype.delete(key)` 删除元素，并返回 `boolean` 值反馈是否删除成功；
- `Map.prototype.clear()` 清除 Map 内的所有元素；
- `new Map([iterable])`，Map 可以在实例化时接收一个可迭代对象，将其转化为 Map 结构；
- `Map.size` 返回 Map 的长度

Map 是可遍历的数据结构，拥有三个遍历器生成函数和一个遍历方法。

- `Map.prototype.keys()` 返回 key 的遍历器；
- `Map.prototype.values()` 返回值的遍历器；
- `Map.prototype.entries()` 返回所有成员的遍历器；
- `Map.prototype.forEach()` 遍历 Map 的所有成员。

注意，**Map 的遍历顺序就是插入顺序**。
