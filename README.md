# TangleGuard Scanner

[TangleGuard](https://tangleguard.com/) is a tool to monitor and enforce your software architecture.

With this GitHub Action you can integrate your architecture into [TangleGuard's public directory](https://app.tangleguard.com), where is can be **viewed and analyzed by anybody**. Private scans will be supported in the future too.

![Screenshot](screenshot.png)

## Inputs

This GitHub Action can be configured in a few ways, depending on your needs. Below you find some examples, which should help you get started.

| Input              | Description                                                                 | Required                         | Default |
| ------------------ | --------------------------------------------------------------------------- | -------------------------------- | ------- |
| `upload_results`   | Upload scan results to TangleGuard Cloud (public directory)                 | No                               | false   |
| `repository`       | Repository in format 'owner/project' (auto-detected from Git if empty)      | No                               | -       |
| `language`         | Programming language (rust/javascript)                                      | Yes                              | -       |
| `path`             | Path to scan                                                                | No                               | `.`     |
| `description`      | Project description for better identification on website                    | Yes (when `upload_results=true`) | -       |
| `ignore_paths`     | Comma-separated list of directories to ignore (e.g., 'examples,benchmarks') | No                               | -       |
| `fail_on_findings` | Fail the workflow if circular dependencies are found                        | No                               | true    |

## Usage Example

### On PR: Fail if circular dependencies were found

Use this example if you only want to validate your codebase for circular dependencies and fail the workflow if any are found. This is useful for enforcing architecture rules in pull requests.

Copy it over if you like. It's a working example and a very common use case.

```yaml
name: Architecture Validation
on: [pull_request]
jobs:
  validate-architecture:
    runs-on: ubuntu-latest
    steps:
      - uses: TangleGuard/github-action@main
        with:
          language: "javascript"
          fail_on_findings: "true" # Fail if circular dependencies are found
```

Set `fail_on_findings: "false"` if you want to see the validation results without failing the workflow.

### On PR: Upload to public directory

This is for open source projects ONLY.

We'd be happy to host the UI for the monitoring if you work on a public projects which is licensed under the MIT license or Apache 2.0 license.

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
          language: "rust" # <-- ADJUST
          description: "A CLI tool that.. " # <-- ADJUST (required when uploading)
```

Private and proprietary repositories will be supported, too. TangleGuard keeps you architecture data very serious. To support private repositories, TangleGuard wants to have setup a proper multi-tenant secure platform. This will need resources to implement and infrastructure. If you are interested in a hosted, private version of TangleGuard, please contact us at kontakt@jaads.de.

## Deletion of projects from the public directory

To delete the project from the directory, please contact the owner directly via kontakt@jaads.de or create an issue.

## License

This GitHub Action is licensed under the MIT License (see LICENSE file).

However, this action downloads and uses the TangleGuard CLI tool, which is proprietary software subject to separate license terms.
By using this GitHub Action, you agree to the [TangleGuard EULA](https://docs.tangleguard.com/legal/terms/).

**Summary:**

- **GitHub Action Code**: MIT License
- **TangleGuard CLI Tool**: Proprietary (see EULA)
