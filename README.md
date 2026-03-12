# 团队 GitHub Actions 仓库

本仓库用于存放团队内部复用的 GitHub Actions 和 Reusable Workflows。

## 📦 可用 Actions 列表

| Action 名称 | 描述 | 文档 |
|---|---|---|
| **Discord Notification** | 发送中文格式的代码提交统计通知到 Discord | [查看文档](docs/discord-notify.md) |

## 🚀 如何在其他项目中使用

要将本仓库的 Action 安装到你的项目中，你不需要下载任何代码，只需在你的项目 `.github/workflows/` 下的 YAML 文件中引用即可。

### 1. 引用 Action (uses)

在你的 Workflow `steps` 中使用 `uses` 关键字引用本仓库的具体路径。

**语法**: `uses: {Owner}/{Repo}/actions/{ActionDir}@{Ref}`

**示例**: 使用 Discord 通知 Action

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 2  # 统计变更需要历史记录

  - name: 发送 Discord 通知
    uses: Time-Machine-Lab/TML-Github-Actions/actions/discord-github-notify@main
    with:
      webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
```

### 2. 引用 Reusable Workflow

如果本仓库提供了完整的流程模板（在 `.github/workflows/` 下），可以在 `jobs` 层级引用。

**语法**: `uses: {Owner}/{Repo}/.github/workflows/{WorkflowFile}@{Ref}`

```yaml
jobs:
  build:
    uses: Time-Machine-Lab/TML-Github-Actions/.github/workflows/maven-build.yml@main
    with:
      java-version: '17'
```

## 目录结构

- `.github/workflows`: 存放可复用的工作流模板 (Reusable Workflows)。
- `actions/`: 存放具体的自定义 Actions。
- `scripts/`: 辅助脚本。
- `docs/`: 详细文档。

## 开发与调试

推荐使用 `act` 进行本地调试。

```bash
brew install act
act -l
```
