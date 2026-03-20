# OpenReview

自动代码审查 GitHub Action，使用 OpenCode AI 进行 PR 审查。

## 功能

- 自动审查 PR 中的代码变更
- 支持通过评论触发审查 (`/oc` 或 `/opencode`)
- 在 PR 上发布内联评论，指出潜在问题
- 自动批准无问题的 PR

## 使用方法

创建 `.github/workflows/openreview.yml`:

```yaml
on:
  pull_request_review_comment:
    types: [created]

jobs:
  review:
    if: |
      contains(github.event.comment.body, '/oc') ||
      contains(github.event.comment.body, '/opencode')

    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
      pull-requests: write
      issues: read

    steps:
      - uses: actions/checkout@v4

      - name: Set Git user info
        run: |
          git config --global user.email "${{ secrets.CI_EMAIL }}"
          git config --global user.name "${{ secrets.CI_USER }}"

      - name: OpenReview
        uses: yang-f/openreview@v0.0.1
```

## 配置

### 触发方式

在 PR 评论中输入 `/oc` 或 `/opencode` 即可触发审查。

### 权限要求

- `id-token: write` - OIDC 认证
- `contents: read` - 读取仓库内容
- `pull-requests: write` - 写入 PR 评论
- `issues: read` - 读取 issues

## 输出

审查结果以 JSON 格式输出，包含：

- `summary`: 整体审查总结
- `comments`: 问题列表，包含文件路径、行号、严重程度和评论内容

### 严重程度

- `nit`: 轻微建议
- `minor`: 小问题
- `major`: 重要问题
- `critical`: 严重问题

## 许可证

MIT License