---
description: Update an existing pull request in Azure DevOps
---

# Update Pull Request

Update an existing pull request in Azure DevOps.

## Environment Variables

This command requires the following environment variables:

- `AZURE_DEVOPS_ORG`: Azure DevOps organization name
- `AZURE_DEVOPS_PROJECT`: Project name in Azure DevOps
- `AZURE_DEVOPS_REPO`: Repository name (optional, defaults to current git remote)

## Instructions

You are tasked with updating the existing pull request for the current branch in Azure DevOps. Follow these steps:

1. **Verify Git Status**

   - Check current branch name
   - Verify there are no uncommitted changes (if there are, commit them first with an appropriate message)
   - Ensure the branch is pushed to origin

2. **Find Existing PR**

   - Use Azure DevOps MCP tools to find the existing PR for the current branch
   - If no PR exists, inform the user and suggest using `/create-pr` instead

3. **Analyze Changes**

   - Run `git log origin/main..HEAD --oneline` to see commits since main
   - Run `git diff --stat origin/main...HEAD` to see file changes
   - Analyze the commits and changes to understand what was worked on

4. **Extract Work Items**

   - Look for work item references in commit messages (e.g., #123, AB#456)
   - Search commit messages for keywords like "fix", "feat", "refactor", "docs", etc.

5. **Get Current PR Description**

   - Fetch the current PR description using Azure DevOps MCP tools
   - Identify any images or content that should be preserved

6. **Update PR Description**
   Based on the commits and changes, update the PR description following this template:

   ```
   ## Summary
   - [Brief bullet points of main changes]

   ## Cambios principales

   ### [Category] (#work-item-id if applicable)
   - **File1**: Description of changes
   - **File2**: Description of changes
   - Key functionality added/modified

   ### [Another Category]
   - Details...

   ## Archivos modificados
   - X archivos modificados
   - +Y líneas agregadas
   - -Z líneas eliminadas

   ## Test plan
   - [ ] Test item 1
   - [ ] Test item 2
   - [ ] Test item 3

   Generated with [Claude Code](https://claude.com/claude-code)
   ```

7. **Update Pull Request**

   Use the Azure DevOps MCP tools to update the PR:
   - Organization: Use `${AZURE_DEVOPS_ORG}` environment variable
   - Project: Use `${AZURE_DEVOPS_PROJECT}` environment variable
   - Repository: Use `${AZURE_DEVOPS_REPO}` environment variable (or detect from git remote)
   - PR ID: The ID of the existing PR found in step 2
   - Title: Update if the main focus has changed
   - Description: Use the generated description from step 6
   - Work Items: Link any new work item IDs found in commits

8. **Output Result**
   - Show the PR number and URL
   - Provide a summary of what was updated

## Important Notes

- If environment variables are not set, ask the user to configure them
- Always link work items if references are found in commits
- Generate descriptions in the same language as the commit messages
- Be thorough in analyzing changes to create an accurate description
- If there are uncommitted changes, ask user if they want to commit them first
- If the PR description already has images, retain them in the updated description
- Preserve any manually added content from the original PR description
