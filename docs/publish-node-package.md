# Publish Node Package Action

This composite action installs dependencies, builds a Node.js package, derives the package version from the Git tag, configures a custom npm registry, and publishes the package.

## Required Secrets

Configure these repository secrets in the caller repository:

| Name | Description |
|---|---|
| `NPM_REGISTRY_URL` | Registry URL used for publishing |
| `NPM_TOKEN` | Registry auth token |

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `working_directory` | No | `.` | Package directory relative to the repository root |
| `node_version` | No | `20.x` | Node.js version for `actions/setup-node` |
| `cache_dependency_path` | No | `package-lock.json` | Lockfile path used for npm cache |
| `build_command` | No | `npm run build` | Build command. Leave empty to skip |
| `publish_command` | No | `npm publish` | Publish command. Leave empty to skip |
| `tag_prefix` | No | `v` | Prefix removed from `github.ref_name` before writing package version |
| `registry_url` | Yes | None | Registry URL, usually passed from `secrets.NPM_REGISTRY_URL` |
| `npm_token` | Yes | None | Registry token, usually passed from `secrets.NPM_TOKEN` |

## Usage Example

Create or update `.github/workflows/publish.yml` in the caller repository:

```yaml
name: Publish TMLSPEC CLI

on:
  push:
    tags:
      - 'v*'

permissions:
  contents: read

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Publish package
        uses: Time-Machine-Lab/TML-Github_Actions/actions/publish-node-package@main
        with:
          working_directory: TMLSPEC-cli
          node_version: '20.x'
          cache_dependency_path: TMLSPEC-cli/package-lock.json
          build_command: npm run build
          publish_command: npm publish
          tag_prefix: v
          registry_url: ${{ secrets.NPM_REGISTRY_URL }}
          npm_token: ${{ secrets.NPM_TOKEN }}
```

## Notes

- The caller workflow must run on a tag context if you want the package version to be derived from `github.ref_name`.
- The caller workflow should checkout the repository before invoking this action.
- The action writes `~/.npmrc` on the runner to configure the target registry for the publish step.
