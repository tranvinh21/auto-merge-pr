# Auto Merge PR Action

A GitHub Action that automatically merges pull requests when they are created or on manual trigger.

## Features

- Automatically merges PRs as soon as they're opened (if mergeable)
- Can be manually triggered to merge specific PRs
- Supports different merge methods (merge, squash, rebase)
- Adds informative comments to PRs about merge status
- Works with both the default `GITHUB_TOKEN` or a custom Personal Access Token
- Provides detailed error messages and troubleshooting suggestions

## Usage

### Basic Usage (Auto-merge on PR Creation)

Add this to your `.github/workflows/auto-merge.yml` file:

```yaml
name: Auto Merge Pull Requests
on:
  pull_request:
    types: [opened]

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    steps:
      - uses: tranvinh21/auto-merge-pr-action@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```

### Usage with Personal Access Token (PAT)

For more permissions (like bypassing branch protections or triggering other workflows):

```yaml
name: Auto Merge Pull Requests
on:
  pull_request:
    types: [opened]

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    steps:
      - uses: tranvinh21/auto-merge-pr-action@v1
        with:
          token: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
```

### Manual Trigger Workflow

You can also set up a workflow to manually merge specific PRs:

```yaml
name: Manual PR Merge
on:
  workflow_dispatch:
    inputs:
      pr_number:
        description: 'PR number to merge'
        required: true
        type: number
      merge_method:
        description: 'Merge method (merge, squash, rebase)'
        required: false
        default: 'merge'
        type: choice
        options:
          - merge
          - squash
          - rebase

jobs:
  manual-merge:
    runs-on: ubuntu-latest
    steps:
      - uses: tranvinh21/auto-merge-pr-action@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.inputs.pr_number }}
          merge_method: ${{ github.event.inputs.merge_method }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `token` | GitHub token or PAT with permissions to merge PRs | Yes | - |
| `pr_number` | Pull request number to merge | No (will use triggering PR if not provided) | - |
| `merge_method` | Merge method to use: merge, squash, or rebase | No | `merge` |

## Outputs

| Output | Description |
|--------|-------------|
| `merge_success` | 'true' if the PR was successfully merged |
| `merge_sha` | The SHA of the merge commit if successful |
| `merge_error` | Error message if the merge failed |

## Permissions Required

For the `GITHUB_TOKEN`:

- `contents: write` - To merge the PR
- `pull-requests: write` - To comment on PRs

If using a Personal Access Token (PAT), it needs at least:

- `repo` scope (for private repositories)
- `public_repo` scope (for public repositories)

## Branch Protection Considerations

If your repository has branch protection rules:

- Required status checks must pass before merge
- Required reviews must be approved
- Your token must have sufficient permissions

## Using with Actions Requiring Further Authentication

If you need the action to trigger other workflows, use a PAT instead of the default `GITHUB_TOKEN`:

1. Create a Personal Access Token with appropriate scopes
2. Add it as a repository secret (e.g., `PERSONAL_ACCESS_TOKEN`)
3. Use this token instead of `GITHUB_TOKEN` in the workflow

## License

MIT License
