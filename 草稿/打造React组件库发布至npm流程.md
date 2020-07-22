下文内容包括：

- webpack 配置
- 开发环境配置
- 区分环境配置
- 载入打包工具
- 样式 loader 配置
- 抽离样式文件
- 处理文件引用路径
- js、es 语法兼容
- react 脚手架配置
- typescript 语法依赖
- 组件测试
- npm 发布组件

## 1 创建项目

### 1.1 目录结构

无需优先创建，我们将会按照进度步骤来创建和调整目录。

```
├── config # webpack 配置
│ └── webpack.common.js # 公共部分
│ └── webpack.dev.js # 开发预览环境
│ └── webpack.prod.js # 打包发布环境
├── dist # 组件打包结果目录
├── example # 示例预览目录
│ └── app.js
│ └── index.html
├── src # 组件源码目录
│ └── ts.tsx
│ └── index.js
└── .babelrc # babel 相关配置
└── .gitignore # 指定发布 git 时需要忽略的文件和文件夹
└── .npmignore # 指定发布 npm 时需要忽略的文件和文件夹
└── LICENSE # 版权许可
└── package-lock.json # 无需手动创建，npm@5 及更高版本, 会创建此文件。
└── package.json # 无需手动创建，初始化自动创建。
└── README.md # 文档
└── tsconfig.json # typescript 配置
```

### 1.2 初始化

`npm init -y`

`git init`

### 1.3 webpack 基础配置

`npm install -D webpack webpack-cli`

创建以下目录结构、文件和内容：

```javascript
// 文件：config/webpack.prod.js

const path = require('path')

module.exports = {
  entry: {
    index: path.join(**dirname, '../src/index.js'),
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(**dirname, '../dist/'),
  }
}
```

```javascript
// 文件：src/index.js

export default (count = 0) => `测试: ${count}`;
```

```json
// 文件：package.json

{
  "name": "xxxxx",
  "version": "0.0.1",
  "description": "",
  "main": "dist/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config config/webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "MIT",
  "devDependencies": {
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11"
  }
}
```

运行以下命令，测试运行是否正常，查看结果是否正确：

`~ $ npm run build`

复制代码可以看到在项目根目录下生成了一个 dist 文件夹并有一个 bundle.js 文件。

接下来我们会去分开发环境和生产环境，因为开发环境只提供**测试和预览**功能，而生产环境需要生成引用，所以两个环境需要配置不同参数。

### 1.4 开发环境

`npm install -D webpack-dev-server`

创建以下目录结构、文件和内容：

```javascript
// 文件：config/webpack.dev.js;

const path = require('path');

module.exports = {
  entry: {
    app: path.join(__dirname, '../example/app.js')
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, '../example/')
  },
  devServer: {
    contentBase: path.join(__dirname, '../example/'),
    host: 'localhost',
    port: 3000,
    open: true
  }
};
```

```javascript
// 文件：example/app.js

function component() {
  const element = document.createElement('div');

  // lodash（目前通过一个 script 引入）对于执行这一行是必需的
  element.innerHTML = _.join(['Hello', 'Compontents'], ' ');

  return element;
}

document.body.appendChild(component());
```

```javascript
// 文件：example/index.html

<!doctype html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Compontents</title>
  <script src="https://unpkg.com/lodash@4.16.6"></script>
</head>
<body>
  <div id="app"></div>
  <script src="bundle.js"></script>
</body>
</html>
```

```javascript
// 文件：package.json

{
  ...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config config/webpack.prod.js",
    "start": "webpack-dev-server --config config/webpack.dev.js"
  },
  ...
}
```

运行以下命令，测试运行是否正常，查看结果是否正确：

`~ $ npm start`

复制代码可以看到运行后自动打开了浏览器并访问 localhost:3000 域名。

### 1.5 分离环境

`npm install -D webpack-merge`

复制代码分离生产环境和开发环境，创建一个 common 配置，使用 `webpack-merge` 合并配置。

创建以下目录结构、文件和内容：

```javascript
// 文件：config/webpack.common.js

module.exports = {};
```

