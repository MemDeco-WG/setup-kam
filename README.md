# Setup kam

[![Release](https://img.shields.io/github/v/tag/MemDeco-WG/setup-kam?label=release)](https://github.com/MemDeco-WG/setup-kam/releases) [![License](https://img.shields.io/github/license/MemDeco-WG/setup-kam.svg)](https://github.com/MemDeco-WG/setup-kam/blob/main/LICENSE)

Install `kam` in your GitHub Actions runner with optional caching and template import.

Important
- This action now requires a KAM private key (`private-key`). If you do not provide `private-key` (e.g. via `secrets.KAM_PRIVATE_KEY`), the action will fail early and print instructions on how to obtain one.
- This action automatically installs `python-commitizen` (Commitizen) and verifies that the `cz` command is available. If `cz` cannot be installed or made available, the action will fail.

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
      - name: Setup kam
        uses: MemDeco-WG/setup-kam@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          private-key: ${{ secrets.KAM_PRIVATE_KEY }}   # required
      - run: kam --version
```

With template import and caching:

```yaml
- name: Setup kam (with template)
  uses: MemDeco-WG/setup-kam@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}        # optional (recommended)
    enable-cache: 'true'                             # optional, default true
    template-url: https://example.com/template.zip   # optional
    private-key: ${{ secrets.KAM_PRIVATE_KEY }}      # required
```

Inputs

- `github-token` (optional): Default `${{ github.token }}`. Use this if you need authenticated access to private releases/assets.
- `enable-cache` (optional): `'true'` (default) or `'false'` to disable caching.
- `template-url` (optional): URL to a template archive (e.g., `.zip`, `.tgz`, `.tar.gz`). The URL must include a filename and extension; if you download manually, preserve the extension or provide the extracted directory path to `kam tmpl import`.
- `private-key` (required): The KAM private key contents (PEM). Provide it through a repository secret, for example:
  `private-key: ${{ secrets.KAM_PRIVATE_KEY }}`. The action will fail early with a helpful message if this input is missing. If you do not have a private key yet, please apply at https://developers.kernelsu.org.

Notes
- This action installs `python-commitizen` and ensures the `cz` CLI is present in the runner PATH; this is useful for consistent commit message and release management workflows. If the installation fails or `cz` cannot be found, the action will fail.
- Prefer `uses: MemDeco-WG/setup-kam@v1` (major alias) rather than pinning patch versions for stable behavior.

