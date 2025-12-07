Setup kam
 
A GitHub Action to install the latest version of kam (a Rust-based tool) with version-aware caching and Rust Cargo environment setup.
 
Features
 
- Automatically installs Rust (with Cargo) via the official  dtolnay/rust-toolchain  action
- Fetches the latest kam version from crates.io API (no local cache bias)
- Caches the kam binary and Cargo dependencies to avoid redundant builds
- Skips installation if the latest version is already cached
- Cross-platform compatible (Linux, macOS, Windows)
 
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

      # Setup kam (installs Cargo + latest kam with caching)
      - name: Setup kam
        uses: your-username/setup-kam@v1  # Replace with your GitHub repo URL and version

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

      - name: Setup kam (latest version + cache)
        uses: your-username/setup-kam@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}  # Optional: Auto-injected by default

      - name: List kam commands
        run: kam --help

      - name: Run custom kam task
        run: |
          # Example: Run kam build (replace with your use case)
          kam build
          # Add other kam commands as needed
 
 
Inputs
 
Input Name Description Required Default Value 
 github-token  GitHub Token for cache optimization and rate limit enhancement No  ${{ github.token }}  
 
How It Works
 
1. Install Rust: Uses  dtolnay/rust-toolchain@stable  to install the minimal Rust toolchain (includes Cargo).
2. Fetch Latest kam Version: Queries the crates.io API to get the official latest version of kam.
3. Version-Aware Caching: Caches the kam binary and Cargo dependencies using a key that includes the kam version, OS, and Rust toolchain hash.
4. Conditional Installation: Skips installation if the latest version of kam is already cached; reinstalls only if a new version is available.
 
Troubleshooting
 
- Cache Misses: If the cache isn't being reused, check the workflow logs to confirm the latest kam version is correctly fetched from crates.io.
- Cross-Platform Issues: For Windows/macOS, ensure your workflow uses  runs-on: windows-latest  or  macos-latest  (the action handles OS-specific paths automatically).
- Rate Limits: If you encounter API rate limits, ensure the  github-token  input is set (uses GitHub's internal rate limits which are more generous).
 
License
 
This project is licensed under the MIT License - see the LICENSE file for details.
 
 
