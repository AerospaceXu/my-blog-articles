# React入门

> 本文基于React官网案例：温度转换。

React很火，蛮多人说他是框架的，但其实官网上一排大字写着：**用于构建用户界面的 JavaScript 库**。

我也觉得React是个库，他真的算是非常轻便简洁，对于整个项目的侵入性很小很小。

npm中，下载量统计react远高于vue和angular，我觉得这可能就是原因——React的对整个项目几乎没有影响。

今天看了一晚上官方文档，深有感触，还是国人自己写的Vue框架的文档好，原生中文。相比之下，React的这个翻译简直有点惨不忍睹！

好了，接下来我们来看一下例题：

> 需要两个input输入框，一个输入华氏度，一个输入摄氏度。
> 
> + 要求1：当你在一个输入框输入的时候，另外一个输入框也会相应的显示出换算后的结果；
> + 要求2：如果超过100摄氏度，就会显示水开了，低于100摄氏度，就显示水没开。

React最基础的是两个包：`react`与`react-dom`。

我们接下来的代码统一基于：

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
```

接下来再看一丢丢代码，这些代码会让你很快明白React是如何工作的：

**html**
```html
<div id='root'></div>
```

**js**
```javascript
ReactDom((<h1>Hello world!</h1>), document.querySelector(#root))
```

----

**解题**

先思考：React是不存在双向绑定的，所以说我们必须要想办法让值流动起来，可是怎么流动起来呢？

简单来说，如果我们让子组件所有的数据，都是父组件传入的，并且子组件不会进行任何修改数据的操作，因为子组件就算修改了数据，也传不出来。

然后让子组件**触发父组件的函数**，从而修改父组件的State，这样，再次传入子组件的数据便是更新过的值了。

```javascript
class TempInput extends React.Component {
  constructor(props) {
		super(props)
		this.state = {}
  }

  handleChange = (e) => {
    // 这里就是父组件传入的函数
    // 让摄氏度是摄氏度的函数、华氏度是华氏度的函数
		this.props.onTemperatureChange(e.target.value)
	}

  render() {
		const temperature = this.props.temperature
		const scale = this.props.scale
		return (
			<fieldset>
				<legend>请输入{scaleNames[scale]}的数值：</legend>
				<input value={temperature} onChange={this.handleChange} />
			</fieldset>
		)
	}
}
```