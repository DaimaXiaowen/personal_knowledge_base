# Git 远程协作

## 来源

- 《Pro Git（第 2 版）》2.5 节「远程仓库的使用」与第 5 章「分布式 Git」：https://git-scm.com/book/zh/v2
- GitHub Docs「关于拉取请求」：https://docs.github.com/zh/pull-requests

## 核心内容

### 远程仓库是什么

托管在服务器（GitHub / GitLab 等）上的仓库副本。本地操作永远不需要联网——只有 fetch / push 时刻才传输提交对象。克隆下来的仓库自动带一个名为 `origin` 的远程。

### remote 管理与同步命令

- `git remote -v`：查看远程；`git remote add <name> <url>`：添加
- `git clone <url>`：完整克隆 + 自动配置 origin + 本地 main 自动跟踪 origin/main
- `git fetch`：把远程新提交下载到 origin/main，**不合并**——先看再合，安全
- `git pull`：= fetch + merge（可用 `git config pull.rebase true` 改为变基方式）
- `git push`：推送本地提交；被拒绝最常见的原因是远程有你没有的提交 → 先 pull 解决分歧再 push
- `git push -u origin <branch>`：首次推送并建立跟踪，之后 `git push` 即可

### 跟踪分支

本地分支与 `origin/同名分支` 的关联关系：`git branch -vv` 查看；`git status` 里 ahead / behind 的数字就来自它。

### PR（Pull Request）工作流

以 GitHub 为例的团队协作流程：

1. 从最新的 main 切出功能分支（`feature/xxx`）
2. 本地开发、提交，`git push -u origin feature/xxx`
3. 平台上发起 PR：请求把功能分支合入 main，写清改了什么、为什么
4. 评审讨论，按意见继续提交 → PR 自动纳入新提交
5. CI 通过后选择一种方式合并；删除远程与本地功能分支，回 main 拉最新

### 三种合并方式（回收上一篇 --no-ff 的钩子）

| 方式 | 效果 | 适用 |
|---|---|---|
| Merge commit（`--no-ff`） | 即使能 fast-forward 也强制生成合并提交，历史上留下清晰的「一次合入」节点 | 要求保留分支完整过程与合并动作的团队 |
| Squash and merge | 功能分支的全部提交压成一个提交进入 main | 要求 main 上每个 PR 恰好一个干净提交 |
| Rebase and merge | 变基后线性接入，不改内容但哈希全变 | 追求线性历史且分支私有 |

`--no-ff` 被团队普遍要求的原因就在第一行：合并动作在历史里显式可见，方便整体回滚一次功能。

### 完整日常循环（对应项目目标第一条）

`git switch main` → `git pull` → `git switch -c feature/x` → 开发 → `add` / `commit` → `push` → 发 PR → 评审合并 → 回 main `git pull`，删掉功能分支。

## 疑问与待深入

- fork 模式 vs 分支模式：开源项目为什么要求先 fork 再提 PR
- force push（`--force` 与 `--force-with-lease` 的区别）什么场景下才合法
- 承接 [[03-Git 分支与合并|分支与合并]] 未深入的：`rebase -i` 的 squash / fixup、cherry-pick、detached HEAD
- 项目只剩最后一项实战：用本知识库仓库跑一遍完整流程（提交、分支、推送）
