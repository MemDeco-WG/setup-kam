# Setup kam（中文）

[![Release](https://img.shields.io/github/v/tag/MemDeco-WG/setup-kam?label=release)](https://github.com/MemDeco-WG/setup-kam/releases) [![License](https://img.shields.io/github/license/MemDeco-WG/setup-kam.svg)](LICENSE)

在 GitHub Actions 中安装 kam（Rust 工具），并支持可选模板导入与缓存。

用法（最简）：

基本示例：
```yaml
name: CI with kam
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: MemDeco-WG/setup-kam@v1
      - run: kam --version
```

带模板导入示例：
```yaml
- uses: MemDeco-WG/setup-kam@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}   # 可选
    enable-cache: 'true'                        # 可选（默认 true）
    template-url: https://example.com/template.zip
```

输入项（简要）：
- `github-token`：可选，默认 `${{ github.token }}`。
- `enable-cache`：可选，默认 `'true'`，设置为 `'false'` 可禁用缓存。
- `template-url`：可选。请确保 URL 包含文件名和扩展名（例如 `.zip` / `.tgz`），或传入已解压目录路径。

说明：
- 推荐在 `uses:` 中使用 `@v1`（major alias），避免固定到某个补丁版本。
- `template-url` 下载文件时必须保留扩展名，否则 `kam tmpl import` 可能无法正确识别并拒绝导入。

许可证：MIT