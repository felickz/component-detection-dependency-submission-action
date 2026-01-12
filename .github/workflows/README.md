# Workflows Documentation

## Dependabot Autofix

The `dependabot-autofix.yml` workflow automatically assigns failed Dependabot PRs to GitHub Copilot for automated fixing.

### How It Works

1. **Triggers**: The workflow runs when the "Test" or "Check dist/" workflows complete
2. **Validation**: It checks if the workflow run is associated with a Dependabot PR
3. **Failure Detection**: If the workflow failed, it retrieves detailed information about failed checks
4. **Assignment**: It assigns the PR to GitHub Copilot with detailed failure information using `gh issue edit`

### Features

- ✅ Automatically detects Dependabot PRs
- ✅ Waits for CI checks to complete via workflow_run event
- ✅ Extracts detailed failure information from failed checks
- ✅ Updates PR description with failure details
- ✅ Adds a comment with instructions for Copilot
- ✅ Labels PR with "copilot-autofix" for easy tracking

### Requirements

- The workflow requires the following permissions:
  - `pull-requests: write` - To update PR descriptions and add comments
  - `issues: write` - To use `gh issue edit` for assignment
  - `checks: read` - To read check run results
  - `contents: read` - To checkout the repository
  - `actions: read` - To read workflow run information

### GitHub Copilot Assignment

The workflow uses `gh issue edit` to assign the PR to Copilot, following the pattern documented in:
https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-a-pr#assigning-an-issue-to-copilot-via-the-github-api

### Workflow Structure

```yaml
on:
  workflow_run:
    workflows: ["Test", "Check dist/"]
    types: [completed]
```

This ensures the autofix workflow only runs after the specified CI workflows complete, avoiding race conditions.

### Customization

To monitor additional workflows, update the `workflows` list in the trigger:

```yaml
on:
  workflow_run:
    workflows: ["Test", "Check dist/", "Your Custom Workflow"]
    types: [completed]
```

### Best Practices

This workflow follows GitHub's best practices for automating Dependabot:
https://docs.github.com/en/code-security/dependabot/working-with-dependabot/automating-dependabot-with-github-actions

Key practices implemented:
- Uses `workflow_run` event to avoid permission issues
- Filters to only Dependabot PRs
- Provides detailed context to the automation (Copilot)
- Uses labels for tracking automated fixes