```javascript
// 文件：config/webpack.prod.js

const path = require('path')
const merge = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
  mode: 'production', // 生产模式
  entry: {
    index: path.join(**dirname, '../src/index.js'),
  },
  output: {
    filename: 'index.js',
    path: path.resolve(**dirname, '../dist/'),
  },
})
```

```javascript
config/webpack.dev.js

const path = require('path')
const merge = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
mode: 'development', // 开发模式
devtool: 'inline-source-map', // 可以追踪源码中 error 的位置
...
})
```

```javascript
// 文件：src/index.js

export default (count = 0) => `我是生产环境输出的: ${count}`;
```

```javascript
// 文件：package.json

{
...
"scripts": {
  "test": "echo"Error: no test specified" && exit 1",
  "build": "webpack --config config/webpack.prod.js",
  "start": "webpack-dev-server --config config/webpack.dev.js"
},
...
}
```

复制代码
example/app.js

import Test from '../src' // 引入模块和测试

function component() {
...
element.innerHTML = \_.join(['Hello', 'Compontents', Test(100)], ' ')
...
}
...
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ \$ npm start
...

> webpack-dev-server --config config/webpack.dev.js

ℹ ｢wds｣: Project is running at http://localhost:3000/
...
复制代码运行 npm start 后，浏览器访问了 localhost:3000 并且可以看到页面上增加了新内容。
~ \$ npm run build
...

> webpack --config config/webpack.prod.js
> ...
> [0] ./src/index.js 70 bytes {0} [built]
> ...
> 复制代码可以看到上面两段命令运行时会从不同的文件中读取配置，npm start 负责运行，npm run build 负责打包产出。有个问题 npm run build 运行后在 dist 文件夹中生成了 index.js 但旧的 bundle.js 依然存在，实际弃应该被清理，我们在下一节解决它。

1. 打包优化
   npm install -D clean-webpack-plugin
   复制代码 clean-webpack-plugin 在每次构建前清理都会 /dist 文件夹，保证生成的都是用到的文件。

创建以下目录结构、文件和内容：

config/webpack.prod.js

const path = require('path')
const merge = require('webpack-merge')
const { CleanWebpackPlugin } = require('clean-webpack-plugin') // 引入清理插件
const common = require('./webpack.common.js')

module.exports = merge(common, {
mode: 'production',
...
plugins: [
new CleanWebpackPlugin(),
],
})
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ \$ npm run build
复制代码会发现 dist 文件夹中的 bundle.js 被清理了
依赖

1. 加载 css、sass
   npm install -D style-loader css-loader sass-loader node-sass
   复制代码
   创建以下目录结构、文件和内容：

config/webpack.common.js

module.exports = {
module: {
rules: [{
test: /\.(sa|sc|c)ss\$/,
use: [
'style-loader',
{
loader: 'css-loader',
options: {
modules: {
mode: 'local', // 以对象方式引入，
localIdentName: 'myCompontent-[local]', // 类名前缀，创建类名时会加上前缀：myCompontent-A 、myCompontent-B ...
},
},
},
'sass-loader',
],
}],
},
}
复制代码
src/index.scss

.test {
font-size: 18px;
color: white;
background: black;
}
复制代码
src/index.js

import styles from './index.scss'

export default (count = 0) => `<div class="${styles.test}">我是生产环境输出的: ${count}</div>`
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ \$ npm start
复制代码
打开浏览器控制台，可以看到类名自动加了前缀。
新的问题：再次运行 npm run build 会发现，引入的样式文件被打包到 index.js 中并没有被单独分离打包，我们接下来解决这个问题。

2. 抽离样式文件
   npm install -D mini-css-extract-plugin
   复制代码因为生产环境和开发环境依赖关系不同，我们需要移除 webpack.common 中的配置内容，分别配置到生产环境和开发环境中。

创建以下目录结构、文件和内容：

config/webpack.common.js

module.exports = {} // 移除内容
复制代码
config/webpack.prod.js

const path = require('path')
const merge = require('webpack-merge')
const MiniCssExtractPlugin = require('mini-css-extract-plugin') // 引入 css 单独打包插件
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const common = require('./webpack.common.js')

