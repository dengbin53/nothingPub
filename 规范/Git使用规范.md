
## 介绍

这是我们团队的 Git 使用规范。每个人对工具的使用往往各有偏好，各种方法各有利弊，无所谓对错。但涉及团队协作的方面需要有一些一致的规范，所以请大家务必遵守。

除了一致性之外，这个规范的目的是以下几点：

- 确保可以轻易确定特定时间发布或运行的版本。在新发布的程序存在重大缺陷时，可以尽快 rollback 到上一个稳定版本。
- 在需要修复紧急 bug 并尽快发布时，可以只发布必要的 bugfix 而不同时发布还不应发布的其他改动。

## 必读

- [猴子都能懂的 Git 入门](http://backlogtool.com/git-guide/cn/)

## GitLab

我们使用 GitLab 协作开发，先申请代码库访问权限。

**SSH Key** 推荐使用 ssh 的方式访问代码库，这种方式更安全而且可以不用输入密码。

**如何去掉 Key 的 Passphrase**

```
pushd ~/.ssh
openssl rsa -in id_rsa -out id_rsa.new
copy id_rsa id_rsa.bak
copy id_rsa.new id_rsa
```

## 约定

### BRANCH - 分支

**命名** 分支名称用 `kebab-case`，即单词用减号分割，如 `hello-world`、`rename-user_id-to-uid`。

**前缀** 用于指明工作流,如下：

| Name    | Naming            | Description              |
|---------|-------------------|--------------------------|
| Feature | `feature/my-task` | 新的功能                 |
| Enhance | `enhance/my-task` | 对某功能的改进           |
| Bugfix  | `bugfix/my-task`  | 修复开发流程中出现的故障 |
| Hotfix  | `hotfix/my-task`  | 修复线上问题             |

每个 Repo 下面仅有以下 branch 和 tag。

**Branch**

GitLab 有两个固定分支 `master` 和 `release`:

- `master` 是开发主分支
- `release` 是发布分支

**Tag** 对应每个发布版本的 tag，遵照 `<major>.<minor>.<patch>` 的命名，如 `2.5.1`。

**服务端程序** 的 tag 以发布的日期命名，如 `2014.11.13`，如果有 bugfix，则在后面增加小写字母，如 `2014.11.13` 后是 `2014.11.13a`，然后是 `2014.11.13b`。

### COMMIT - 提交

请阅读 [提交代码的最佳实践](../git-howtos/#提交代码的最佳实践)

### COMMIT MESSAGE - 提交信息

如何撰写「提交信息」请阅读 [Commit message 的约定](../git-commit-conventions)；

## DEVELOPMENT FLOW - 开发流程

当你要开始一项新的工作、开发一个新的功能时，使用此流程。

```bash
# 确保 master 是最新的
git checkout master
git pull
# 创建自己的开发分支
git checkout -b feature/my-task
git add my-changes
git commit -m 'feat: my-changes'
git push -u origin feature/my-task
```

在 GitLab 上基于 `master` 创建 Pull Request，邀请同事进行代码审核。审核后 **Rebase and Merge** 至 master 分支。

## RELEASE FLOW - 发布流程

当开发和测试结束，要发布（上线）时，使用此流程。

- 确保所有要发布的 pull request 都已经 merge 到 master；
- 使用 master branch 的代码进行测试，如果发现 bug，把对应的 bugfix merge 到 master；
- 删除旧的 release branch，并从当前的 master 创建新的 release branch；
- 在 Jenkins 上从 release branch 发起新的 build 并发布；
- 发布完成后在当前的 release branch 打上对应版本的 tag。

## HOTFIX FLOW - 线上故障修复流程

当已发布（线上）系统出现故障，`master`

这里的 `hotfix` 指的是修复已经发布的程序（`release` branch）中的缺陷。

```bash
# 确保 'release' 分支最新
git checkout release
git pull
# 创建 'hotfix' 分支
git checkout -b hotfix/your-bug
# -- 修复缺陷
git commit
git push -u origin bugfix/your-bug
# -- 执行 Pull Request 流程，base 为 master
# 至此 master 上的问题已修复
# 同步 release 分支
git checkout release
git cherry-pick commit-foo
git cherry-pick commit-bar
# -- 发布 'release' branch
git tag -a vX.Y.Z -m 'Release X.Y.Z'
```

## MERGING CONFLICTS - 冲突合并

首先，应从工作安排上尽量避免冲突：

- 按业务安排研发工作，尽量独立，避免同时修改同一文件；
- 对共享模块（比如基础库、工具库等）的修改，应单独进行，尽早提交；

冲突是在所难免的，目前认为有两种典型场景，应使用不同的方式达到最好的效果。

### CASE-1 HOTFIX BRANCH

按照 HOTFIX 流程：

1. 基于 `release` 分支创建新的分支 `hotfix/my-branch`；
1. 修改、提交得到 `hotfix-commit`，推送到 GitLab，并创建基于 `master` 分支的 Pull Request；
1. 此时若有冲突，采用 GitLab 的冲突解决方法，即：
  1. `git checkout master`
  1. `git pull master`
  1. `git checkout hotfix/my-branch`
  1. `git merge master`
  1. 解决冲突
  1. `git commit`
  1. `git push`
1. 此时除了 `hotfix-commit`，还会得到一个解决冲突的提交，比如 `Merge branch 'master' into bugfix/my-branch`，其中包含解决冲突的代码；
1. Code review 后合并 Pull Request；
1. 将修改应用到 `release` 分支：
  1. `git checkout release`
  1. `git cherry-pick 091f1c5`，（假设 `091f1c5` 是 `hotfix-commit` 的 SHA）
  1. `git tag -a 'v1.0.1'`
  1. `git push`
  1. `git push --tags`
1. 此时可从 `release` 分支发布新的 Hotfix 版本；

这种做法的好处是，`Merge branch 'master' into hotfix/my-branch` 是一个独立的 commit，包含了解决冲突的代码，使 `hotfix/my-branch` 可以轻松 `cherry-pick` 到 `release`，不用担心将 `master` 上的代码带到 `release` 分支。

### CASE-2 FEATURE BRANCH

对于 FEATURE 分支，把「合并冲突的代码」独立为一个 commit 意义不大，反而会让日志混乱，所以我们使用下面的流程。

1. 基于 `master` 分支创建新的分支 `feature/my-branch`
1. 开发新功能提交得到 `feature-commit`，推送到 GitLab，并创建基于 `master` 分支的 Pull Request；
1. 此时若有冲突，请：
  1. `git checkout master`
  1. `git pull`
  1. `git checkout feature/my-branch`
  1. `git rebase master`
  1. 解决冲突
  1. `git rebase --continue`
  1. `git push`

这种做法的好处是：没有单独的 Merge Commit，Git History 更加清晰，原因是：`git-rebase` 会将 `master` 分支的提交从创建 `feature/my-branch` 的 commit 起先压入，再应用 `feature/my-branch` 上的提交，因此你的提交在最后，并且解决冲突后不会产生单独的 Merge Commit；

