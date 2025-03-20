# GitHub Auto-Merge Pull Request Action

This GitHub Action automatically merges pull requests when they are created, reopened, or marked as ready for review.

## How It Works

When a pull request is opened, reopened, or marked as ready for review, this action:

1. Checks if the pull request is mergeable
2. Waits for GitHub to calculate the mergeable status if needed
3. Automatically merges the pull request if it's mergeable
4. Provides detailed logs about the merge process

## Setup Instructions

### 1. Create a Personal Access Token (PAT)

You need a GitHub Personal Access Token with the necessary permissions to merge pull requests:

1. Go to your GitHub account settings: <https://github.com/settings/tokens>
2. Click "Generate new token" (classic)
3. Give your token a descriptive name (e.g., "Auto-Merge PR Token")
4. Select the following scopes:
   - `repo` (Full control of private repositories)
   - `workflow` (if you want to trigger other workflows)
5. Click "Generate token"
6. **Important**: Copy the token immediately, as you won't be able to see it again

### 2. Add the Token as a Repository Secret

1. Go to your repository settings
2. Click on "Secrets and variables" → "Actions"
3. Click "New repository secret"
4. Name: `PERSONAL_ACCESS_TOKEN`
5. Value: Paste your personal access token
6. Click "Add secret"

### 3. The Workflow File

The workflow file is already set up at `.github/workflows/auto-merge-pr.yml`. It uses the personal access token to authenticate with the GitHub API and merge pull requests.

## Configuration Options

### Merge Method

By default, the action uses the "merge" method to merge pull requests. You can customize this by adding an input parameter to your workflow:

```yaml
on:
  pull_request:
    types: [opened, reopened, ready_for_review]
  workflow_dispatch:
    inputs:
      merge_method:
        description: 'Merge method (merge, squash, rebase)'
        required: false
        default: 'merge'
        type: choice
        options:
          - merge
          - squash
          - rebase
```

This allows you to trigger the workflow manually and select a merge method.

## Limitations

- The action will only merge pull requests that GitHub considers mergeable
- Pull requests with merge conflicts, failing checks, or required reviews will not be merged
- Branch protection rules still apply

## Troubleshooting

If the action fails to merge a pull request, check the workflow logs for detailed error messages. Common issues include:

- Insufficient permissions for the personal access token
- Branch protection rules preventing the merge
- Pull request has merge conflicts
- Required status checks are failing
- Required reviews are missing

## License

This project is licensed under the MIT License - see the LICENSE file for details.
