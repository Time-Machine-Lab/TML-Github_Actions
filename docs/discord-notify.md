# Discord GitHub Notify Action

这是一个用于发送 GitHub 事件通知到 Discord 的 Action。支持 Push、Pull Request 和 Release 事件，并以中文格式展示详细信息。

## 功能特点

- 🇨🇳 **全中文界面**：友好的中文通知格式。
- 📊 **多事件支持**：
  - **Push**: 代码提交统计（新增/删除行数）。
  - **Pull Request**: 新增 PR（待检视）、合并 PR、关闭 PR。
  - **Release**: 新版本发布通知。
- 🔗 **快速链接**：提供仓库、PR、Commit、Release 的直接跳转链接。

## 输入参数 (Inputs)

| 参数名 | 必填 | 默认值 | 描述 |
|---|---|---|---|
| `webhook_url` | 是 | 无 | Discord Webhook 地址。建议存储在 Secrets 中。 |
| `github_token` | 否 | `${{ github.token }}` | GitHub Token，用于 API 访问（目前仅作预留）。 |

## 快速开始

在你的 GitHub 仓库 `.github/workflows/` 目录下创建一个 YAML 文件（例如 `notify.yml`）：

```yaml
name: Discord Notification

on:
  push:
    branches: [ "**" ]
  pull_request:
    types: [opened, reopened, closed]
  release:
    types: [published]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2  # 必须设置为 2 或 0，以便统计 Push 事件的变更

      - name: Send Discord Notification
        uses: Time-Machine-Lab/TML-Github-Actions/actions/discord-github-notify@main
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
```

## 支持的事件类型

该 Action 会根据触发的事件自动调整通知样式：

### 1. Push (代码提交)
- **颜色**: 🔵 蓝色
- **内容**: 分支、提交者、提交信息、代码变更统计（文件/新增/删除）。

### 2. Pull Request (代码合并请求)
- **Open / Reopen**: 🟠 橙色
  - 标题: "🔀 新增 PR 待检视"
  - 内容: 发起人、源分支 -> 目标分支。
- **Merged**: 🟢 绿色
  - 标题: "✅ PR 已合并"
  - 内容: 合并者、目标分支。
- **Closed (未合并)**: 🔴 红色
  - 标题: "⛔ PR 已关闭"
  - 内容: 操作者。

### 3. Release (版本发布)
- **Published**: 🟣 紫色
  - 标题: "🎉 新版本发布"
  - 内容: 版本号、版本类型（正式版/预发布版）、发行说明链接。

## 注意事项

1. **Checkout Depth**: 务必在 `actions/checkout` 中设置 `fetch-depth: 2`，否则无法计算 Push 事件的代码变更统计。
2. **Secrets**: 请在仓库 Settings -> Secrets and variables -> Actions 中添加 `DISCORD_WEBHOOK_URL`。
