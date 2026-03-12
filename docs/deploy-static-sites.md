# Deploy Static Sites Reusable Workflow

该 Reusable Workflow 用于将 `products/<product>/` 下的静态站点内容通过 `rsync` 部署到远程服务器，并在部署完成后可选地执行 `nginx -s reload`。

## 必要 Secrets

在**调用该 Workflow 的仓库**中配置以下 Secrets（Settings -> Secrets and variables -> Actions）：

| 名称 | 描述 |
|---|---|
| `SERVER_HOST` | 服务器地址（IP/域名） |
| `SERVER_USER` | SSH 用户名 |
| `SERVER_PASS` | SSH 密码（用于 `sshpass`） |

## Inputs

| 参数 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `products_dir` | string | `products` | 产品根目录（目录下每个子目录视为一个 product） |
| `deploy_path` | string | `/var/www/static-sites` | 远程部署根路径 |
| `force_deploy` | boolean | `false` | 为 `true` 时部署 `products_dir` 下所有产品 |
| `max_parallel` | number | `3` | 并行部署数量 |
| `reload_nginx` | boolean | `true` | 是否在部署后 reload nginx |
| `nginx_container` | string | `static-sites` | nginx 所在 Docker 容器名 |

## 使用示例（在 TML-site 中）

在 `TML-site` 仓库创建/修改 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Static Sites

on:
  push:
    branches: [master]
    paths:
      - 'products/**'
  workflow_dispatch:
    inputs:
      force_deploy:
        description: Force deploy all products
        type: boolean
        required: false
        default: false

jobs:
  deploy:
    uses: Time-Machine-Lab/TML-Github_Actions/.github/workflows/deploy-static-sites.yml@main
    with:
      force_deploy: ${{ github.event_name == 'workflow_dispatch' && inputs.force_deploy }}
    secrets:
      SERVER_HOST: ${{ secrets.SERVER_HOST }}
      SERVER_USER: ${{ secrets.SERVER_USER }}
      SERVER_PASS: ${{ secrets.SERVER_PASS }}
```

## 工作原理

- 默认：
  - `push` 事件：比较本次推送范围（`github.event.before..github.sha`）在 `products_dir/` 下的变更，提取受影响的 `<product>` 并按矩阵并行部署。
  - 其他事件：若存在上一提交则使用 `HEAD~1..HEAD` 作为兜底 diff 范围。
- `force_deploy: true`：忽略 diff，部署 `products_dir` 下所有产品目录。

## 注意事项

- 该 Workflow 使用 `sshpass` 进行密码登录；如需更安全的 SSH Key 方式，我可以继续帮你改造。
- 远程服务器需安装 `rsync`，并允许 `SERVER_USER` 写入 `deploy_path`。
- `reload_nginx` 会在远程服务器执行：`docker exec <nginx_container> nginx -s reload || true`，请确保容器名与 nginx 命令符合实际环境。
- 部署阶段会将服务器写入 `~/.ssh/known_hosts` 并启用 Host Key 校验；如服务器 Host Key 变更，需要更新对应配置。
