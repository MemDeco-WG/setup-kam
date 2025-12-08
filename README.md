# Setup kam

[![Release](https://img.shields.io/github/v/tag/MemDeco-WG/setup-kam?label=release)](https://github.com/MemDeco-WG/setup-kam/releases) [![License](https://img.shields.io/github/license/MemDeco-WG/setup-kam.svg)](https://github.com/MemDeco-WG/setup-kam/blob/main/LICENSE)

Install `kam` in your GitHub Actions runner with optional caching and template import.

Usage

Basic example:

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

With template import and caching:

```yaml
- name: Setup kam (with template)
  uses: MemDeco-WG/setup-kam@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}   # optional
    enable-cache: 'true'                        # optional, default true
    template-url: https://example.com/template.zip
```

Inputs

- `github-token` (optional): Default `${{ github.token }}`.
- `enable-cache` (optional): `'true'` (default) or `'false'` to disable caching.
- `template-url` (optional): URL to a template archive (e.g., `.zip`, `.tgz`). The URL must include a filename and extension; if you download manually, preserve the extension or provide the extracted directory path to `kam tmpl import`.

Note: Prefer `uses: MemDeco-WG/setup-kam@v1` (major alias) rather than pinning patch versions.