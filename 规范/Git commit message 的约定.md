下列规则采纳自 [the AngularJS commit conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/).

## Commit 信息格式

每个 commit 信息由 **header** **body** **footer** 组成，Header 的由 **type** **scope** 和 **subject** 组成。

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

例子：
```
feat(ruler): 新增单位英寸和厘米
```
```
fix(protractor): 修复角度度量错误，90 记为了 91
```
```
refactor(pencil): 使用石墨代替铅

石墨比铅的资源更丰富，我们以此来降低价格
```
```
fix(pen): 用蓝色墨水代替红色墨水

BREAKING CHANGE: 钢笔现在使用蓝色墨水代替红色墨水

要迁移，请按下面的的例子修改你的代码：

`pen.draw('blue')`

To:

`pen.draw('red')`
```

每一行 commit 消息不能超过 100 个字符。这样在 github 上以及各种 git 工具上都更易被阅读。

### Type

推荐使用下列类型，其中只有 **feat** 和 **fix** 会展示在 changelog 中，还有 breaking changes （详见下面的 breaking changes 一节）。

- **feat**: 一个新功能
- **fix**: 一个 bug 修复
- **docs**: 仅修改了文档
- **style**: 未涉及任何有意义的代码，如空白、格式化、分号等
- **refactor**: 既不是新功能也不是修复 bug 的修改
- **test**: 添加遗漏的测试代码
- **chore**: 修改构建流程，或者辅助工具和库，比如文档生成

### Scope

范围可以使任何东西，用于指明这个 commit 修改的地方，比 `$location`, `$browser`, `$compile`, `$rootScope`, `ngHref`, `ngClick`, `ngView`, 等。

### Subject

主题是关于这个修改的简洁的介绍：

- 使用祈使语气，现在时态，使用 "change"，而不是 "changed" 也不是 "changes"
- 不要大写第一个字符
- 不要再尾部添加句点（。或者 .）

### Breaking Changes

这是什么鬼？[What is a breaking change in software?](https://stackoverflow.com/questions/21703216/what-is-a-breaking-change-in-software)

在 commit footer 添加 breaking changes 的迁移指导。

在 commit footer 中添加 **BREAKING CHANGE:**，它会在 changelog 中展示。

## 在 github 上合并 Pull request

PS：暂时记在这里，以后迁移到 github 使用指南中
使用 [Rebase and merge your pull request commits](https://help.github.com/articles/about-pull-request-merges/#rebase-and-merge-your-pull-request-commits)

## 在 gitLab 上合并 Pull request

参考 github 上的 pr