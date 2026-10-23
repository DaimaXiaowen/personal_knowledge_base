# Git 实战流程示例

## 来源

- 2026-10-23「Git 学习」项目结项实战的完整操作记录（本知识库仓库，PR #1）

## 场景

把知识库的新增笔记通过完整流程合入 GitHub 的 main：切功能分支 → 提交 → 推送 → 网页发 PR → 合并 → 清理两端分支。以下每一步的命令与输出均来自真实终端，注释是当时的判断依据。

## 完整流程（带注释）

### 1. 动手前同步 main

```bash
git pull
# Already up to date.
```

> 先拉最新再开发，避免基于旧历史工作——「减少冲突的习惯」第一条。

### 2. 切出功能分支

```bash
git switch -c feature/git_learning
# Switched to a new branch 'feature/git_learning'
```

> `-c` 创建并切换；分支名用 `feature/` 前缀表明用途，一眼可读。

### 3. 暂存与提交（踩坑一：漏 -m）

```bash
git add .

git commit "完成git学习,进行人工提交"
# error: pathspec '完成git学习,进行人工提交' did not match any file(s) known to git

git commit -m "完成git学习,进行人工提交"
# [feature/git_learning fd31bf4] 完成git学习,进行人工提交
#  12 files changed, 317 insertions(+), 5 deletions(-)
```

> 第一次失败：漏了 `-m`，git 把提交消息当成文件路径去找了。读报错的线索：`pathspec ... did not match any file` ≈「把参数当文件但找不到」→ 参数被误解了，回头查命令格式。
> 成功输出读法：方括号里是新提交哈希 `fd31bf4`，下面是变更统计（12 个文件、+317 行）。

### 4. 首次推送（踩坑二：无上游）

```bash
git push
# fatal: The current branch feature/git_learning has no upstream branch.

git push --set-upstream origin feature/git_learning
#  * [new branch]      feature/git_learning -> feature/git_learning
#  branch 'feature/git_learning' set up to track 'origin/feature/git_learning'.
```

> 远端还没有这个分支，git 不知道推到哪、跟谁比 ahead/behind，所以拒绝裸 `push`；`--set-upstream`（等价 `-u`）一次做两件事：在远端创建分支 + 建立跟踪关系。之后该分支直接 `git push` 即可。
> 注意 `remote: Create a pull request ...` 只是 GitHub 附带的提示链接，**不是已创建 PR**——见 [[04-Git 远程协作]] 的「push 和 PR 的边界」。

### 5. 在 GitHub 网页上发起并合并 PR（终端之外）

1. 打开上一步输出的链接 → 「Create pull request」（base: `main` ← compare: `feature/git_learning`）
2. 检查 diff 与提交列表，填写说明
3. 点「Merge pull request」，选「Create a merge commit」→ 远端 main 产生合并提交 `438e03a`

### 6. 回 main 拉取合并结果

```bash
git switch main
# Switched to branch 'main'

git pull
#    8b74809..438e03a  main       -> origin/main
# Fast-forward
#  12 files changed, 317 insertions(+), 5 deletions(-)
```

> `Fast-forward`：本地 main 没有分叉提交，指针直接前移到远端的合并提交，工作区文件随之全部更新。

### 7. 清理功能分支

```bash
git push origin --delete feature/git_learning
#  - [deleted]         feature/git_learning

git branch -d feature/git_learning
# Deleted branch feature/git_learning (was fd31bf4).
```

> 先删远端、再删本地。`-d` 是安全删除：`fd31bf4` 已通过 PR 合入 main，检查通过；若被拒绝，说明还有未合并的工作，确认要丢弃才改用 `-D`。
> 合并提交 `438e03a` 仍在 main 历史里——删的是指针，不是提交。

## 流程速记

```
pull → switch -c → 开发 → add → commit -m → push -u origin <分支> → 网页发 PR → Merge → switch main → pull → 删两端分支
```

下次在别的仓库做功能开发，照这条链走即可。

## 疑问与待深入

- 本次单人单分支没有遇到冲突，等真实遇到冲突后回来补一节「冲突处理实录」
- 若 PR 用 squash 方式合并，`git branch -d` 会因提交哈希不同被拒——实际体验到时再验证
