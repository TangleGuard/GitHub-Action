# TangleGuard Scanner

A GitHub Action that scans your codebase and uploads architectural analysis results to the TangleGuard server with automatic repository metadata extraction.

More information can be found at https://docs.tangleguard.com/

## Procedure

- Checks out your repository
- Downloads TangleGuard CLI and makes it executable
- Automatically extracts repository metadata (owner, name, description, stars, topics) from GitHub API
- Scans the workspace for architectural dependencies
- Uploads scan results with rich metadata to TangleGuard server

## Inputs

| Input            | Description                                                            | Required | Default |
| ---------------- | ---------------------------------------------------------------------- | -------- | ------- |
| `upload_results` | Upload scan results to TangleGuard server                              | Yes      | -       |
| `repository`     | Repository in format 'owner/project' (auto-detected from Git if empty) | No       | -       |
| `language`       | Programming language (rust/javascript)                                 | Yes      | -       |
| `path`           | Path to scan                                                           | No       | `.`     |
| `description`    | Project description (auto-detected from GitHub API if empty)           | No       | -       |

## Usage

### Basic Example (Auto-detects repository info from GitHub API)

```yaml
name: TangleGuard Scan
on: [push, pull_request]

jobs:
  scan-workspace:
    runs-on: ubuntu-latest
    steps:
      - uses: TangleGuard/github-action@main
        with:
          upload_results: "true"
          language: "rust"
```

### Manual Repository Info

```yaml
name: TangleGuard Scan
on: [push, pull_request]

jobs:
  scan-workspace:
    runs-on: ubuntu-latest
    steps:
      - uses: TangleGuard/github-action@main
        with:
          upload_results: "true"
          repository: "my-org/my-project"
          language: "rust"
          description: "Production scan for ${{ github.ref_name }} branch"
```

### Mixed Approach (Manual repository, auto-detect description)

```yaml
name: TangleGuard Scan
on: [push, pull_request]

jobs:
  scan-workspace:
    runs-on: ubuntu-latest
    steps:
      - uses: TangleGuard/github-action@main
        with:
          upload_results: "true"
          repository: "my-org/my-project"
          language: "rust"
```

## Enhanced Repository Metadata Detection

The action automatically extracts comprehensive repository information:

## License

This GitHub Action is licensed under the MIT License.

However, this action downloads and uses TangleGuard CLI, which is a proprietary software with its own license terms.
