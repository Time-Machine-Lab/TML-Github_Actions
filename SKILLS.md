---
name: github workflow actions
description: This skill provides guidelines for developing GitHub Actions and Reusable Workflows in the TML-Github-Actions repository. Use it when creating new actions, debugging workflows, or writing documentation to ensure compliance with project standards.
metadata:
  author: tml
---

# 🛠️ 项目开发技能指南 (SKILLS)

本文档旨在辅助项目贡献者快速掌握本仓库的开发规范，特别是针对 GitHub Actions 的开发流程。

## 1. 开发 GitHub Actions

### 📂 目录规范

根据你要开发的类型，选择正确的目录：

- **Composite / Docker / JS Action**:
  - **位置**: `actions/<action-name>/`
  - **要求**: 必须在该目录下包含 `action.yml` 描述文件。
  - **适用场景**: 独立的逻辑封装，如“发送通知”、“构建 Docker 镜像”。

- **Reusable Workflow (可复用工作流)**:
  - **位置**: `.github/workflows/<workflow-name>.yml`
  - **要求**: 文件头部需定义 `on: workflow_call`。
  - **适用场景**: 完整的 CI/CD 流程模板，如“Java Maven 构建流程”。

## 2. 文档编写规范

### 📝 文档要求

每当新增一个 Action 或 Workflow 时，**必须**同步创建一份详细的使用说明文档。

- **存放位置**: `/docs/`
- **命名规则**: 与 Action 或 Workflow 同名。
  - 例如：Action 目录为 `actions/discord-notify/`，则文档应为 `docs/discord-notify.md`。
- **文档内容**:
  - **功能描述**: 该 Action 做什么。
  - **输入参数 (Inputs)**: 表格形式列出所有参数、是否必填、默认值。
  - **输出变量 (Outputs)**: 如有。
  - **使用示例**: 提供标准的 YAML 引用示例。
