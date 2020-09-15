# TS 自学笔记（一）

本文写于 2020 年 5 月 6 日

**日常废话两句**

有几天没有更新了，最近学的比较乱，休息了两天感觉好一些了。这两天玩了几个设计软件，过几天也写篇文章分享分享。

## 为啥要学 TS？

进入正题哈，经常写 JS 的人会特别多的碰到一个 bug：`xxxx is not a function`之类的。

这是为什么呢？

其实就是我们用了一些不属于它的方法。

比如说我们有一个`const a = 250`，然后我们让他`a.filter(()=>{})`。

有人可能会说，那明明是一个 number 类型，我怎么可能给他 `filter` 呢？

我之前就犯过这个错误，我想让一个数组等于另一个被 push 一个新元素数组：

```javascript
let newArr = arr.push('hello');
newArr.filter(() => {});
```

要知道，`arr.push` 的返回值是**新的数组长度！**让一个 number 类型去使用数组方法，自然是会报错的。

好吧,我知道这样似乎非常愚蠢，但是总而言之，在日常写 JS 的报错中，有大把大把的错因都是这种**完全可以避免掉的问题**。

如果是 Java 或是 C#，编辑器在还没运行时就给会你划上红线，不会等到你运行了再去告诉你你错了。

这就是动态语言的毛病。

所以我们需要学习 TypeScript，用它来构建应用，节省时间。_据说用了 TS，能有效减少 80%的 Bug（笑）_

不管这是不是真的，从各个大框架对于 TS 的青睐程度来看，未来几年内，TS 必然会是绝对的热点之一！Angular 早早的就从 Angular.js 变成了 Angular（必须使用 TS）；React 对 TS 极其友好；Vue3 也开始使用 TS 重写。

毕竟，现在已经不只是：

> 能被 JS 改写的终将被 JS 改写

而是：

> 能被 TS 改写的，终将被 TS 改写

## 01 变量类型

JS 是弱类型语言，TS 却不是。TS 拥有这些类型：

```
1. undefined
2. null
3. boolean
4. number
5. string
6. object
7. array

----⬆️JS 有的，⬇️JS 没有的----

8. tuple
9. enum
10. any
11. void
12. never
```

_注：对于 JS 来说，数组就是 Object 类型，并且在 JS 中数据类型并不是小写，而是大写。_

_在 2020 年，JS 的数据类型增加了 BigInt 和 Symbol 两种。_

number、string、boolean 没有什么特别好说的，和 JS 没什么区别，我来写几个我觉得比较重要的。

### 数组的声明

```typescript
let list: number[] = [1, 2, 3];
let list_b: Array<number> = [1, 2, 3];
```

第一种方法不难理解，就是生命一个 number 数组。

第二种方法叫做数组泛型，即`Array<元素类型>`。

**什么叫做泛型呢？**

如果我们有一个函数：

```typescript
function identity(arg: number): number {
  return arg;
}
```

那这个时候，传入的变量就必须是数字。

考虑到可复用性的问题，我们可以使用泛型。

```typescript
function identity<T>(arg: T): T {
  return arg;
}
```

identity 函数叫做泛型（注意，T 不是泛型！），因为它可以适用于多个类型。

**T 是类型变量，它是一种特殊的变量，只用于表示类型而不是值。**它可以帮助我们捕获用户传入的类型，让我们可以对这个类型加以利用。

如果我们不确定传入的参数类型，我们是完全可以通过 any 来实现的，但是 any 就会让我们丢失 T 这一信息。

**enum 是成组常量的好用法！**

```typescript
enum Hello {
  teacher: "老师好",
  classmate: "同学好",
  parents: "爸爸好",
  girlfriend: "刘好"
}
```

一般情况，枚举类型会自动赋给自己常量值：

```typescript
enum Hello {
  teacher, // 0
  classmates, // 1
  parents, // 2
  girlfriend // 3
}
```

我们也可以从中间打断这个赋值：

```typescript
enum Hello {
  teacher, // 0
  classmates, // 1
  parents = 5, // 5
  girlfriend // 6
}
```

接下来会进行顺延。

**从来没有的 Never 值**

`never` 类型表示的是那些永不存在的值的类型，听起来就像个悖论——我如何列举一个不存在的值？

其实`never` 类型可以是抛出的异常，或根本就不会有返回值的函数的返回值类型。

```typescript
function error(message: string): never {
  throw new Error(message);
}

function fail() {
  return error('Something failed');
}

function infiniteLoop(): never {
  while (true) {}
}
```

总体来讲大同小异，TS 补充了一些在别的语言中有过的常见数据类型，相信这一块对于 JS 开发者来说，不会是什么难题。

唯一的难题就是习惯，可不能老是使用 any 类型！

PS: 在命名变量的时候有一个小坑，变量名不能命名为`name`，因为会与 DOM 中的全局 `window` 对象下的 `name` 属性出现重名。

（未完待续）
