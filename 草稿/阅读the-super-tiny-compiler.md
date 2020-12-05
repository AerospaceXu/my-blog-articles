# 阅读 the-super-tiny-compiler

<!-- TODO: 本文写于 2020 年 -->

编译、编译器、编译原理等等编译相关的词汇大家应该常常听到。到底什么是编译呢？

以前端为例：Babel 将 ES6 转为 ES5；SCSS/Stylus 编译为 CSS；ESLint。**这都是编译原理的领域。**

现在你应该大概能理解编译到底有什么用了吧。

其流程有这么几步：

1. **Parsing（解析）**，这一步我们会将源代码的逻辑抽象出来；
2. **Transformation（转换）**，操作刚刚抽象的逻辑，做一些我们想做的事情；
3. **Code Generation（生成）**，基于转换后的结果，生成新的代码。

```JS
// ES6
let a = 1;

// Babel ES6 to ES5
"use strict";

var a = 1;
```

这是一段 ES6 代码，由 Babel 编译为了 ES5。

那他是怎么变的呢？难道是字符串替换？我们需要下面四样武器：

1. tokenizer 词法分析器，可以将原始代码分词；
2. parser 语法解析器，把分词转化为 AST（抽象语法树）；
3. transformer 转换器，对原始代码的 AST 进行转换，得到目标代码的 AST；
4. generator 代码生成器，将目标代码的 AST 转化为具体代码。

好吧，这四个词可能让大家一头雾水，让我们慢慢来。

借用 [the-super-tiny-compiler](https://github.com/jamiebuilds/the-super-tiny-compiler) 的例子，我们将两个 Lisp 风格的函数转化为 C 风格。

| 算式 \ 语言 | Lisp                   | C                      |
| ----------- | ---------------------- | ---------------------- |
| 2 + 2       | (add 2 2)              | add(2, 2)              |
| 4 - 2       | (subtract 4 2)         | subtract(4, 2)         |
| 2 + (4 - 2) | (add 2 (subtract 4 2)) | add(2, subtract(4, 2)) |

## Parsing 解析

Parsing 一般会分为两个阶段：**词法分析（Lexical Analysis）**和**语法（Syntactic）分析**。

这两个词看起非常像，但是并不一样。

- Lexical Analysis 分析原始代码，将其分割为很多个数组，这些数组称为 **Token**，它由一些代码语句的碎片组成，可以是数字、标签、标点符号、运算符或者任何东西都可以。
- Syntactic Analysis 分析 Token，将其转化为更抽象的表示（比如一个对象），这种表示代表了原始代码中的每一个片段以及它们之间的关系，这称为 Intermediate Representation（中间表示），或者说是 AST。

对于 `(add 2 (subtract 4 2))` 这行代码来说，它的 Token 可能是这样的：

```js
[
  { type: 'paren', value: '(' },
  { type: 'name', value: 'add' },
  { type: 'number', value: '2' },
  { type: 'paren', value: '(' },
  { type: 'name', value: 'subtract' },
  { type: 'number', value: '4' },
  { type: 'number', value: '2' },
  { type: 'paren', value: ')' },
  { type: 'paren', value: ')' },
];
```

AST 可能是这样：

```js
{
  type: 'Program',
  body: [{
    type: 'CallExpression',
    name: 'add',
    params: [{
      type: 'NumberLiteral',
      value: 2
    }, {
      type: 'CallExpression',
      name: 'subtract',
      params: [{
        type: 'NumberLiteral',
        value: 4
      }, {
        type: 'NumberLiteral',
        value: 2
      }]
    }]
  }]
}
```

这里我们就能看懂了，AST 其实就是一个对象，因为对象我们更易于处理，能带给我们更多的信息。

（完）
