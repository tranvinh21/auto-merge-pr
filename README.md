# GitHub Auto-Merge Pull Request Action

This GitHub Action automatically merges pull requests when they are created.

## How It Works

The Auto-Merge Pull Request Action has two modes:
lkjsdf
1. **Event-triggered**: When a pull request is opened, it attempts to merge it immediately
2. **Manual trigger**: You can manually run the workflow on a specific PR by providing its number

When it runs, the action:sdf

1. Verifies the PR is not in draft state
2. Checks if the pull request is mergeable
3. Waits for GitHub to calculate the mergeable status if needed
4. Automatically merges the pull request if it's mergeable
5. Adds a comment to the PR indicating it was auto-merged upon creation
6. Provides detailed logs about the merge process

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

The action supports three merge methods:

- **merge** (default): Creates a merge commit
- **squash**: Squashes all commits into a single commit
- **rebase**: Rebases and fast-forwards the PR commits

### Manual Triggers

You can manually trigger the workflow with these options:

- **merge_method**: Choose between merge, squash, and rebase
- **pr_number**: Specify the PR number to process (required)

## Limitations

- The action will only merge pull requests that GitHub considers mergeable
- Pull requests with merge conflicts, failing checks, or required reviews will not be merged
- Branch protection rules still apply
- Draft PRs will be skipped

## Troubleshooting

If the action fails to merge a pull request, check the workflow logs for detailed error messages. The action includes comprehensive error reporting to help diagnose issues.

### Common Issues and Solutions

#### Mergeable Status is Null

GitHub calculates the mergeable status asynchronously, and sometimes it may remain `null` even after multiple attempts to check. The action includes several workarounds:

- It forces GitHub to recalculate the mergeable status by updating the PR
- It waits for up to 10 attempts with 10-second intervals
- If the mergeable status is still `null` but the mergeable state is `clean`, it will attempt to merge anyway

#### Permission Issues

If you see errors related to permissions:

- Ensure your Personal Access Token has the `repo` scope (full control of private repositories)
- For public repositories, you may need only the `public_repo` scope
- If you're using branch protection rules, ensure the token belongs to a user with bypass permissions or that all required checks are passing

#### Branch Protection Rules

If your repository uses branch protection rules:

- Required status checks must be passing
- Required reviews must be approved
- The token must belong to a user with permission to bypass protections, or all requirements must be met

#### Merge Conflicts

If the PR has merge conflicts:

- The action will detect this and report it in the logs
- You'll need to resolve the conflicts manually or update the PR branch

#### Other Issues

- If the PR branch is behind the base branch, the action will report this
- If required status checks are failing, the action will detect and report this
- If the PR is blocked by branch protection rules, this will be indicated in the logs

## License

This project is licensed under the MIT License - see the LICENSE file for details.
