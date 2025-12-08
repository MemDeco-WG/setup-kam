
Setup kam
 
A GitHub Action to install the latest version of kam (a Rust-based tool) with version-aware caching and Rust Cargo environment setup.
 
Changelog
 
- v1.1: Updated GitHub Marketplace branding (icon:  activity , color:  purple ) - no functional changes.
- v1: Initial release (core functionality: Rust + kam installation + version-aware caching).
 
Features
 
- Automatically installs Rust (with Cargo) via the official  dtolnay/rust-toolchain  action
- Fetches the latest kam version from crates.io API (no local cache bias)
- Caches the kam binary and Cargo dependencies to avoid redundant builds
- Skips installation if the latest version is already cached
- Cross-platform compatible (Linux, macOS, Windows)
- GitHub Marketplace branding (activity icon + purple color)
 
Usage
 
Basic Example
 
Add this to your GitHub Actions workflow file (e.g.,  .github/workflows/ci.yml ):
 
yaml
  

name: CI with kam
on: [push, pull_request]

jobs:
  build-and-test:
    runs-on: ubuntu-latest  # Supports ubuntu-latest, macos-latest, windows-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # Setup kam (v1.1 - with activity icon + purple branding)
      - name: Setup kam
        uses: MemDeco-WG/setup-kam@v1.1  # Use v1.1 for latest branding update

      # Verify installation and run kam commands
      - name: Verify kam installation
        run: kam --version

      - name: Run kam workflow
        run: kam <your-command-here>  # Replace with your actual kam command
 
 
Full Workflow Example (with Custom Steps)
 
yaml
  

name: Kam Pipeline
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  run-kam-operations:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup kam (latest version + cache + branding)
        uses: MemDeco-WG/setup-kam@v1.1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}  # Optional: Auto-injected by default
          enable-cache: 'true'  # Optional: set to 'false' to disable version-aware caching
          # Optional: import a template from URL (for example: https://example.com/your-template.tgz)
          template-url: https://example.com/your-template.tgz

      - name: List kam commands
        run: kam --help

      - name: Run custom kam task
        run: |
          # Example: Run kam build (replace with your use case)
          kam build
          # Add other kam commands as needed

      # Optional: run Setup kam and import a template provided by a workflow input
      # Example (pass workflow_dispatch input `template-url` to the action):
      # on:
      #   workflow_dispatch:
      #     inputs:
      #       template-url:
      #         description: 'Template URL'
      #         required: false
      #
      # In job steps:
      - name: Setup kam (with optional template import)
        uses: MemDeco-WG/setup-kam@v1.1
        with:
          template-url: ${{ github.event.inputs.template-url }}  # Will be ignored if empty
 
 
Inputs

- `github-token`: GitHub Token for cache optimization and rate limit enhancement (optional). Default: `${{ github.token }}`
- `enable-cache`: Toggle version-aware caching for kam and Cargo (optional). Default: `'true'` (set to `'false'` to disable caching)
- `template-url`: Optional URL to a template file (e.g., .tgz or archive) to download and import using `kam tmpl import`. Default: not set
 
How It Works
 
1. Install Rust: Uses dtolnay/rust-toolchain@stable to install the Rust toolchain (includes Cargo).
2. Fetch Latest kam Version: Queries the crates.io API to get the official latest version of kam.
3. Version-Aware Caching: Caches the kam binary and Cargo dependencies using a key that includes the kam version, OS, and Rust toolchain hash.
4. Conditional Installation: Skips installation if the latest version of kam is already cached; reinstalls only if a new version is available.
 
Troubleshooting
 
- Cache Misses: If the cache isn't being reused, check the workflow logs to confirm the latest kam version is correctly fetched from crates.io.
- Cross-Platform Issues: For Windows/macOS, ensure your workflow uses  runs-on: windows-latest  or  macos-latest  (the action handles OS-specific paths automatically).
- Rate Limits: If you encounter API rate limits, ensure the  github-token  input is set (uses GitHub's internal rate limits which are more generous).
 
License
 
This project is licensed under the MIT License - see the LICENSE file for details.
 
3. Quick Commands to Release v1.1 (Run in Terminal)
 
bash
  

# 1. Pull latest main branch (ensure you have all changes)
git pull origin main

# 2. Update action.yml and README.md with the above content, then commit
git add action.yml README.md
git commit -m "Release v1.1: Update branding (activity icon + purple color)"

# 3. Create v1.1 tag and push to GitHub
git tag v1.1
git push origin v1.1

# 4. (Optional) Create GitHub Release for v1.1
# Go to your repo → Releases → Draft a new release → Select v1.1 tag → Add release note:
# "v1.1: Updated GitHub Marketplace branding (icon: activity, color: purple) - no functional changes."
 
 
Key Notes
 
- Users can now use  uses: MemDeco-WG/setup-kam@v1.1  to get the branded version (v1 remains unchanged for backward compatibility).
- The changelog in README.md clarifies the v1.1 update (no functional changes, only branding).
- The GitHub Release (optional but recommended) helps users track updates.
 
Need help drafting the GitHub Release note or verifying the tag after pushing?
