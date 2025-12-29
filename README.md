# Setup kam

[![Release](https://img.shields.io/github/v/tag/MemDeco-WG/setup-kam?label=release)](https://github.com/MemDeco-WG/setup-kam/releases) [![License](https://img.shields.io/github/license/MemDeco-WG/setup-kam.svg)](https://github.com/MemDeco-WG/setup-kam/blob/main/LICENSE)

Install `kam` in your GitHub Actions runner with optional caching and template import.

Important
- By default the `private-key` input is optional. If it is not provided the action will continue but will print a warning. To make the key mandatory set `require-private-key: 'true'`. When provided the key will be imported into the `kam` keyring (secret name `main`) and a random passphrase will be exported to `KAM_SIGN_PASSPHRASE` (masked).
- The action will attempt to install Commitizen (`python-commitizen`) via `uv` when `install-commitizen` is `'true'` (default). Verification prefers module invocation (`python -m commitizen`) and falls back to the `cz` CLI if present. If Commitizen cannot be verified and `require-commitizen` is `'true'`, the action will fail; otherwise it will continue and emit a warning.
- The action performs preflight checks for required tools (such as `curl` and `openssl`) and will fail fast with guidance if missing.
- To avoid unnecessary recompilation when a later step in the job fails after a successful build, this action saves the compiled `kam` artifacts to the GitHub Actions cache immediately after installation. The cache save is guarded so it only runs when a working `kam` binary is present, and it is executed with an `always()`-style guard so that it will still attempt to save the build even if later steps fail. Keep `enable-cache: 'true'` (the default) and ensure files used in the cache key (for example `Cargo.lock`) are present so the cache can be reliably restored on subsequent runs.

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
    kam-version: '0.3.1'                             # optional, pin version
    private-key: ${{ secrets.KAM_PRIVATE_KEY }}      # optional (see 'require-private-key' to enforce)
    require-private-key: 'true'                      # optional (fail if private-key not provided)
```

Inputs

- `github-token` (optional): Default `${{ github.token }}`. Use this if you need authenticated access to private releases/assets.
- `enable-cache` (optional): `'true'` (default) or `'false'` to disable caching.
- `kam-version` (optional): Pin a specific kam version to install (e.g., `'0.1.2'`). When set, network lookup for the latest version is skipped and the pinned version will be used for installation and cache keys.
- `template-url` (optional): URL to a template archive (e.g., `.zip`, `.tgz`, `.tar.gz`). The URL must include a filename and extension; if you download manually, preserve the extension or provide the extracted directory path to `kam tmpl import`.
- `private-key` (optional): The KAM private key contents (PEM). Provide it through a repository secret, for example:
  `private-key: ${{ secrets.KAM_PRIVATE_KEY }}`. If provided, the action will import it into the kam keyring (using secret name `main`) and export an auto-generated passphrase as `KAM_SIGN_PASSPHRASE` (masked).
- `require-private-key` (optional): `'false'` (default) or `'true'`. When `'true'` the action will fail early if `private-key` is not provided.
- `install-commitizen` (optional): `'true'` (default) or `'false'`. Attempt to install commitizen via `uv`. If you prefer not to install commitizen in the runner, set to `'false'`.
- `require-commitizen` (optional): `'false'` (default) or `'true'`. When `'true'` the action will fail if commitizen cannot be verified after attempted installation.

Outputs

- `latest-version`: Resolved kam version (from crates.io/GitHub or pinned).
- `installed-version`: Currently installed kam version (string, or empty).
- `should-install`: `'true'` if the action decided to install kam.
- `key-imported`: `'true'` if the private key was successfully imported.

Notes
- The action attempts to install `python-commitizen` and verifies it's callable (preferring `python -m commitizen` and falling back to `cz` if present). This behavior is controlled by `install-commitizen` and `require-commitizen` inputs; if verification fails and `require-commitizen` is `'true'`, the action will fail, otherwise it will continue with a warning.
- The action performs preflight checks for required tools (e.g., `curl`, `openssl`, and archive utilities if `template-url` points to an archive) and will fail early with guidance if they are missing.
- When a `private-key` is imported the action masks sensitive values and sets `KAM_SIGN_PASSPHRASE` in the environment for subsequent steps (masked in logs).
- Prefer `uses: MemDeco-WG/setup-kam@v1` (major alias) rather than pinning patch versions for stable behavior.
