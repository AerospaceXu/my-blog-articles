# 如何利用 React Hooks 管理全局状态

本文写于 2020 年 1 月 6 日

React 社区最火的全局状态管理库必定是 Redux，但是 Redux 本身就是为了大型管理数据而妥协设计的——这就会让**一些小一点的应用一旦用上 Redux 就变得复杂无比**。

后来又有了 Mobx，它对于小型应用的状态管理确实比 Redux 简单不少。可是不得不说 Mobx+React 简直就是一个繁琐版本的 Vue。所以我也不太喜欢，不如直接用 Vue3。

总而言之，不管是 react-redux 还是 mobx，他们使用的时候都**非常复杂**，甚至需要你去组件函数或是组件类上修修改改，从审美角度上来说就令人不太喜欢。

直到后来某一天用了 Angular，我就开始对 SOA 产生好感，ng 的 Service 的写法与依赖注入控制反转着实惊艳到了我。

Service 是 Angular 的逻辑复用方法，并且解决了共享状态的问题，那 React 的自定义 Hook 可以达到类似的效果嘛？

**可以，并且会比 Angular 更简洁！！！**

## 什么是 Service

我们先来想一下，Service 到底是什么？

- Service 包含 n 个方法；
- Service 包含有状态；
- Service 应该是个单例。
- 这些方法与状态应该是**高度整合**的，一个 Service 解决的是一个模块的问题。

例如下面这个负责 Todo List 记录的 Service：

```ts
class TodoRecordService {
  private todoList: Record[] = [];

  get getTodoList() {
    return this.todoList;
  }

  public addRecord(newRecord: Record) {
    this.todoList.push(newRecord);
  }

  public deleteRecord(id: string) {
    this.todoList = this.todoList.filter((record) => record.id !== id);
  }

  public getRecord(id: string) {
    const targetIndex = this.todoList.findIndex((record) => record.id === id);
    return { index: targetIndex, ele: this.todoList[targetIndex] };
  }
}
```

## 自定义 Service

那我们用 React 如何实现一个状态共享的单例呢？

使用 `Context` 与 `useContext` 即可。

接下来我们做一个最简单的计数器吧：一个负责计数的 button，一个负责显示当前数值的 panel。

```tsx
const App: React.FC = () => {
  return (
    <div>
      <Button />
      <Panel />
    </div>
  );
};
```

然后我们来定义我们的 Service：

```tsx
interface State {
  count: number;
  handleAdd: () => void;
}

export const CountService = createContext<State>(null);
```

我们选择让一个 Context 成为一个 Service，这是因为我们可以**利用 Context 的特性来进行状态共享**，达到单例的效果。

但是光这样还不行，我们想让 `count` 拥有响应性，就必须使用 `useState`（或者其他 hook）来创建。

因此需要一个自定义 Hook，并且在 `Context.Provider` 中传入 `Provider` 的 `value` 值：

```tsx
interface State {
  count: number;
  handleAdd: () => void;
}

export const CountService = createContext<State>(null);

export const useRootCountService = () => {
  const [count, setCount] = useState<number>(0);
  const handleAdd = useCallback(() => {
    setCount((n) => n + 1);
  }, []);

  return {
    count,
    handleAdd,
  };
};
```

那么在组建中，我们如何使用 Service 呢？

非常简单：

```tsx
const App: React.FC = () => {
  const countService = useContext(CountService);

  return <div>{countService.count}</div>;
};
```

所以计数器的完整代码应该这么写：

```tsx
import { CountService, useRootCountService } from './service/count.service';

const App: React.FC = () => {
  return (
    <CountService.Provider value={useRooCountService()}>
      <div>
        <Button />
        <Panel />
      </div>
    </CountService.Provider>
  );
};

// Button.tsx
import { CountService } from '../services/global.service';

const Button: React.FC = () => {
  // 注意，此处是故意写复杂了，是为了凸显跨组件状态管理的特性
  const countService = useContext(CountService);
  return <button onClick={() => countService.handleAdd()}>+</button>;
};

// Panel.tsx
import { CountService } from '../services/global.service';

const Panel: React.FC = () => {
  const countService = useContext(CountService);
  return <h2>{countService.count}</h2>;
};
```

## hooks 与 Service

对于小组件而言，刚刚的写法已经足够了。

但是要知道，**Service 是高度集中的某个模块的状态与方法**，我们不能保证 Service 的方法可以直接用到组件的逻辑中去。

所以需要我们在组件内部对于逻辑进行二次拼装。

但是**把逻辑直接写到组件里面是一件非常恶劣的事情**！！！

幸好，React 有了 hooks 让我们去抽离逻辑代码。

```tsx
const useLogic1 = () => {
  // 在 hook 中获取服务
  const xxxService = useContext(XxxService);
  // ...
  const foo = useCallback(() => {
    // ...
    xxxService.xxxx();
    // ...
  }, []);

  return {
    // ...
    foo,
  };
};

const SomeComponent: React.FC = () => {
  // 复用逻辑
  const { a, b, foo } = useLogic1(someParams);
  const { c, bar } = useLogic2();

  return (
    <div>
      <button onClick={() => bar()}>Some Operation</button>
    </div>
  );
};
```

这种形式的组件，便是我们的目标。

（完）
