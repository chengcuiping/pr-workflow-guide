# Pull Request Workflow

适用于通过常见的 Fork + Pull Request 模式向 GitHub 开源仓库贡献代码。

> 本文是通用流程。开始前必须优先阅读目标仓库的 `README`、`CONTRIBUTING.md` 和 Pull Request 模板；项目自己的贡献规则优先于本文。

## Overview

典型流程如下：

1. Fork 原仓库到自己的 GitHub 账号。
2. 克隆自己的 Fork，并将原仓库配置为 `upstream`。
3. 基于上游目标分支创建独立工作分支。
4. 完成修改、测试和提交。
5. 同步上游最新代码并推送到自己的 Fork。
6. 从 Fork 向原仓库创建 Pull Request。
7. 根据代码审查和 CI 结果继续更新同一个分支。

## 1. Check the Repository Contribution Rules

开始修改前，先检查目标仓库是否有以下要求：

- 是否要求先创建或认领 issue。
- PR 应提交到哪个目标分支。
- 分支名和提交信息的格式。
- 必须运行哪些格式化、静态检查和测试命令。
- 是否要求签署 CLA、使用 DCO 或执行 `git commit -s`。
- 是否有 PR 模板、变更日志或文档更新要求。

不要直接套用本文中的测试命令；应以项目文档和 CI 配置为准。

## 2. Fork and Clone the Repository

在 GitHub 上打开目标仓库，点击右上角 **Fork**，将仓库复制到自己的账号下。

克隆自己的 Fork：

```bash
git clone https://github.com/<your-username>/<repo>.git
cd <repo>
```

此时 `origin` 通常指向自己的 Fork。添加原仓库为 `upstream`：

```bash
git remote add upstream https://github.com/<original-owner>/<repo>.git
git remote -v
```

预期关系：

- `origin`：自己的 Fork，用于推送工作分支。
- `upstream`：原仓库，用于获取最新代码。

## 3. Configure Commit Identity and Authentication

配置提交身份：

```bash
git config user.name "Your Name"
git config user.email "your-verified-email@example.com"
```

建议使用 GitHub 已验证邮箱或 GitHub 提供的 `noreply` 邮箱，确保提交能够正确关联到账号。

GitHub 不支持使用账号密码进行 Git 推送。使用 HTTPS 时，可以通过 GitHub CLI、Git Credential Manager 或 Personal Access Token 认证；也可以改用 SSH。

## 4. Identify the Upstream Target Branch

原仓库的默认分支不一定叫 `main`，也可能是 `master`、`develop` 或项目指定的其他分支。

获取上游信息：

```bash
git fetch upstream
git remote show upstream
```

查看输出中的 `HEAD branch`，并结合 `CONTRIBUTING.md` 确认 PR 应提交到哪个目标分支。以下示例假设目标分支为 `main`；如果项目使用其他分支，请替换命令中的 `main`。

## 5. Create a Dedicated Working Branch

基于最新的上游目标分支创建工作分支：

```bash
git switch -c feature/my-change upstream/main
```

旧版 Git 可以使用：

```bash
git checkout -b feature/my-change upstream/main
```

不要直接在自己的 `main` 分支上开发。一个分支和一个 PR 尽量只解决一个明确问题。

## 6. Make Focused Changes

修改代码时遵循以下原则：

- 改动小而聚焦，避免混入无关重构或格式化。
- 遵循项目现有的代码风格、目录结构和公共 API 约定。
- 功能变更和缺陷修复应尽量补充对应测试。
- 不要提交密钥、Token、密码、内部地址、个人数据或大型生成文件。
- 提交前检查 `git diff`，确认没有调试代码或意外文件。

## 7. Run the Project's Checks

执行项目明确要求的格式化、静态检查和测试。下面仅为示例：

```bash
# Python examples
black .
ruff check .
pytest <related-tests>

# JavaScript / Node.js examples
npm run lint
npm test
```

如果无法运行某些检查，应在 PR 描述中说明未运行的项目及原因。

## 8. Review and Commit the Changes

检查当前状态和改动：

```bash
git status
git diff
```

添加需要提交的文件，避免无条件使用 `git add .`：

```bash
git add <changed-files>
git diff --cached
git commit -m "Briefly describe the change"
```

