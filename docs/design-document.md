# 设计文档 - Update README Workflow

## 概述

本设计文档描述了GitHub Actions workflow `update-readme.yml` 的功能和效果。该workflow旨在自动更新仓库的README.md文件，添加用户前10个最受欢迎（star数最多）的仓库列表。

## 目的

- 自动维护README.md中的热门仓库列表。
- 提供用户仓库的动态展示。
- 通过定时任务确保列表始终是最新的。

## 触发器

- **Push事件**：当代码推送到仓库时触发。
- **定时调度**：每小时运行一次，使用cron表达式 `0 */1 * * *`。

## 工作流程步骤

### 1. Checkout Repository

- 使用 `actions/checkout@v4` 检出仓库代码。
- 确保workflow有权限访问仓库内容。

### 2. Update README

- 使用 `actions/github-script@v7` 执行JavaScript脚本。
- 脚本逻辑：
  - 使用GitHub API获取用户的前10个仓库，按star数降序排序。
  - 生成Markdown格式的仓库列表，包括名称、链接、描述和star数。
  - 读取当前README.md内容。
  - 在README.md中查找占位符 `<!-- TOP-REPOS-LIST -->`，如果存在，则替换；否则追加到文件末尾。
  - 更新README.md文件内容。

### 3. Commit Changes

- 使用 `stefanzweifel/git-auto-commit-action@v5` 自动提交更改。
- 提交消息：`Update README with top starred repos`。
- 推送到 `main` 分支。

## 效果

- README.md文件会被定期更新，包含最新的前10个最受欢迎仓库列表。

- 列表格式为：

  ```markdown
  ## 🌟 Top Starred Repositories

  - [Repo Name](URL) - Description (stars stars)
  ```

- 如果README.md中已有占位符，列表会插入到占位符位置；否则追加到文件末尾。
- 每次更新都会生成新的提交，确保仓库历史记录更新过程。

## 依赖

- GitHub API：用于获取仓库信息。
- GitHub Token：使用 `secrets.GH_TOKEN` 进行认证。

## 注意事项

- 确保仓库有适当的权限设置。
- 如果README.md不存在，workflow会创建新的README.md。
- 列表仅包括用户自己的仓库（type: 'owner'）。