module.exports = merge(common, {
...
module: {
rules: [{
test: /\.(sa|sc|c)ss\$/,
use: [
MiniCssExtractPlugin.loader,
{
loader: 'css-loader',
options: {
modules: {
mode: 'local', // 以对象方式引入，
localIdentName: 'myCompontent-[local]', // 类名前缀，创建类名时会加上前缀：myCompontent-A 、myCompontent-B ...
},
},
},
'sass-loader',
],
}],
},
plugins: [
new CleanWebpackPlugin(),
new MiniCssExtractPlugin(),
],
...
})
复制代码
config/webpack.dev.js

const path = require('path')
const merge = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
...
module: {
rules: [{
test: /\.(sa|sc|c)ss\$/,
use: [
'style-loader',
{
loader: 'css-loader',
options: {
modules: {
mode: 'local',
localIdentName: 'myCompontent-[local]',
},
},
},
'sass-loader',
],
}],
},
...
})
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ \$ npm run build
...
[0] ./src/index.scss 704 bytes {0} [built][1] ./src/index.js 139 bytes {0} [built]
...
复制代码可以看到在命令窗口输出了两条文件有关的命令，会发现在 dist 文件夹中也多了一个 index.css 文件。那么还有其它的静态资源我们要怎么处理呢？比如图片字体等。

3. 处理资源引用
   npm install -D file-loader
   复制代码
   创建以下目录结构、文件和内容：

config/webpack.common.js

module.exports = {
module: {
rules: [{
test: /\.(png|svg|jpg|gif)$/,
loader: 'file-loader',
options: {
outputPath: 'assets', // 打包后资源存放的目录
},
}, {
test: /\.(woff|woff2|eot|ttf|otf)$/,
loader: 'file-loader',
options: {
outputPath: 'assets', // 打包后资源存放的目录
},
}],
},
}
复制代码
src/index.js

找张图片放到 src 目录下 => src/icon.png

import styles from './index.scss'
import Icon from './icon.png'

export default (count = 0) => (
`<div> <img src="${Icon}" /> <div class="${styles.test}">我是生产环境输出的: ${count}</div> <i class="iconfont icon-iconfontcheck"></i> </div>`
)
复制代码
src/index.scss

用 iconfont 做例子，很多 UI 库也会有自己的 Icon 组件。

参考 iconfont.cn 使用说明, 需要的文件分别是：

src/iconfont.eot
src/iconfont.svg
src/iconfont.ttf
src/iconfont.woff
src/iconfont.woff2

.test {
font-size: 18px;
color: white;
background: black;
}

// 请参考 iconfont.cn 使用方法
@font-face {
font-family: "iconfont";
src: url('iconfont.eot?t=1590137292113'); /_ IE9 _/
src: url('iconfont.eot?t=1590137292113#iefix') format('embedded-opentype'), /_ IE6-IE8 _/
url('data:application/x-font-woff2;charset=utf-8;base64,d09GMgABAAAAAAKgAAsAAAAABlgAAAJUAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHEIGVgCCcApcbAE2AiQDCAsGAAQgBYRtBzcbmQXILrBt2JMiITaW4h530No6cgBCBPVjv7337gviEumeRROZRGkkEsRGJlSme6h2OJcZgQNS2c8SgS1eCpxm5ADYEwnL7ifkXg9rr74TMWXL5cZDu0/cO/0T6IPMB5TjXjTWpEldQF0cSIHugVGcSAll3DB2gUu4T6DTrBxhR209IyhWspYF4k5TBBSbi0jJ8m2hWbM3xZOGdnkoQchj+P34VhSxFI3Kajq9bZVRwxu7qM78z7jxCVECOtxAxTYkicvazLEIwbgInZlIi9C+6oM3//++W+zVIdhfZ9V2g2movCeVq77VfIHbmmgcMjfqrcS4o7Nkeexo7WomRThMSTlcT0s7TlOOZ8vnxtxZvXn/2szsjFP0EygIDCYOFWYVPYXIQA3wZ/W+cCgMwWbj5IWrZrsrwesFQ3vq50Pae/IGf0TVsy91jeUqqapqh4vxM7V06sQ/HBgUGmsdOX0htI2M8LXMpKjalslkt9HQZQdNbbvotKX9cJchVITIDmxaBAj9HlH0ekbV74NM9hMNo/7R1B8R6HQdSWd2WQ0tvpmCwEDG4jTWJMcmjofFR/VDoIybAs1qQn4MqK/FcWFeQTHfDjbQOTb4E0oRYwQT6li4DTwGpulglzo6SCxPZcytys8ndS/KkxwLxTtEAQEDZJhoGqaROGzEH83Elz4/BCjGmQS0paemOAZQPm16rFCegh5ku9Hu1XMvz/gmKIowDIERlMOCtYFZwGRyYG79LB2QMHnUESlXlXx7EOlrzFvfbH3dMehk2UrY/qrnzsslFSQDIQAA') format('woff2'),
url('iconfont.woff?t=1590137292113') format('woff'),
url('iconfont.ttf?t=1590137292113') format('truetype'), /_ chrome, firefox, opera, Safari, Android, iOS 4.2+ _/
url('iconfont.svg?t=1590137292113#iconfont') format('svg'); /_ iOS 4.1- _/
}

