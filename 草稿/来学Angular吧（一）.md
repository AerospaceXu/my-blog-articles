# 来学 Angular 吧（一）

_不得不说国内网上关于 Angular 的教程是真的少之又少。_

最近看到一个说法：React、Vue、Angular 关注的层面是递增的。

这个观点认为 React 是前端框架的底层实现；Vue 是集成运用；Angular 则是大范围工程化。

利用 React，我们什么都可以实现，用 Vue 呢可以让我们用熟悉的方案很快去实现，Angular 则是工程化的，可以开箱即用的保持响应和扩展性，进而形成不同职能的工作流程，并且持续集成与交付。

我是认为 Angular 是可以学一下的，因为里面可以接触到一些后端的概念，帮助我们扩展视野。另一方面，我确实在 React 的茫茫生态海中游的有些累了。

## 概述

是 Angular，不是 Angular.js。这是首先需要明确的。

我在搜索学习资料的时候，发现大批大批的资料将 Angular.js 的内容写上了「最新 Angular」的标题名，非常误导人。

Angular 在从版本 1 到 2 的过程中就已经去掉了 js 的后缀。为什么呢？**因为 Angular 全面采用了 TypeScript！**

## 环境搭建

这都要写？自己看文档去。

## 架构概览

当你新建好了一个 Angular 项目之后会发现它很复杂——至少比 React 和 Vue 的项目模板要复杂很多。

但我们首先做的就是摒除杂念，聚焦一点。

因此根据我们的经验，首先该看 src 文件夹，发现果然这里面有入口文件 `main.ts` 和页面基础 html 文件 `index.html`。

然后目光就应该看向 app 文件夹了，因为很明显 asset 和 environment 文件夹都不是我们想要的。

在 app 文件夹中我们可以看到好多好多的文件，但他们的名字开头都是 `app`，大概可以猜到，这些文件组合起来应该就是「根组件」了。

对于一个基础的 Angular 组件可以看作有三个文件：

1. html 模板文件 `xxx.component.html`；
2. CSS 样式文件 `xxx.component.css`；
3. 页面逻辑文件 `xxx.component.ts`。

关于模板文件、样式文件、页面逻辑文件的作用不必多说，基本跟 Vue 是一样的。

在页面逻辑文件 `xxx.component.ts` 中，导出的是一个 `class`，这个 `class` 由一个装饰器进行修饰（装饰器不懂得可以看我之前的一篇关于装饰器的博客）。

这个装饰器的写法是这样的：

```TypeScript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'my-test-angular';
}
```

可以看出来，这个装饰器的作用就是帮我们绑定 html 页面中的元素，并且起到了联系 html 和 css 文件的作用。

这应该都不难。

最大的问题在于这个 `xxx.module.ts` 文件，究竟是干什么的呢？

## 模块 module

Angular 应用是模块化的，每个 Angular 应用都该拥有自己的模块系统——`NgModule`。

每个 Angular 应用都应该至少包含一个 `NgModule` 模块——其实就是 `NgModule` 类。可以称它为「根模块」，我们习惯命名它为 `AppModule` 并放置在 app.module.ts 文件之中。

我们启动项目，其实就是引导这个根模块去启动的。

上面也提到了，`NgModule` 就是一个带有 `@NgModule` 装饰器的类。`@NgModule` 接受一个元数据对象，这个对象的属性是用来描述这个模块的。

具体写法如下：

```TypeScript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

**我们来一个个看。**

`declaration`，他被官网译作「可声明对象表」。也就是属于这个模块的组件、指令需要放在这里。当然，还有一个要放在这里的东西叫做「管道」。但我们目前不知道这是个什么概念，所以先行跳过。

`providers`，本模块向全局服务中贡献的那些服务的创建器，这些服务能被本应用中的任何部分使用。

<!-- TODO: 没看懂 -->

`bootstrap` 是应用的主视图，也被称为根组件——是其他所有组件的祖宗，因此只有根模块（AppModule）才能设置该属性。

按照官网的说法，它的存在为组件提供了编译的上下文环境。

我知道这里可能很多话都不是人话，但慢慢我们会理解的。

官网对于这个文件是这么描述的

（未完待续）
