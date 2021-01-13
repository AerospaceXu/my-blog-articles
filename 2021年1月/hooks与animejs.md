# hooks 与 animejs

animejs 是现如今非常不错的一个 js 动画库。我们将其与 React Hooks 融合，使它更方便的在 React 中使用。

**最终效果：**

```tsx
const Animate: React.FC = () => {
  const { animateTargetRef, animationRef } = useAnime({
    translateX: 300,
    loop: true,
    duration: 2000,
    autoplay: false,
  });

  useEffect(() => {
    setTimeout(() => {
      animationRef.current?.play?.();
    }, 3000);
  }, [animationRef]);

  return (
    <div
      ref={animateTargetRef}
      style={{ width: '100px', height: '100px', backgroundColor: 'black' }}
    />
  );
};
```

首先看看 animejs 在一般的 JS 和 HTML 中如何使用：

```html
<div class="xxx"></div>
```

```js
import anime from 'animejs';

const animation = anime({
  translateX: 300,
  loop: true,
  duration: 2000,
  autoplay: false,
  targets: '.xxx',
});

animation.play();
```

但是在 React 中，我们不想要到处写这些玩意儿。我们喜欢 hooks！

所以我们可以封装一个 `useAnime` hook。

第一步，我们可以引入 AnimeParams，让我们的 hook 拥有代码提示：

```ts
import anime, { AnimeParams } from 'animejs';

export const useAnime = (props: AnimeParams) => {
  anime(props);
};
```

然后我们通过 `useRef` 将 anime 的 targets 绑定，并且暴露出去，供其他地方使用。

```ts
//...
const animateTargetRef = useRef<any>(null);

useLayoutEffect(() => {
  if (!animationTargetRef.current) {
    console.warn('please bind animation target ref');
    return;
  }
  animate({
    ...props,
    targets: [animationTargetRef.current],
  });
}, [props]);

return { animateTargetRef };
//...
```

通过观察发现，animate 返回的是 `AnimeInstance` 类型，我们从 animejs 中导入：

```ts
import anime, { AnimeParams, AnimeInstance } from 'animejs';

// ...

const animationRef = useRef<AnimeInstance>();

// ...
animationRef.current = animate({
  ...props,
  targets: [animationTargetRef.current],
});
// ...

return { animateTargetRef, animationRef };
```

这样就轻松完成了 `useAnime` 的封装。

完整代码：

```ts
import anime, { AnimeParams, AnimeInstance } from 'animejs';
import { useRef, useLayoutEffect } from 'react';

export const useAnime = (props: AnimeParams = {}) => {
  const animateTargetRef = useRef<any>();
  const animationRef = useRef<AnimeInstance>();

  useLayoutEffect(() => {
    if (!animateTargetRef.current) {
      console.warn('please bind the anime ref while useAnime');
      return;
    }
    animationRef.current = anime({
      ...props,
      targets: [animateTargetRef.current],
    });
  }, [props]);

  return { animateTargetRef, animationRef };
};
```

（完）
