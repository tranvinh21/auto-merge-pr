# Project Auto-Merge Workflow

This document explains how to integrate and use the auto-merge workflow in your project.

## Overview

The Project Auto-Merge workflow automatically merges pull requests when they are created. It's designed to:

- Run immediately when a new PR is opened
- Check if the PR is mergeable
- Automatically merge it if possible
- Add comments explaining the merge status
- Provide detailed logs and error messages

## Integration Steps

### 1. Add the Workflow File

The workflow file is set up at `.github/workflows/project-auto-merge.yml`. To use it in another project:

1. Copy the `project-auto-merge.yml` file to your target project's `.github/workflows/` directory
2. **Add the PERSONAL_ACCESS_TOKEN secret** to your repository (see step 2)

### 2. Add Your Personal Access Token

This workflow uses your personal access token to interact with GitHub's API:

1. Create a Personal Access Token (if you don't have one already):
   - Go to your GitHub account settings: https://github.com/settings/tokens
   - Click "Generate new token" (classic)
   - Give your token a descriptive name (e.g., "Auto-Merge PR Token")
   - Select the following scopes:
     - `repo` (Full control of private repositories)
     - `workflow` (if you want to trigger other workflows)
   - Click "Generate token"
   - Copy the token immediately, as you won't be able to see it again

2. Add the token as a repository secret:
   - Go to your repository settings
   - Click on "Secrets and variables" → "Actions"
   - Click "New repository secret"
   - Name: `PERSONAL_ACCESS_TOKEN`
   - Value: Paste your personal access token
   - Click "Add secret"

### 3. Usage

The workflow will automatically run when:
- A new pull request is opened

You can also manually trigger it:
1. Go to the Actions tab in your repository
2. Select "Project Auto-Merge PRs" workflow
3. Click "Run workflow"
4. Enter the PR number and select a merge method
5. Click "Run workflow"

## Reusing in Other Projects

To reuse this auto-merge workflow in other projects:

1. **Copy the workflow file**:
   ```bash
   # From your current repository
   cp .github/workflows/project-auto-merge.yml /path/to/other-project/.github/workflows/
   ```

2. **Add the PERSONAL_ACCESS_TOKEN secret to the new repository**:
   - Follow the steps in section 2 above to add your token as a secret
   - If you use the same token across repositories, you only need to create it once
   - Each repository needs its own secret configuration

3. **Commit and push to the target repository**:
   ```bash
   cd /path/to/other-project
   git add .github/workflows/project-auto-merge.yml
   git commit -m "Add auto-merge workflow"
   git push
   ```

## Benefits of Using a Personal Access Token

Using your personal access token offers these advantages:

1. **Identity**: Actions are performed as your GitHub identity, not as the "GitHub Actions bot"
2. **Cross-repository actions**: Ability to interact with other repositories (with proper permissions)
3. **Workflow chaining**: Can trigger other workflows when performing actions
4. **Potential bypass of branch protections**: Depending on repository settings, your token may have permission to bypass certain branch protections

## Advanced Configuration

### Customizing the Merge Method

By default, the workflow uses the "merge" method. You can customize this by:

1. Manually selecting a different method (merge, squash, rebase) when triggering the workflow
2. Editing the workflow file to change the default value:
   ```yaml
   merge_method:
     default: "merge"  # Change to "squash" or "rebase"
   ```

### Branch Protection Rules

If your repository has branch protection rules, the auto-merge will only work if:
- All required status checks have passed
- Required reviews have been approved
- Other protection rules are satisfied

For PRs that are newly created, these conditions might not be met immediately. Consider:
- Using this workflow for non-protected branches
- Customizing branch protection to allow specific users/tokens to bypass restrictions

## Troubleshooting

### Common Issues

1. **PR Not Mergeable**
   - The workflow will comment on the PR with the specific reason
   - Check if there are merge conflicts or failing checks

2. **Permission Issues**
   - Make sure your PERSONAL_ACCESS_TOKEN has sufficient permissions (repo scope)
   - For private repositories, you need the full 'repo' scope
   - For public repositories, the 'public_repo' scope may be sufficient

3. **Required Checks Not Passing**
   - The auto-merge will fail if required status checks haven't completed yet
   - You can manually re-run the workflow after checks have passed

4. **Token Expiration**
   - If your token has expired, you'll need to generate a new one and update the repository secret

### Workflow Outputs

The workflow provides the following outputs that can be used in subsequent steps:
- `merge_success`: 'true' if the merge was successful, 'false' otherwise
- `merge_sha`: The SHA of the merge commit if successful
- `merge_error`: Error message if the merge failed

## Extending the Workflow

### Adding Notifications

You can extend the workflow to add notifications by:

1. Modifying the "Send notification on successful merge" step
2. Adding integrations with Slack, MS Teams, or email

Example for Slack:
```yaml
- name: Send Slack notification
  if: steps.merge.outputs.merge_success == 'true'
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    fields: repo,message,commit,author,action,eventName
    text: 'PR #${{ github.event.pull_request.number || github.event.inputs.pr_number }} was automatically merged'
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Additional Checks

You can add pre-merge checks by inserting steps before the auto-merge step:

```yaml
- name: Custom validation
  run: |
    # Add your custom validation logic here
    # Exit with code 1 to prevent merge
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.
