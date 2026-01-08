# TangleGuard Scanner

TangleGuard creates an interactive graph diagram out of your source code and uploads it to the [TangleGuard Cloud](https://app.tangleguard.com).

This GitHub Action extracts structural information from your codebase to be analyzed on the web version of [TangleGuard](https://tangleguard.com).
Then you, all collaborators and basically everyone can explore the architecture of your project.

> [!IMPORTANT]
> The architecture of the scanned project will be made public.
> Private scan results will be supported in future.
> To delete the project from the directory, please contact the owner directly via kontakt@jaads.de or create an issue.

More information:

- Website https://tangleguard.com/
- Documentation https://docs.tangleguard.com/

## Procedure

Below you see what the action does in detail:

- Validates required input parameters
- Checks out your repository
- Downloads TangleGuard CLI and makes it executable
- Extracts repository metadata (owner, name, host) from `.git`
- Scans the workspace for architectural dependencies
- Uploads scan results with rich metadata to TangleGuard server where it can be explored

## Usage Example

There are four mandatory inputs:

- `language`: Which let's you select the correct scanner to be used
- `upload_results`: Must be set to "true" - the only supported option during evaluation phase
- `make_public`: Must be set to "true" - private scan results are not yet supported
- `description`: Project description for better identification on the website

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
          make_public: "true"
          language: "rust"
          description: "A Rust-based CLI tool for dependency analysis and architectural visualization"
```

## Inputs

| Input            | Description                                                            | Required | Default |
| ---------------- | ---------------------------------------------------------------------- | -------- | ------- |
| `upload_results` | Must be "true" - only supported option during evaluation phase         | Yes      | -       |
| `make_public`    | Must be "true" - private scan results are not yet supported            | Yes      | -       |
| `repository`     | Repository in format 'owner/project' (auto-detected from Git if empty) | No       | -       |
| `language`       | Programming language (rust/javascript)                                 | Yes      | -       |
| `path`           | Path to scan                                                           | No       | `.`     |
| `description`    | Project description for better identification on website               | Yes      | -       |


## License

This GitHub Action is licensed under the MIT License (see LICENSE file).

However, this action downloads and uses the TangleGuard CLI tool, which is proprietary software subject to separate license terms.
By using this GitHub Action, you agree to the [TangleGuard EULA](https://docs.tangleguard.com/legal/terms/).

**Summary:**
- **GitHub Action Code**: MIT License
- **TangleGuard CLI Tool**: Proprietary (see EULA)
