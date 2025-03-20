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
2. No configuration changes are needed as it uses the built-in `GITHUB_TOKEN`

### 2. Workflow Permissions

This workflow uses the built-in `GITHUB_TOKEN` with the following permissions:

```yaml
permissions:
  contents: write     # Needed to merge PRs
  pull-requests: write  # Needed to comment on PRs
```

These permissions are already configured in the workflow file. No additional setup is required.

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

2. **Commit and push to the target repository**:

   ```bash
   cd /path/to/other-project
   git add .github/workflows/project-auto-merge.yml
   git commit -m "Add auto-merge workflow"
   git push
   ```

3. **No additional configuration needed** - the workflow uses the built-in `GITHUB_TOKEN` so it works immediately

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
   - Make sure the workflow has the correct permissions (contents: write, pull-requests: write)
   - For repositories with stricter settings, you may need to customize permissions

3. **Required Checks Not Passing**
   - The auto-merge will fail if required status checks haven't completed yet
   - You can manually re-run the workflow after checks have passed

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

This workflow is provided under the MIT License. Feel free to modify and use it in your projects.
