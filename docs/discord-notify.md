# Discord GitHub Notify Action

这是一个用于发送 GitHub Push 事件通知到 Discord 的 Action。它会统计代码变更行数，并以中文格式展示详细信息。

## 功能特点

- 🇨🇳 **全中文界面**：友好的中文通知格式。
- 📊 **代码统计**：自动计算新增、删除行数及变更文件数。
- 🔗 **快速链接**：提供仓库、分支、Commit 的直接跳转链接。

## 输入参数 (Inputs)

| 参数名 | 必填 | 默认值 | 描述 |
|---|---|---|---|
| `webhook_url` | 是 | 无 | Discord Webhook 地址。建议存储在 Secrets 中。 |
| `github_token` | 否 | `${{ github.token }}` | GitHub Token，用于 API 访问（目前仅作预留）。 |

## 快速开始

在你的 GitHub 仓库 `.github/workflows/` 目录下创建一个 YAML 文件（例如 `notify.yml`）：

```yaml
name: Discord Notification

on: [push]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          fetch-depth: 2  # 必须设置为 2 或 0，以便统计变更

      - name: Send Discord Notification
        uses: Time-Machine-Lab/TML-Github-Actions/actions/discord-github-notify@main
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
```

## 注意事项

1. **Checkout Depth**: 务必在 `actions/checkout` 中设置 `fetch-depth: 2`，否则无法计算代码变更统计（因为默认深度为 1，没有上一个 commit 的记录）。
2. **Secrets**: 请在仓库 Settings -> Secrets and variables -> Actions 中添加 `DISCORD_WEBHOOK_URL`。

## 效果预览

通知将包含以下信息：
- 仓库名称及链接
- 分支与提交者
- 提交信息
- 变更统计（文件数、新增、删除）
- Commit 跳转链接
