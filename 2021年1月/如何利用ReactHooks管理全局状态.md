# 如何利用 React Hooks 管理全局状态

本文写于 2020 年 1 月 6 日

React 社区最火的全局状态管理库必定是 Redux，但是 Redux 本身就是为了大型管理数据而妥协设计的——这就会让一些小一点的应用一旦用上 Redux 就变得复杂无比。

后来又有了 Mobx，它对于小型应用的状态管理确实比 Redux 简单不少。可是不得不说 Mobx+React 简直就是一个繁琐版本的 Vue。

另外不管是 react-redux 还是 mobx，他们使用的时候都非常复杂。需要你去组件函数或是组件类上修修改改，我个人从审美上来说就不是很喜欢。

后来用了 Angular 之后，我就开始对 SOA 产生好感，ng 的 Service 与依赖注入我都觉得非常漂亮。

Service 是 Angular 的逻辑复用形式，并且解决了共享状态的问题，那 React 的自定义 Hook 可以达到类似的效果嘛？

**可以，并且会比 Angular 更简洁。**

## 自定义 Service

材料：`useXxxx（自定义 hook）`, `createContext`, `useContext`。

我们做一个最简单的计数器吧：一个 button，一个 panel，button 用来增加，panel 用来展示。

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
// services/global.service.ts
interface State {
  count: number;
  handleAdd: () => void;
}

export const GlobalService = createContext<State>(null);
```

我们选择让一个 Context 成为一个 Service，这是因为我们可以利用 Context 的特性来进行状态共享。

然后我们创建一个自定义 Hook，并且在 `Context.provider` 中传入该 Root Service：

```tsx
// services/global.service.ts
export const useRootGlobalService = () => {
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

接着我们再创建一个自定义 Hook，让我们可以随时拿到该 Service：

```tsx
// services/global.service.ts
export const useGlobalService = () => useContext(GlobalService);
```

接着我们就可以运用了

```tsx
// App.tsx
import { GlobalService, useRooGlobalService } from './services/global.service';

const App: React.FC = () => {
  return (
    <GlobalService.Provider value={useRooGlobalService()}>
      <div>
        <Button />
        <Panel />
      </div>
    </GlobalService.Provider>
  );
};

// Button.tsx
import { useGlobalService } from '../services/global.service';

const Button: React.FC = () => {
  const { handleAdd } = useGlobalService();
  return <button onClick={() => handleAdd()}>+</button>;
};

// Panel.tsx
import { useGlobalService } from '../services/global.service';

const Panel: React.FC = () => {
  const { count } = useGlobalService();
  return <h2>{count}</h2>;
};
```

（完）