提交信息应遵循目标项目的规范。首行简洁说明改动；必要时在正文解释原因、实现方式和兼容性影响。

如果项目要求 DCO，请用下面的签名提交命令替代前面的普通 `git commit`：

```bash
git commit -s -m "Briefly describe the change"
```

## 9. Rebase on the Latest Upstream Branch

提交 PR 前，再次获取上游代码并 rebase：

```bash
git fetch upstream
git rebase upstream/main
```

如果发生冲突：

```bash
# 编辑并解决冲突后
git add <resolved-files>
git rebase --continue
```

如果需要取消本次 rebase：

```bash
git rebase --abort
```

rebase 会重写相关提交历史。不要对多人共享的分支随意 rebase。

## 10. Push to Your Fork

首次推送工作分支：

```bash
git push -u origin feature/my-change
```

后续只是新增普通提交时：

```bash
git push
```

如果该分支已经推送过，之后又执行了 rebase，需要安全地更新远程历史：

```bash
git push --force-with-lease
```

优先使用 `--force-with-lease`，不要直接使用 `--force`，以免覆盖其他人的远程提交。

## 11. Open the Pull Request

在 GitHub 上打开自己的 Fork，点击 **Compare & pull request**。创建前仔细确认：

- **base repository**：原仓库。
- **base branch**：项目要求的目标分支。
- **head repository**：自己的 Fork。
- **compare branch**：本次工作分支。

PR 描述应说明：

- 解决了什么问题以及为什么需要修改。
- 主要实现方式和行为变化。
- 如何验证，包括实际运行的测试命令和结果。
- 是否有兼容性、性能、安全或文档影响。
- 关联的 issue，例如 `Fixes #123`。只有确定合并后应自动关闭该 issue 时才使用 `Fixes`。

如果工作尚未完成但希望尽早获得反馈，可以创建 Draft Pull Request。

## 12. Respond to Reviews and CI

维护者提出修改意见后，在同一个本地分支继续修改、提交和推送：

```bash
git add <changed-files>
git commit -m "Address review comments"
git push
```

原 Pull Request 会自动更新，通常不需要创建新的 PR。

处理审查意见时：

- 回复已经处理的意见，并说明对应改动。
- 不同意建议时，礼貌说明技术原因和权衡。
- 确认必需 CI 全部通过。
- 如果根据项目要求整理或 rebase 已推送的提交，使用 `git push --force-with-lease`。

## 13. Optional: Sync Your Fork's Default Branch

工作分支基于 `upstream/main` 创建即可，不要求先更新 Fork 的 `main`。如果还希望同步自己 Fork 的默认分支，可以执行：

```bash
git switch main
git fetch upstream
git merge --ff-only upstream/main
git push origin main
```

也可以在 GitHub Fork 页面使用 **Sync fork**。请将示例中的 `main` 替换为实际默认分支。

## Quick Reference

```bash
# Fork the repository on GitHub first
git clone https://github.com/<your-username>/<repo>.git
cd <repo>
git remote add upstream https://github.com/<original-owner>/<repo>.git

# Read CONTRIBUTING.md and determine the actual target branch
git fetch upstream
git remote show upstream

# Replace main when the project uses another target branch
git switch -c feature/my-change upstream/main

# Make focused changes and run the project-required checks
git status
git diff
git add <changed-files>
git diff --cached
git commit -m "Briefly describe the change"

git fetch upstream
git rebase upstream/main
git push -u origin feature/my-change

# Open a PR from your fork branch to the correct upstream branch
```

## PR Description Template

```markdown
## What changed

-

## Why

-

## How tested

- Command:
- Result:

## Impact

- Compatibility:
- Performance:
- Security:
- Documentation:

## Related issue

- Fixes #
```

## Final Checklist

- [ ] 已阅读并遵循 `CONTRIBUTING.md` 和 PR 模板。
- [ ] PR 只包含一个聚焦的改动。
- [ ] 已检查 `git diff` 和 `git diff --cached`。
- [ ] 已运行项目要求的格式化、检查和测试。
- [ ] 未提交密钥、Token、密码或个人数据。
- [ ] 已同步正确的上游目标分支。
- [ ] PR 的 base 和 compare 仓库、分支均正确。
- [ ] PR 描述包含原因、改动、验证结果和影响。