// 因为在 css-loader 中配置了类名前缀，所以 iconfont 类名需要用 :global 函数包裹，表示不做前缀处理。
:global {
.iconfont {
font-family: "iconfont" !important;
font-size: 16px;
font-style: normal;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
}

.icon-iconfontcheck:before {
content: "\e61b";
}
}
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ \$ npm start
复制代码
打开浏览器控制台，可以看到 icon 相关类名并没有自动加上前缀。
再次运行 npm run build 命令，会发现 dist 文件夹中多出了 assets 文件夹，里面存着所有的静态资源。
提示：在实际开发中静态资源会以 url 地址的方式引入，你会发现引入成熟的组件并不会要求你再去引静态资源。

4. js 语法兼容
   npm install -D babel-loader @babel/core @babel/preset-env core-js
   npm install -D @babel/plugin-proposal-class-properties # 编译 class 类
   复制代码
   创建以下目录结构、文件和内容：

config/webpack.common.js

module.exports = {
module: {
rules: [{
...
}, {
test: /\.js(x?)$/,
exclude: /node_modules/,
loader: 'babel-loader'
}],
},
}
复制代码
.babelrc

{
"presets": [
[
"@babel/preset-env",
{
"useBuiltIns": "usage",
"corejs": 3
}
]
],
"plugins": [
"@babel/plugin-proposal-class-properties"
]
}
复制代码测试

和下节 React 脚手架 合并测试

扩展

1. React 脚手架
   npm install -D @babel/preset-react react react-dom
   复制代码
   创建以下目录结构、文件和内容：

.babelrc

{
"presets": [
[ ... ],
"@babel/preset-react"
],
...
}
复制代码
src/index.js

import React from 'react'
import styles from './index.scss'
import Icon from './icon.png'

export default ({ count = 0 }) => (

  <div>
    <img src={Icon} />
    <div className={styles.test}>我是生产环境输出的: {count}</div>
    <i className="iconfont icon-iconfontcheck"></i>
  </div>
)
复制代码
example/app.js

import React from 'react'
import ReactDom from 'react-dom'
import Test from '../src'

ReactDom.render(<Test count="1000" />, document.getElementById('app'))
复制代码
example/index.html

# 使用 react-dom 渲染页面，删除

# <script src="https://unpkg.com/lodash@4.16.6"></script>

复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

再次运行 npm start 和 npm run build 命令，运行正常无错误。

2. typescript 扩展
   npm install -D typescript @babel/preset-typescript
   复制代码
   创建以下目录结构、文件和内容：

.babelrc

{
"presets": [
...
"@babel/preset-react",
"@babel/preset-typescript"
],
...
}
复制代码测试

和下节 react 脚手架 ts 扩展 合并测试

3. react 脚手架 ts 扩展
   npm install -D @types/react @types/react-dom
   复制代码
   创建以下目录结构、文件和内容：

