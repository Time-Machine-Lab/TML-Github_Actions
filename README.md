# 团队 GitHub Actions 仓库

本仓库用于存放团队内部复用的 GitHub Actions 和 Reusable Workflows。

## 📦 可用 Actions 列表

| Action 名称 | 描述 | 文档 |
|---|---|---|
| **Discord Notification** | 发送中文格式的代码提交统计通知到 Discord | [查看文档](docs/discord-notify.md) |

## 🔁 可用 Reusable Workflows 列表

| Workflow 名称 | 描述 | 文档 |
|---|---|---|
| **Deploy Static Sites** | 将 `products/*` 静态站点增量部署到服务器并可选 reload nginx | [查看文档](docs/deploy-static-sites.md) |

## 🚀 如何在其他项目中使用

### 🤖 1. Agent 辅助集成 (推荐)

如果你正在使用 AI 编程助手（如 Trae, Copilot, Cursor 等），复制以下 **Prompt (提示词)** 发送给它，并将 `{{工作流名称}}` 替换为你想要安装的 Action（例如 `Discord Notification`）：

> **请帮我集成 GitHub Actions 工作流。**
>
> **来源仓库**：`https://github.com/Time-Machine-Lab/TML-Github_Actions`
> **目标 Action**：`{{工作流名称}}`
>
> **任务要求**：
> 1. **阅读文档**：请访问上述仓库，查阅 `actions/` 目录下对应的 `action.yml` 定义以及 `docs/` 目录下的使用文档。
> 2. **生成配置**：在当前项目中生成一个新的 Workflow 文件（如 `.github/workflows/{{工作流名称}}.yml`）。
> 3. **智能配置**：
>    - 确保引用路径正确（参考文档中的 `uses` 路径）。
>    - 根据文档自动配置所有必要的 `inputs` 和 `secrets`。
>    - 检查是否需要特殊的步骤（例如文档中提到的 `checkout fetch-depth` 等前置要求）。
>
> **请生成完整的 YAML 文件内容，并提醒我需要配置哪些 Repository Secrets。**

---

### 2. 手动引用 Action (uses)

要将本仓库的 Action 安装到你的项目中，你不需要下载任何代码，只需在你的项目 `.github/workflows/` 下的 YAML 文件中引用即可。

在你的 Workflow `steps` 中使用 `uses` 关键字引用本仓库的具体路径。

**语法**: `uses: {Owner}/{Repo}/actions/{ActionDir}@{Ref}`

**示例**: 使用 Discord 通知 Action

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 2  # 统计变更需要历史记录

  - name: 发送 Discord 通知
    uses: Time-Machine-Lab/TML-Github_Actions/actions/discord-github-notify@main
    with:
      webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
```

### 3. 引用 Reusable Workflow

如果本仓库提供了完整的流程模板（在 `.github/workflows/` 下），可以在 `jobs` 层级引用。

**语法**: `uses: {Owner}/{Repo}/.github/workflows/{WorkflowFile}@{Ref}`

```yaml
jobs:
  deploy:
    uses: Time-Machine-Lab/TML-Github_Actions/.github/workflows/deploy-static-sites.yml@main
    with:
      force_deploy: false
    secrets:
      SERVER_HOST: ${{ secrets.SERVER_HOST }}
      SERVER_USER: ${{ secrets.SERVER_USER }}
      SERVER_PASS: ${{ secrets.SERVER_PASS }}
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
