# Vue3 与依赖注入

本文写于 2021 年 2 月 19 日

在 React 中，我们可以通过 context 与 useContext 实现单例、注入……等诸多特性。

详细请看上一篇文章：[如何利用 React Hooks 管理全局状态](https://www.cnblogs.com/xhyccc/p/14242492.html).

例如：

```tsx
const SomeService = createContext(null);

const useRootSomeService = () => {
  const [n, setN] = useState(0);

  const add = useCallback(() => {
    setN((number) => number + 1);
  }, []);

  return { n, setN };
};

const App: React.FC = () => {
  return (
    <SomeService.Provider value={useRootSomeService()}>
      <Apple />
    </SomeService>
  )
};

const Apple: React.FC = () => {
  const someService = useContext(SomeService)
};
```

**那么 Vue3 可以吗？**

可以！！！只需要利用 Vue3 的 provide 与 inject API 即。

## 创建一个 Service

```ts
import { Ref, ref } from "vue";

class ChatService {
  static INJECT_KEY: string;

  conversations: Ref<Conversation[]>;

  constructor() {
    this.conversations = ref([]);
  }
}
```

这样我们只需要在组件中实例化该 class，就可以拥有响应性的 conversations 属性了。

但是我们不满足于如此，我们还想要在任何地方，都可以使用 `const chatService = inject(ChatService);` 来拿到服务的单例！

## provide/inject

vue3 提供给我们的 provide API 可以在任意组件中使用：`provide(key, variable);`

使用后该组件的自组件的任何位置都可以利用 inject API 拿到刚刚注入的变量：`const v = inject(key);`。

因此我们可以这么封装一下：

```ts
import { provide as vueProvide, inject as vueInject } from "vue";

export const createInjectKey = () => {
  const randomNumber = Math.round(Math.random() * 10 ** 8).toString();
  return randomKey;
};

interface ServiceType<T> {
  new (): T;
  INJECT_KEY: string;
}

export function provide<T>(Service: ServiceType<T>) {
  const key = createInjectKey();
  Service.INJECT_KEY = key;
  vueProvide(key, new Service());
}

export function inject<T>(Service: ServiceType<T>): T {
  const service = vueInject<T>(Service.INJECT_KEY);
  if (!service) {
    console.error("You have to provide service first!!!");
  }
  return service!;
}
```

这样一来，我们只需要写一个拥有 static INJECT_KEY 属性的 class，就可以在组件树顶端使用 `provide(xxxService)`，然后再在任意位置调用 `inject(xxxService)` 来获取服务单例了！

（完）
