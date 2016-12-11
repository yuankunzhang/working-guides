# Git Commit Message 书写规范
## 目录
* [关于](#关于)
* [目的](#目的)
  * [生成 changelog](#生成-changelog)
  * [过滤 commit](#过滤-commit)
  * [提供更多历史信息](#提供更多历史信息)
* [格式](#格式)
  * [头部](#头部)
  * [正文](#正文)
  * [尾部](#尾部)
  * [撤销](#撤销)
* [示例](#示例)
* [工具](#工具)
  * [Commitizen](#commitizen)
  * [validate-commit-msg](#validate-commit-msg)
  * [conventional-changelog](#conventional-changelog)

## 关于
Git 要求每次提交代码，都要撰写一段 commit message，否则不允许提交。

Commit message 应该简洁清晰，说明本次提交的目的。但是，如果缺乏约束，很容易写出混乱或者无意义的内容。

目前，社区中有多种 commit message 的写法规范。其中，[AngularJS 规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md) 使用最广范，配套工具也最完善。本文档详细描述了该规范。

## 目的
本规范约束了格式化 commit message 的方式，以期达到如下目的。

1. 可以直接通过 commit message 生成 changelog；
2. 可以过滤某些 commit，便于快速查找信息；
3. 可以提供更丰富的历史信息，便于快速浏览历史。

### 生成 changelog

借助工具，可以直接通过 commit message 生成 changelog。

每个版本的 changelog 包含三部分。

* Features
* Bug fixes
* Breaking changes

良好的格式帮助快速比较两个版本之间的差异。

例如，列举自上个版本以来的所有变更：

```
git log <last release> HEAD --pretty=format:%s
```

自上个版本以来的所有新添加的特性：

```
git log <last release> HEAD --grep feature
```

### 过滤 commit

某些 commit 并不如另一些来的重要，例如，仅是删除空行或者添加缩紧。良好的格式帮助过滤 commit。

例如，进行 bisect 时，忽略仅改动代码格式的 commit：

```
git bisect skip $(git rev-list --grep irrelevant style HEAD)
```

### 提供更多历史信息

简洁清晰的 commit message 提供更多历史信息，方便快速浏览。

例如，下面的命令显示上个版本以来的所有变动，每个 commit 占据一行。只看行首，就应该让所有人理解代码的演进历史。

```
git log <last release> HEAD --pretty=format:%s
```

## 格式
每次提交，commit message 都包括三个部分：头部，主体和尾部。

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

其中，头部是必须的，主体和尾部可以省略。

任意一行不得超过100个字符，以免自动换行影响美观。

### 头部

头部只有一行，包含三个字段：`type` （必需），`scope `（可选）和 `subject` （必需）。

**type** 用于说明 commit 的类别，只能是下面几种之一。

* `feat`: 新功能
* `fix`: bug 修复
* `docs`: 文档更新
* `style`: 代码格式调整（不影响代码的运行逻辑）
* `refactor`: 代码逻辑重构（既非新增功能，也非修复 bug）
* `perf`: 代码性能优化
* `test`: 测试用例添加或改动
* `build`: 构建系统配置文件或脚本文件的改动
* `ci`: 持续集成系统配置文件或脚本文件的改动
* `chore`: 辅助脚本或工具的变动

如果 `type` 为 `feat` 和 `fix`，则该 commit 将肯定出现在 changelog 之中。其他情况可选。

**scope** 用于说明 commit 影响的范围，视项目不同而进行不同的划分。例如，以模块来区分范围。

**subject** 是 commit 目的的简短描述，遵循下列约束。

* 不超过50个字符
* 以动词开头，使用第一人称现在时
* 首字母小写
* 结尾不加句号

### 正文

正文是头部中 `subject` 字段的补充，用于详细描述本次 commit 的目的，代码变动，以及代码行为的变化。正文遵循下列约束。

* 可以分成多行，每行不超过100个字符
* 使用第一人称现在时
* 句尾正常使用标点符号

### 尾部

尾部仅适用于两种情况。

如果 commit 与上一个版本不兼容，则尾部以 `BREAKING CHANGE` 开头，后面是堆变动的描述，以及变动理由和迁移办法。

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

    The removed `inject` wasn't generaly useful for directives
    so there should be no code using it.
```

如果 commit 针对某个 issue，则在尾部关闭这个 issue。

```
Closes #234
```

也可以一次关闭多个 issue。

```
Closes #123, #245, #992
```

### 撤销

一种常见的特殊情况是，本次 commit 用于撤销之前的 commit。此时，必须以 `revert:` 开头，后面跟着被撤销的 commit 的头部。

```
revert: feat(pencil): add 'graphiteWidth' option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

主体部分的格式是固定的，必须写成如下格式：`This reverts commit <hash>`，其中的 `hash` 是被撤销的 commit 的标识符。

如果本次 commit 与被撤销的 commit 位于同一个版本中，那么它们都不会出现在 changelog 中。否则，本次的 commit 会出现在 changelog 的 `Reverts` 小标题下面。

## 示例
新特性：

```
feat($browser): onUrlChange event (popstate/hashchange/polling)

Added new event to $browser:
- forward popstate event if available
- forward hashchange event if popstate not available
- do polling when neither popstate nor hashchange available


Breaks $browser.onHashChange, which was removed (use onUrlChange instead)
```

修复 Bug：

```
fix($compile): couple of unit tests for IE9

Older IEs serialize html uppercased, but IE9 does not...
Would be better to expect case insensitive, unfortunately jasmine does
not allow to user regexps for throw expectations.

Closes #392
Breaks foo.bar api, foo.baz should be used instead
```

代码格式调整：

```
style($location): add couple of missing semi colons
```

更新文档：

```
docs(guide): updated fixed docs from Google Docs

Couple of typos fixed:
- indentation
- batchLogbatchLog -> batchLog
- start periodic checking
- missing brace
```

不兼容的新特性：

```
feat($compile): simplify isolate scope bindings

Changed the isolate scope binding options to:
  - @attr - attribute binding (including interpolation)
  - =model - by-directional model binding
  - &expr - expression execution binding

This change simplifies the terminology as well as
number of choices available to the developer. It
also supports local name aliasing from the parent.

BREAKING CHANGE: isolate scope bindings definition has changed and
the inject option for the directive controller injection was removed.

To migrate the code follow the example below:

Before:

scope: {
  myAttr: 'attribute',
  myBind: 'bind',
  myExpression: 'expression',
  myEval: 'evaluate',
  myAccessor: 'accessor'
}

After:

scope: {
  myAttr: '@',
  myBind: '@',
  myExpression: '&',
  // myEval - usually not useful, but in cases where the expression is assignable, you can use '='
  myAccessor: '=' // in directive's template change myAccessor() to myAccessor
}

The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```

## 工具
### Commitizen
[Commitizen](https://github.com/commitizen/cz-cli) 是一个撰写合格 commit message 的工具。

安装命令如下。

```
npm install -g commitizen
```

然后，在项目目录中，运行下面的命令。

```
commitizen init cz-conventional-changelog --save --save-exact
```

以后，凡是用到 git commit 命令，一律改为使用 git cz。

### validate-commit-msg

[validate-commit-msg](https://github.com/kentcdodds/validate-commit-msg) 用于检查 commit message 是否合格。

把脚本添加到 Git 的 hook 中，以后每次执行 git commit 的时候，这个脚本会自动检查 comment message 是否符合要求。如果不合格，则会报错。

### conventional-changelog

[conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) 用于生成 changelog。

安装命令如下。

```
npm install -g conventional-changelog
```

然后，在项目录中，运行下面的命令。

```
conventional-changelog -p angular -i CHANGELOG.md -w
```

该命令自动在 CHANGELOG.md 的头部添加自上个版本以来的变动。

添加的内容包括以下三部分：

* Features
* Bug fixes
* Breaking changes

每个部分都会罗列相关的 commit。