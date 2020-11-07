# 使用 ESLint, Prettier, Husky, Lint-staged 提升你的项目规范

本文写于 2020 年 11 月 7 日

大家应该都知道 ESLint 与 prettier，他们的用途分别在于约束代码和美化代码格式。

但我们并不能保证每次提交代码之前我们的项目都执行过了 ESLint 与 prettier，所以我们需要 Git Hook，它能让我们在 git 操作的各个阶段进行一些自定义的操作。

例如在 commit 时，我们会在 commit 之前执行 ESLint 与 prettier，如果执行出错，git 就会停止 commit 操作。

本文介绍的流程为：

1. 利用 ESLint 和 Prettier 来提升代码质量；
2. 套用如 Airbnb Style Guide 这类大型团队的 Style Guide；
3. 使用 `yarn/npm run lint` 来格式化所有代码；
4. 使用 Husky 及 Lint-staged 在 git commit 之前测试代码品质。

## Prettier

首先介绍 Prettier，一个代码格式化工具。

Prettier 主要用来**整理我们的代码格式**。它刻意**不提供**太多的客制化功能，目的是不让开发团队花太多时间在讨论该用怎样的 Style，而能够有个一致的设计规范。

即使使用上 Prettier 绝大多数的定制选项，你的 JSON 文件也许也就只有 10 行。

Prettier 提供的另一项好处就是安装后即可上手，要安装只需要：`yarn add --dev prettier`，然后 `./node_modules/.bin/prettier --write file-path` 即可格式化文件。

官网写的是 `--write .` 可以格式化所有文件，但在尝试后发现不太行，我们需要使用通配符来配置：`./node_modules/.bin/prettier --write src/**`。

如果想要定制一些选项，可以创建 `.prettierrc.json` 文件，具体配置可以在官网查询，只有十几项。如果存在想忽略的文件夹，则可以新建一个 `.prettierignore` 文件夹。

## ESLint

Linter 是一种静态程式分析的工具，能够帮助使用者找出程式中不符合团队定义之规则的代码。

对于 Javascript 这种动态的、弱类型的语言，如果能够加上一个在写程式时就提醒我们，哪一段代码在执行时可能会出问题的工具，可谓百利而无害！

**ESLint 跟 Prettier 的区别在于：Prettier 旨在统一「代码样式」，而 ESLint 则重视「代码质量」。**

_例如在 ES6 环境中使用 `const/let` 而非 `var`、移除未被使用而无意义的 `import`、若是 `function` 的循环复杂度过高则需要调整设计等。_

eslint 的另一个好处是：我们可以使用别人设定好的规则。

以这几年最流行的 Style Guide 为例：为 Github、MongoDB 等知名公司所使用的 Javascript Standard Style；Airbnb 所使用的 Airbnb Style Guide；以及 Google 的 Google Style Guide。

_我比较喜欢 Airbnb 的 Style。_

首先，可以安装 eslint：`yarn add eslint`，然后可以使用 `npx eslint --init`。一步步跟随者他的引导，选择 Airbnb 的代码样式。

eslint 有两个常用的命令：`eslint --fix file-path` 与 `eslint file-path`。前者是修复可以自动修复的错误，后者是寻找是否存在 eslint 可以检查出得错误。

## 加入 scripts

这个时候我们的流程应该是什么呢？

1. 首先使用 prettier **格式化**代码；
2. 然后使用 eslint 修复可以**自动修复**的错误；
3. 最后再使用 eslint **查看哪些文件存在错误**，我们去一一进行**修改**。

所以，我们可以将这些操作加进 `package.json` 的 `scripts` 之下：

```json
"scripts": {
  "lint": "./node_modules/.bin/prettier --write 'src/**' && ./node_modules/.bin/eslint --fix 'src/**' && ./node_modules/.bin/eslint ."
}
```

## Husky 与 Lint-staged

当我们有了 Prettier 和 ESLint，在开发时已经能为我们避免掉许多不符合要求的代码了。或是当我们拿到一个新项目的时候，也可以通过这些步骤去格式化整个工程。

但就像文章开头说的那样，我们还需要 git hook 来帮助我们在 git 操作时进行操作——可以使用 Husky。

Husky 可以让我们在 git commit 或 git push 时执行一些动作，而 Lint-staged 则让我们可以 lint 那些已经 git add 的文件。

这两者的组合能够在我们真正 commit 之前就检查我们的代码是否符合我们的要求，同时可以主动帮我们修好一些能够修的错误。

`yarn add -D husky lint-staged`

接着在根目录创建 `.huskyrc.json`，写入：

```json
{
  "hooks": {
    "pre-commit": "lint-staged"
  }
}
```

然后再在根目录创建 `.lintstagedrc.json`，来检查并修正文件：

```json
{
  "*.+(ts|js|tsx|jsx)": ["prettier --write", "eslint --fix"],
  "*.+(json|css|md)": ["prettier --write"]
}
```

如此一来，你每次 commit 之前，都会对文件执行操作了。

（完）
