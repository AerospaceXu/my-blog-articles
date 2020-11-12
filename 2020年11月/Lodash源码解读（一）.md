# Lodash 源码解读（一）

<!-- TODO: 本文写于 2020 年 11 月 -->

Lodash 是一个著名的 JS 库，没有第三方依赖，是一个意在提高开发者效率、提升 JS 代码性能的 JS 库。并且它的核心代码只有 4kb，十分精简。

例如：深拷贝、数组拆分……简单来说，Lodash 就是封装了一些常用的，但是 JS 自己又没有的，或是实现的不好的方法。

所以我想要来研究它的源码实现，并将有价值的东西记录出来。

## chunk 方法

`chunk` 方法用于拆分数组。

`chunk(arr, size)` 将 arr 数组拆分为长度为 size 的多等份，如果剩下的不够分一份，剩余的元素便会组成一个区块。

例如：

```js
// 够均分
chunk([3, 1, 4, 1, 5, 9, 2, 6, 2], 3);
/**
 *  [
 *    [3, 1, 4],
 *    [1, 5, 9],
 *    [2, 6, 2]
 *  ]
 */

 // 不够均分
 chunk([3, 1, 4, 1, 5, 9, 2, 6, 2], 4);
/**
 *  [
 *    [3, 1, 4, 1],
 *    [5, 9, 2, 6],
 *    [2]
 *  ]
 */
```

我们先不急着看源码，可以猜测一下如何实现这个效果。

### 猜测

首先第零步，我们的函数需要接收两个参数，`arr: any[]`（目标数组）与 `size: number`（每份长度）。

为了方便，我们先假设用户一定会传入正确类型的参数。

**步骤 1：得到数组的数组元素个数**

我们最终会将数组平分为数组数组：`[[], [], []]`，所以外层数组的元素个数是我们首先需要得到的。

因为是平均分，所以我们需要将 `arr.length / size`，并且向上取整来得到最后的数组长度。

**步骤 2：**

### 源码

`chunk` 函数的源码如下所示：

```js
function chunk(array, size = 1) {
  // 判断 size 是否大于零，如果小于零，则 size=0，函数返回的就是一个空数组
  size = Math.max(toInteger(size), 0)
  // 不太推荐 ==，应该全部使用 ===
  // ES6 可以直接判断 Array.isArray()
  const length = array == null ? 0 : array.length
  if (!length || size < 1) {
    return []
  }
  let index = 0
  let resIndex = 0
  const result = new Array(Math.ceil(length / size))

  while (index < length) {
    result[resIndex++] = slice(array, index, (index += size))
  }
  return result
}
```
