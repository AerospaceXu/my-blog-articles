# React 与 Hooks 如何使用 TypeScript 书写类型？

本文写于 2020 年 9 月 20 日

## 函数组件与 TS

对于 Hooks 来说是不支持使用 class 组件的。

如何在函数组件中使用 TS 呢？

首先定然是函数的类型，我们需要告诉 TS，这个函数他是个 React 组件。

如果是 JavaScript，我们会这么写函数组件：

```JavaScript
import React from 'react';

const MyComponent = () => {
  return <h1>你好，世界</h1>
}
```

但如果是 TypeScript，我们就应该加上类型了。

React 为我们提供了一个叫做 `React.FunctionComponent` 的类型，学过小学二年级英语的同学应该都知道，这个类型是 `React.函数组件` 的意思。如果你觉得这个名字太长了不好写，React 也提供了一个简单版本：`React.FC`

```TypeScript
import React from 'react';

const MyComponent: React.FC = () => {
  return <h1>你好，世界</h1>
}
```

如果我们希望它能接收到 Children 该怎么写呢？直接写就可以了。

```TypeScript
const MyComponent: React.FC = (props) => {
  return (
    <div>
      {props.children}
    </div>
  )
}
```

但这种情况是不可以接受额外的参数的。我们必须要声明我们函数需要接收哪些参数。

```TypeScript
interface Props {
  title: string;
  msg: string;
  list: number[];
}
```

然后将这个 Props 接口提供给 `React.FC`，写成：`React.FC<Props>`。

此时我们函数接收的参数 `props` 除了函数组件默认可以接收的一些参数，例如 `children` 之外，还可以接收 `Props` 接口声明的参数了。

## useState

useState 的类型声明如右边：`const [count, setCount] = useState<number>(0)`。

## useRef

useRef 我们通常用来管理 DOM，但也可以用用来管理其他的变量。

这里就说一下取 HTML DOM 元素的类型：`HTMLInputElement` 等等即可。

（完）
