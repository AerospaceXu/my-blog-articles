# 理解 Angular 服务

本文写于 2021 年 3 月 29 日

## 什么是服务

angular 的最佳实践推荐业务逻辑要尽量分门别类的抽离为一个个的 Service。

那么到底什么是 Service 呢？

service 意为服务，是一个广义的概念。

例如：饭馆提供的是吃饭服务、澡堂提供的是洗澡服务、学校提供的是授课服务……

那么对于写代码来说，服务是什么呢？

服务一般会包括值/状态、函数或各种特性，在 Angular 中，我们使用一个 class 来包裹这些。

但实际上，服务可以不是一个类，例如 React 的 context，通过闭包得到的返回值也可以是一个服务。

归根结底，可以这么总结：**把一系列高度聚合的代码放到一起，就是一个服务**。

对于前端来说，操心的事情其实有两件：

1. 用户体验（动画、跳转、样式……）
2. 业务逻辑与状态管理

Angular 推荐我们将第二点放到服务中来完成，从而令组件可以专心的负责用户体验。

**比如从服务器获取数据、验证用户输入、日志……都应该放在服务，而不是特定的组件中。**

当我们将这些委托给了各种可注入的服务之后，就可以将其注入到各个组件或者其他服务中进行调用。**让你的代码更具适应性。**

在 Angular 中，服务通常是一个单例。

## 服务写法

_代码来自 Angular 官方文档，有删改_

```ts
// 省略 @Injectable 代码
export class Logger {
  log(msg: any) {
    console.log(msg);
  }

  error(msg: any) {
    console.error(msg);
  }

  warn(msg: any) {
    console.warn(msg);
  }
}
```

在 ng 组件或是 service 的构造函数中注入服务（框架会帮我们自动完成）后即可在其他方法中调用。

```ts
// 省略 @Injectable 代码
export class HeroService {
  constructor(private logger: Logger) {}

  getHeroes() {
    this.getXXXXData().then((res: any) => {
      this.logger.log(`Fetched data: ${res}`);
    });
  }
}
```

### 原理简述

当 Angular 创建组件类的新实例时，框架会查看该组件类的 constructor，得到该组件依赖哪些服务或其它依赖项。

如果发现了某个组件依赖某个服务，框架会**首先检查注入器中是否已经有了那个服务的实例**。如果存在，直接返回；如果不存在，注入器就会使用以前注册的服务提供者来创造一个，并把它加入注入器中，最后把该服务返回。

### 提供服务

服务需要「提供」，才能使用。

#### 1. 在服务中注册

```ts
@Injectable({
  providedIn: "root",
})
export class SomeService {}
```

默认情况下，服务会提供给 `root`，也就是根。这样该服务就可以在任何 module 的组件进行注入。

这种在 `@Injectable` 元数据中注册提供者的方式还让 Angular 能够通过移除那些从未被用过的服务来优化大小。

#### 2. 在 module 中注册

```ts
@NgModule({
  providers: [
   AService,
   BService
 ],
 ...
})
```

你也可以提供给单独的 module，让 service 只能在该 module 使用。

#### 3. 在组件中注册

```ts
@Component({
  selector:    'app-hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ SomeService ]
})
```

当你在组件级注册提供者时，你**会为该组件的每一个新实例提供该服务的一个新实例**！！！

所以不推荐这么做。

## 该在哪里注入服务

Angular 使得我们可以在任何的组件中注入服务，这会使得我们“滥用”注入。

这里我们借用一些 react-redux 中的概念——展示组件和容器组件。

在展示组件中，应该只负责定义自己需要的状态、方法，具体的依赖于外层传入。

在容器组件中，才可以注入服务进行操作。

这样能有效的降低组件对于特定服务的依赖。

（完）
