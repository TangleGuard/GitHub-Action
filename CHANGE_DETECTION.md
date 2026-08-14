# Change Detection Feature - Updated Implementation

## Overview

The change detection feature now uses the TangleGuard CLI's built-in `diff` command to provide structured comparison between base and head commits in pull requests.

## Architecture

### Flow

1. **Base Analysis**: Run validation on PR base commit → output `base.json`
2. **Head Analysis**: Run validation on PR head commit → output `head.json`
3. **Diff Computation**: Run `diff` to compare → output `diff.json`
4. **PR Comment**: Parse `diff.json` and post structured comment

## CLI Commands Used

### Validation on Base/Head

```bash
./tangleguard-cli -l rust -p . validate > base.json
```

**Output Structure:**

```json
{
  "rule_violations": [
    {
      "from": { "package": "api", "module": "handlers" },
      "to": { "package": "db", "module": "repository" },
      "message": "optional message"
    }
  ],
  "circles": [
    {
      "cycle": ["pkg::a", "pkg::b", "pkg::a"],
      "package": "pkg"
    }
  ]
}
```

### Diff Command

```bash
./tangleguard-cli diff base.json head.json > diff.json
```

**Output Structure:**

```json
{
  "rule_violations_added": [...],
  "rule_violations_removed": [...],
  "rule_violations_unchanged": [...],
  "circles_added": [...],
  "circles_removed": [...],
  "circles_unchanged": [...],
  "summary": {
    "status": "worsened",
    "rule_violations_added_count": 2,
    "rule_violations_removed_count": 0,
    "circles_added_count": 1,
    "circles_removed_count": 0
  }
}
```

## Status Determination

The `status` field in the diff summary determines the overall change:

| Status      | Meaning                   | Example                       |
| ----------- | ------------------------- | ----------------------------- |
| `clean`     | No issues in base or head | Both commits are clean        |
| `improved`  | Issues fixed, none added  | 3 violations removed, 0 added |
| `worsened`  | New issues introduced     | 0 violations removed, 2 added |
| `unchanged` | Same issues in both       | Existing issues persist       |
| `mixed`     | Some fixed, some added    | 1 violation removed, 1 added  |

## PR Comment Format

### Example: Worsened Status

```markdown
## ❌ TangleGuard Architecture Analysis

**New Issues Introduced**

**This PR introduces new architecture violations!** Please review the details below and fix the issues before merging.

### 📊 Summary

• ➕ **2** new rule violation(s)
• ➕ **1** new circular dependency

<details>
<summary>📋 Details</summary>

**❌ New Rule Violations:**
• api::handlers → db::repository
• core::domain → infra::database

**❌ New Circular Dependencies:**
• myapp::a → myapp::b → myapp::a

</details>

---

🔍 Analyzed `abc123` → `def456` by TangleGuard | Documentation
```

### Example: Improved Status

```markdown
## 🎉 TangleGuard Architecture Analysis

**Issues Resolved**

Great job! This PR fixes existing architecture issues.

### 📊 Summary

• ➖ **2** rule violation(s) fixed

<details>
<summary>📋 Details</summary>

**✅ Fixed Rule Violations:**
• api → db
• core → infra

</details>

---

🔍 Analyzed `abc123` → `def456` by TangleGuard | Documentation
```

## Configuration

### Input Parameters

- **`detect_change`** (default: `false`) - Enable change detection mode
- **`fail_on_findings`** (default: `true`) - Fail action on violations
- **`path`** (optional) - Workspace path
- **`ignore_paths`** (optional) - Directories to ignore

### Example Workflow

```yaml
name: TangleGuard PR Check
on: [pull_request]

permissions:
  pull-requests: write # Required to post PR comments

jobs:
  architecture-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Required for base commit access

      - uses: TangleGuard/github-action@main
        with:
          detect_change: "true"
          fail_on_findings: "true"
```

## Exit Codes

The action uses the diff exit code to determine success/failure:

- **Exit 0**: No new issues introduced
- **Exit 1**: New issues introduced (when `fail_on_findings: true`)
- **Exit 2**: System error (file not found, parse error, etc.)

## Benefits

1. **Structured Data**: JSON format allows precise comparison
2. **Rich Details**: Shows exactly what changed (added/removed violations)
3. **Better UX**: Clear summary with expandable details
4. **Maintainability**: Logic in CLI, not in bash/JS
5. **Testability**: Diff logic has unit tests in core module
6. **Reusability**: `diff` command can be used standalone

## Troubleshooting

### Common Issues

**Issue**: `Error reading base file 'base.json': No such file or directory`

- **Cause**: Validation command failed to produce output
- **Solution**: Check validation command syntax, ensure workspace can be scanned

**Issue**: `Error parsing base JSON`

- **Cause**: Validation produced invalid JSON (possibly error output)
- **Solution**: Check for errors in validation step, review base.json content

**Issue**: `Failed to parse diff JSON`

- **Cause**: Diff command failed or produced invalid output
- **Solution**: Verify both base.json and head.json are valid, check diff command logs

### Debug Tips

1. Check step outputs in GitHub Actions logs
2. Verify JSON files are created (`ls -la *.json`)
3. Validate JSON syntax (`cat base.json | jq .`)
4. Run diff command locally to test

## Future Enhancements

Potential improvements:

1. **Inline Comments**: Comment on specific files/lines where violations occur
2. **Trend Analysis**: Track violations over time
3. **Auto-fix Suggestions**: Propose code changes to fix violations
4. **Severity Levels**: Distinguish between warning and error level violations
5. **Custom Rules**: Allow per-repo rule configuration in PR checks
