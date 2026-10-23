# Git 常用命令

## 来源

- 《Pro Git》第 2 章
- Git 官方命令参考：https://git-scm.com/docs
- GitHub 出品的速查表：https://education.github.com/git-cheat-sheet-education.pdf

## 核心内容

### 配置（首次使用）

- `git config --global user.name "..."` / `user.email "..."`：身份信息，提交记录的作者
- `git config --global init.defaultBranch main`：新仓库默认分支名

### 开始与提交

- `git init`：当前目录初始化为仓库；`git clone <url>`：克隆远程仓库
- `git add <file>` / `git add .`：改动加入暂存区（. 表示全部）
- `git commit -m "说明"`：提交暂存区内容；`git commit -am "说明"`：跳过 add，直接提交所有已跟踪文件的改动（新文件仍需 add）
- `git status`：查看工作区与暂存区状态

### 查看历史与差异

- `git log --oneline --graph`：单行 + 图形化查看提交历史
- `git diff`：工作区 vs 暂存区；`git diff --staged`：暂存区 vs 上次提交

### 撤销与回退

- `git restore <file>`：丢弃工作区中某文件的改动（危险，不可恢复）
- `git restore --staged <file>`：把文件移出暂存区（改动保留在工作区）
- `git reset --soft <commit>`：回退提交，改动保留在暂存区
- `git reset --mixed <commit>`：回退提交，改动保留在工作区（默认）
- `git reset --hard <commit>`：回退提交并丢弃所有改动（最危险）
- 已推送到共享分支的提交不要用 reset，改用 `git revert`（生成一条反向提交）

### 分支（初步）

- `git branch`：列出分支；`git switch -c <name>`：新建并切换分支
- `git merge <name>`：把指定分支合并进当前分支；有冲突时改完文件后 `git add` 再 `git commit`

## 疑问与待深入

- `reset --hard` 只影响本地，为什么推送到共享分支后就危险？
- `git stash` 的使用场景，pop 和 apply 的区别
- rebase 和 merge 各适合什么场合 → 第 2 阶段学习分支时深入
