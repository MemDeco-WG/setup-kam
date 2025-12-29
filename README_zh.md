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
    kam-version: '0.3.1'                        # 可选：固定 kam 版本
    private-key: ${{ secrets.KAM_PRIVATE_KEY }} # 可选；使用 require-private-key: 'true' 强制要求
    require-private-key: 'false'                # 可选（默认 'false'）
    install-commitizen: 'true'                  # 可选（默认 'true'）
    require-commitizen: 'false'                 # 可选（默认 'false'）
```

输入项（简要）：
- `github-token`：可选，默认 `${{ github.token }}`。
- `enable-cache`：可选，默认 `'true'`，设置为 `'false'` 可禁用缓存。
- `kam-version`：可选。用于固定要安装的 kam 版本（例如 `kam-version: '0.1.2'`）。设置后将跳过网络查询并使用该版本用于安装与缓存键。
- `template-url`：可选。请确保 URL 包含文件名和扩展名（例如 `.zip` / `.tgz` , `.tar.gz`），或传入已解压目录路径。
- `private-key`：可选。KAM 私钥 PEM 内容（例如 `${{ secrets.KAM_PRIVATE_KEY }}`）。如果提供，action 会将其导入到 kam 密钥环（默认密钥名为 `main`），并导出自动生成的密码到 `KAM_SIGN_PASSPHRASE`（在日志中被掩码）。如需强制提供私钥，请设置 `require-private-key: 'true'`。
- `require-private-key`：可选，默认 `'false'`。设置为 `'true'` 时，当 `private-key` 未提供时 action 会直接失败。
- `install-commitizen`：可选，默认 `'true'`。是否尝试通过 `uv` 安装 `python-commitizen`（Commitizen）。
- `require-commitizen`：可选，默认 `'false'`。设置为 `'true'` 时，如果安装后无法校验 commitizen，action 将失败。

输出：
- `latest-version`：解析到的 kam 版本（来自 crates.io/GitHub，或 `kam-version` 固定值）。
- `installed-version`：action 结束时 PATH 上的 kam 版本（如果存在）。
- `should-install`：如果 action 决定需要安装 kam 则为 `'true'`。
- `key-imported`：如果成功将私钥导入到 kam 密钥环，则为 `'true'`。

说明：
- 推荐在 `uses:` 中使用 `@v1`（major alias），避免固定到某个补丁版本。
- `template-url` 下载文件时必须保留扩展名，否则 `kam tmpl import` 可能无法正确识别并拒绝导入。
- 本 Action 会尝试安装 `python-commitizen`（Commitizen）并校验其可用性（优先使用 `python -m commitizen`，无法时退回使用 PATH 中的 `cz`）。默认情况下这是一个非致命的警告；若要在校验失败时让 action 直接失败，请设置 `require-commitizen: 'true'`。
- 在运行前，Action 会对若干外部工具做预检（例如 `curl`、`openssl`，以及当 `template-url` 指向压缩包时需要的归档工具），如缺失则会失败并给出可复现的提示信息。
- 为避免在后续步骤失败导致需要重新编译的情况，Action 会在成功安装 `kam` 后尽快将编译产物保存到 GitHub Actions 缓存（当启用缓存时）。保存步骤会先确认 `kam` 二进制可用，并在 `always()` 风格的守护下执行（因此即使后续步骤失败也会尝试保存），从而减少下次运行时重新编译的概率。请保持 `enable-cache: 'true'` 并确保用于缓存键的文件（例如 `Cargo.lock`）存在，以便缓存能可靠地被恢复。

许可证：MIT
