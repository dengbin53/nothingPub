
## Tag

### 删除 Tag

```bash
git tag -d vX.Y.Z
git push origin :refs/tags/vX.Y.Z
```

## 提交代码的最佳实践

### 目的

- 清晰的提交历史
- 可抽取提交

### 以逻辑为单元提交

每个提交都应有「明确的」「唯一的」目的，比如添加某功能、修复某故障，切忌将若干修改混在一个提交中。

### 频繁提交

代码应频繁提交，频繁测试，尽快通过持续集成系统交付到测试人员手中进行测试。

### Rebase & Fixup

实际执行中你会发现「以逻辑为单元提交」、「频繁提交」反而无法获得「清晰的提交历史」，例如：

```text
feat: add feature-a
feat: add feature-b
fix: bug-a-1 of feature-a
fix: bug-b-1 of feature-b
feat: add feature-c
```

开发阶段的 bugfix 一般并不需要保留单独的提交（除非是出于评估开发人员的代码质量的目的）我们可以通过 `git-rebase` 的 Fixup 功能避免这种情况，例如：

开始前的提交历史：

```text
5d02ffb (master) hello world
8ad582c init
```

```bash
git checkout master
git pull
git checkout -b feature/my-task
# 当前 commit 为 5d02ffb
git commit -m 'feat: add feature-a' # 此时得到 commit c55f417
git commit -m 'feat: add feature-b' # 此时得到 commit 81b7d9c
# 修复 feature-a 的问题 bug-a-1
git commit --fixup c55f417
git commit -m 'feat: add feature-c' # 此时得到 commit 4116961
# 修复 feature-b 的问题 bug-b-1
git commit --fixup 81b7d9c
```

此时的提交历史类似下面的情况：

```text
98a9090 (HEAD -> feature/my-task) fixup! feat: add feature-b
4116961 feat: add feature-c
f4aa408 fixup! feat: add feature-a
81b7d9c feat: add feature-b
c55f417 feat: add feature-a
5d02ffb (master) hello world
8ad582c init
```

使用 `git commit --fixup` 的提交，不用写提交信息，系统会以 'fixup!' 加目标提交的提交信息作为提交信息。

此时提交历史依旧很乱，我们可以用 `git-rebase` 进行整理

```bash
git rebase -i --autosquash 5d02ffb
```

此时 Git 会打开编辑器要求确认，直接确认即可，之后的提交历史会变为：

```text
4116961 feat: add feature-c
01a7b88 feat: add feature-b
252f396 feat: add feature-a
5d02ffb (master) hello world
8ad582c init
```

注意 `feat: add feature-a` 和 ` feat: add feature-b` 改动了所以其 SHA 也发生了变化，`feat: add feature-c** 没有改动则不变。

至此，我们实现了「以逻辑为单元提交」、「频繁提交」，并且获得了「清晰的提交历史」。

> `git-rebase` 是危险操作，它会改写历史，发生冲突时若选择 `skip` 会丢弃代码，请确认你清晰的理解其原理和使用方法后再在实际工作中应用