config/webpack.common.js

module.exports = {
resolve: {
// 定义 import 引用时可省略的文件后缀名
extensions: ['.js', '.jsx', '.ts', '.tsx'],
},
module: {
rules: [{
...
}, {
test: /(\.js(x?))|(\.ts(x?))$/,
exclude: /node_modules/,
loader: 'babel-loader'
}],
},
}
复制代码
tsconfig.json
配置说明

baseUrl：基础路径
esModuleInterop：允许使用 import React from 否则对于没有默认导出的模块，必须使用 import \* as React from
include：设置 ts 处理的具体路径

{
"compilerOptions": {
"baseUrl": "./",
"jsx": "react",
"esModuleInterop": true
},
"include": [
"src/*",
"example/*",
]
}
复制代码
src/ts.tsx

import React from 'react'

const text: string = '测试 typescript'
export default () => <div>{text}</div>
复制代码
src/index.js

import React from 'react'
import styles from './index.scss'
import Icon from './icon.png'
import Ts from './ts'

export default ({ count = 0 }) => (

  <div>
    <img src={Icon} />
    <div className={styles.test}>我是生产环境输出的: {count}</div>
    <i className="iconfont icon-iconfontcheck"></i>
    <Ts />
  </div>
)
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

~ $ npm start
复制代码
可以看到视图又更新啦，惊喜！
~ $ npm run build
...
assets/2c5104bc97e257b036d264a3eb96cb9e.ttf 1.59 KiB [emitted]
assets/2d63f7f318725f04f304f5b2c3402650.png 2.54 KiB [emitted]
assets/494ab9deb2ac6a1decee40a67548b9ad.woff 1.02 KiB [emitted]
assets/a83a4d1296e90999d007661dff12e13e.eot 1.75 KiB [emitted]
assets/a83c905a2a120f6119cdf4e502d7d455.svg 1.14 KiB [emitted]
index.css 1.57 KiB 0 [emitted] index
index.js 5.36 KiB 0 [emitted] index
Entrypoint index = index.css index.js
[0] external {"root":"React","commonjs2":"react","commonjs":"react","amd":"react"} 42 bytes {0} [built][1] ./src/index.scss 704 bytes {0} [built][3] ./node_modules/css-loader/dist/cjs.js??ref--4-1!./node_modules/sass-loader/dist/cjs.js!./node_modules/mini-css-extract-plugin/dist/loader.js!./node_modules/css-loader/dist/cjs.js!./node_modules/sass-loader/dist/cjs.js!./src/index.scss 232 bytes {0} [built][5] ./src/index.js + 2 modules 884 bytes {0} [built]
| ./src/index.js 633 bytes [built]
| ./src/icon.png 87 bytes [built]
| ./src/ts.tsx 154 bytes [built] + 3 hidden modules
Child mini-css-extract-plugin node_modules/css-loader/dist/cjs.js!node_modules/sass-loader/dist/cjs.js!src/index.scss:
Entrypoint mini-css-extract-plugin = \*
[0] ./node_modules/css-loader/dist/cjs.js!./node_modules/sass-loader/dist/cjs.js!./src/index.scss 2.76 KiB {0} [built][3] ./src/iconfont.eot?t=1590137292113 87 bytes {0} [built][4] ./src/iconfont.woff?t=1590137292113 88 bytes {0} [built][5] ./src/iconfont.ttf?t=1590137292113 87 bytes {0} [built][6] ./src/iconfont.svg?t=1590137292113 87 bytes {0} [built] + 2 hidden modules
...
复制代码可以看到依赖信息和资源也打包成功，惊喜！
补充

config/webpack.prod.js

...
module.exports = merge(common, {
...
output: {
...
libraryTarget: 'umd', // 采用通用模块定义，兼容 ES6 的模块系统、CommonJS 和 AMD 模块规范
},
...
externals: {
// 定义外部依赖，将不会将 react、react-dom 打包进去
react: 'react',
'react-dom': 'react-dom',
},
})
复制代码
config/webpack.common.js

const path = require('path')

