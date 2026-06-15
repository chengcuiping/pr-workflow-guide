# Pull Request Workflow

适用于向任意开源仓库提交代码贡献，采用常见的 Fork + Pull Request 模式。

## Overview

大多数开源项目采用 Fork + Pull Request 协作模式：你先 Fork 原仓库，在自己的 Fork 仓库里开发，然后向原仓库提交 Pull Request 请求合并。

## Basic Workflow

### 1. Fork Repository

在 GitHub 上打开目标仓库，点击右上角 **Fork**，将仓库复制到自己的 GitHub 账号下。

### 2. Clone Your Fork and Configure Upstream

克隆你自己的 Fork 仓库：

    git clone https://github.com/<your-username>/<repo>.git
    cd <repo>

添加原仓库作为 `upstream`，方便后续同步：

    git remote add upstream https://github.com/<original-owner>/<repo>.git

配置 Git 提交身份：

    git config user.name "Your Name"
    git config user.email "your-email@example.com"

### 3. Create a New Branch Based on Upstream Main

先拉取上游最新代码：

    git fetch upstream

基于上游主分支新建一个功能分支：

    git checkout -b feature/my-change upstream/main

> 建议不要直接在 `main` 分支上修改代码。

### 4. Make Code Changes

修改代码时建议遵循以下原则：

  * 一个 PR 只做一件事，改动尽量小而聚焦。
  * 遵循项目已有的代码风格和目录约定。
  * 如果修改了功能，尽量补充对应测试。

### 5. Run Format, Lint, and Tests Locally

提交前先在本地运行项目要求的格式化、静态检查和测试。

示例：

    # Python project examples
    black .
    ruff check .
    pytest <related-tests>

    # JavaScript / Node.js project examples
    npm run lint
    npm test

### 6. Commit Changes

查看修改内容：

    git status
    git diff

添加文件并提交：

    git add <changed-files>
    git commit -m "Briefly describe the change"

提交信息建议：

  * 首行简短概括这次改动。
  * 如有必要，在正文说明为什么这样改、如何验证。

### 7. Rebase on the Latest Upstream Main

提交 PR 前，再次同步上游主分支，减少冲突：

    git fetch upstream
    git rebase upstream/main

如果发生冲突，解决冲突后继续 rebase：

    git add <resolved-files>
    git rebase --continue

### 8. Push to Your Fork

将本地分支推送到你的 Fork 仓库：

    git push -u origin feature/my-change

> 如果使用 HTTPS 推送，GitHub 通常需要使用 Personal Access Token，不能直接使用账号密码。

### 9. Open a Pull Request

打开 GitHub 上你的 Fork 页面，点击 **Compare & pull request**。

填写 PR 时建议说明：

  * 这个 PR 解决了什么问题。
  * 主要做了哪些修改。
  * 如何验证这些修改。
  * 是否关联了 issue，例如 `Fixes #123`。

### 10. Respond to Review Comments

PR 提交后，维护者会 review 代码并触发 CI。

如果需要修改：

    # 修改代码后
    git add <changed-files>
    git commit -m "Address review comments"
    git push

同一个分支继续 push，Pull Request 会自动更新。

## Quick Reference

    # Fork repository on GitHub first

    git clone https://github.com/<your-username>/<repo>.git
    cd <repo>
    git remote add upstream https://github.com/<original-owner>/<repo>.git

    git fetch upstream
    git checkout -b feature/my-change upstream/main

    # Make code changes
    # Run lint and tests

    git add <changed-files>
    git commit -m "Briefly describe the change"

    git fetch upstream
    git rebase upstream/main

    git push -u origin feature/my-change

    # Open Pull Request on GitHub

## PR Description Template

    ## What changed

    - 

    ## Why

    - 

    ## How tested

    - 

    ## Related issue

    - Fixes #
