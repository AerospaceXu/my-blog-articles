# Git Commit 规范

本文写于 2021 年 2 月 1 日

话不多说，格式如下：（_该格式是 Google 前端框架 Angular 的项目 commit 规范_）

```
<type>(<scope>): <subject>
<BLANK LINE>
  <body>
<BLANK LINE>
<footer>
```

## 1 格式详解

每次提交，Commit message 都需要包括三个部分：Header，Body 和 Footer，三个部分之间用空行分割。

提交信息的任何一行都**不能超过 100 字符**，这样这些信息就可以在 github 和其他 git 工具上更容易阅读。

### 1.1 header

Header 部分只有一行，包括三个字段：type（必需）、scope（可选）和 subject（必需）。

#### 1.1.1 type

type 的作用是说明该 commit 的类别，只允许使用下面 7 个词来进行标示：

```
feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动
```

如果 type 为 feat 和 fix，则该 commit 将肯定出现在 Change log 之中。其他情况（docs、chore、style、refactor、test）由你决定，要不要放入 Change log，建议是不要。

#### 1.1.2 scope

scope 用于说明 commit 影响的范围。

比如数据层、控制层、视图层等等；或者说是某分支、某模块……都可以做为 scope，视项目不同而不同。

#### 1.1.3 subject

**subject 是 commit 目的的「简短描述」，不超过 50 个字符。**

- 以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes；
- 第一个字母小写；
- 结尾不加句号。

### 1.2 Body

Body 部分是对本次 commit 的「详细描述」，可以分成多行。

下面是一个范例。

```
More detailed explanatory text, if necessary. Wrap it to
about 72 characters or so.

Further paragraphs come after blank lines.

- Bullet points are okay, too
- Use a hanging indent
```

有两个注意点：

1. 使用第一人称现在时，比如使用 change 而不是 changed 或 changes；
2. 应该说明代码变动的动机，以及与以前行为的对比。

### 1.3 Footer

Footer 部分只用于两种情况。

#### 1.3.1 不兼容变动

如果当前代码与上一个版本不兼容，则 Footer 部分以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动理由和迁移方法。

```
BREAKING CHANGE: isolate scope bindings definition has changed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }

    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```

#### 1.3.2 关闭 Issue

如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue 。

```
Closes #234
```

也可以一次关闭多个 issue 。

```
Closes #123, #245, #992
```

### 1.4 Revert

还有一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以 `revert:` 开头，后面跟着被撤销 Commit 的 Header。

```
revert: feat(pencil): add 'graphiteWidth' option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

**Body 部分的格式是固定的**，必须写成 `This reverts commit <hash>.`，其中的 hash 是被撤销 commit 的 SHA 标识符。

如果当前 commit 与被撤销的 commit，在同一个发布（release）里面，那么它们都不会出现在 Change log 里面。如果两者在不同的发布，那么当前 commit，会出现在 Change log 的 Reverts 小标题下面。

（完）