module.exports = {
resolve: {
// 定义 import 引用时可省略的文件后缀名
extensions: ['.js', '.jsx', '.ts', '.tsx'],
alias: {
// 设置路径别名，用 @ 代示部分路径。例如：import X from '@/X.js'
'@': path.join(\_\_dirname, '../src'),
},
},
...
}
复制代码
tsconfig.json

{
"compilerOptions": {
"baseUrl": "./",
"paths": {
"@/_": ["src/_"] # 上文定义的路径别名 @ 需要在 tsconfig.json 中加入相应配置。
},
...
},
...
}
复制代码
package.json

{
"name": "my-compontent", # 确保包名独一无二，若有重名会报错
"version": "1.0.0", # 确保包版本未被使用
"description": "",
"main": "dist/index.js", # 参照 package.json 标准
"module": "dist/index.js", # 参照新提案允许 JavaScript 生态系统升级使用 ES2015 模块，而不会破坏向后兼容性。
...
}
复制代码
example/app.js

import React from 'react'
import ReactDom from 'react-dom'
import Test from '@'

ReactDom.render(<Test count="1000" />, document.getElementById('app'))
复制代码再次运行 npm start 和 npm run build 命令，运行正常无错误。
测试组件

运行以下命令，测试运行是否正常，查看结果是否正确：

~ $ npm run build
复制代码~ $ npm link # 提示 permission denied 错误，使用 sudo npm link 命令
...
npm WARN sass-loader@8.0.2 requires a peer of sass@^1.3.0 but none is installed. You must install peer dependencies yourself.
npm WARN sass-loader@8.0.2 requires a peer of fibers@>= 3.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN my-compontent@1.0.0 No description
npm WARN my-compontent@1.0.0 No repository field.

audited 917 packages in 4.688s
found 0 vulnerabilities
...
found 0 vulnerabilities
复制代码~ \$ npm link my-compontent # 把打包后的组件引入到 node_modules 中
...
~/my-compontent/node_modules/my-compontent -> ~/my-compontent -> ~/my-compontent
...
复制代码在 node_modules 中查找，可以看到引入了名为 my-compontent 的包

创建以下目录结构、文件和内容：

example/app.js

import React from 'react'
import ReactDom from 'react-dom'
import Test from 'my-compontent' // 引用 my-compontent 组件

ReactDom.render(<Test count="1000" />, document.getElementById('app'))
复制代码测试

运行以下命令，测试运行是否正常，查看结果是否正确：

再次运行 npm start 命令，运行正常无错误。
如果 npm start 运行有错误，在上面命令操作后运行 npm i ，然后再运行一遍 npm link my-compontent 命令。
其它错误 "export 'default' (imported as 'XXX') was not found in 'XXX' 因开发和测试使用相同环境而引起的错误。可以将 npm run build 生成的 dist 文件夹修改为组件的名称，再复制到 node_modules 目录 下，来测试组件。
npm 发包

package.json

{
...
"keywords": ['my-compontent'], # npm 上能搜索到包的关键词
"author": "白告", # 作者
"license": "ISC", # 版权许可
...
}
复制代码
.npmignore

# 指定发布 npm 时需要忽略的文件和文件夹

# npm 默认不会把 node_modules 发上去

config
example
src
.babelrc
.npmignore
.gitignore
package-lock.json
tsconfig.json
npm-debug.log\*
复制代码
运行以下命令，测试运行是否正常，查看结果是否正确：

~ $ npm login # 登录
~ $ npm run build
~ \$ npm publish
复制代码在 npm 上查找一下刚刚我们发布的包，并用 npm i -S my-component 安装此包。
结语
以上内容就是本人发包的全过程啦，记录一下过程和想法。上面每个步骤都可测试，保证每个流程正确和方便错误排查。
如果还有什么问题和建议请留言吧！
心得
学习配置过程对 webpack 会有更好的了解，在以后项目中对 webpack 的配置也能够快速上手，例如：多页面项目、多仓库协同开发项目都需要对 webpack 进行配置。
每天都有新技术的迭代更新，我们需要探索和学习呀！
作业
node 支持读取目录文件，根据目录结构打包输出，实现组件按需加载。

```

```
