# 几种简单的排序算法与 ts 实现

本文写于 2020 年 12 月 29 日

下列排序的规则为：给定一个数组 `[1, 2, 3, ...]`，设计一个函数 `sort(arr: number[])` 接收该数组，并返回一个从小到大排序的新数组。

## 归并排序

我觉得归并排序算得上最简单的一种排序了。

### Step 1

**首先第一步，从小开始。**

如果我们有一个两个元素的数组：`[4, 2]`，我们会如何处理？

```ts
const sort = ([a, b]: [number, number]) => {
  return a > b ? [b, a] : [a, b];
};
```

非常简单。

### Step 2

第二步，如果有两组数组：`[5, 2]`, `[4, 3]`，我们该如何处理？

首先自然是使用第一步的函数为他们分别排序，得到：`[2, 5]` 与 `[3, 4]`。

然后对比两个数组的首位元素，谁比较小就拿走谁：`2 < 3`，然后放入最终结果。

**因为此时两边数组的首位都是各自组里最小的元素了，因此二者之中的更小者，一定是整个数组的最小者。**

接着再对比首位，再拿走小数，直到所有的元素都被拿走为止。

### Step 3

第三步，我们现在解决了两个长度为 2 的数组的排序问题，因此，所有的长度 n 都可以进行排列组合了。

n = 3 = 2 + 1
n = 5 = 2 + 3 = 2 + 2 + 1
n = 6 = 3 + 3 = 2 + 1 + 2 + 1
......

此时我们就可以得到公式了：

```
                            a1, n = 1
sort([a1, a2, ..., an]) = { a1 > a2 ? [a2, a1] : [a1, a2], n = 2
                            merge(sort([a1, ..., an/2]), sort(an/2+1, ..., an)), n >= 3
```

当 n = 1 时不需要排序；n = 2 时我们很容易排序；n >= 3 时我们分别对数组的前半段和后半段排序，然后合并。

这样，一个递归算法就完成了！

### 代码

```ts
const merge = (arr1: number[], arr2: number[]) => {
  const arrLeft = [...arr1];
  const arrRight = [...arr2];
  const res: number[] = [];
  while (arrLeft.length > 0 || arrRight.length > 0) {
    if (
      (arrRight.length === 0 && arrLeft.length >> 0) ||
      arrLeft[0] < arrRight[0]
    ) {
      res.push(arrLeft.shift()!);
    } else {
      res.push(arrRight.shift()!);
    }
  }
  return res;
};
```

这里因为数组的 `shift` 方法返回值必然是 `undefined | number`，但是数学上来说，我们的代码是不可能出现 `undefined` 的，因此加上了 `!` 进行断言。

```ts
const sort = (arr: number[]): number[] => {
  const len = arr.length;
  if (len <= 1) {
    return [...arr];
  } else if (len === 2) {
    return arr[0] > arr[1] ? [arr[1], arr[0]] : [...arr];
  } else {
    const left = Math.floor(len / 2);
    const right = left + 1;
    return merge(sort(arr.slice(0, left + 1)), sort(arr.slice(right)));
  }
};
```

## 快速排序

快速排序的思路是这样的：

我们随便挑选一个数组中的数，然后遍历数组，将比数组小的数丢到数组的左边，比数组大的数丢到数组的右边；然后再在两边做同样的事情，直到长度为 1。

（完）
