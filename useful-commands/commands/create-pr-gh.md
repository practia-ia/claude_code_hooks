---
description: Create a pull request to main branch in GitHub
---

# Create Pull Request (GitHub)

Create a pull request to main branch in GitHub.

## Environment Variables

This command requires the following environment variable:

- `GITHUB_PERSONAL_ACCESS_TOKEN`: GitHub Personal Access Token with `repo` scope

## Instructions

You are tasked with creating a pull request for the current branch to main in GitHub. Follow these steps:

1. **Verify Git Status**

   - Check current branch name
   - Verify there are no uncommitted changes (if there are, commit them first with an appropriate message)
   - Ensure the branch is pushed to origin

2. **Detect Repository**

   - Extract owner and repo name from git remote URL
   - Example: `git@github.com:owner/repo.git` → owner: `owner`, repo: `repo`

3. **Analyze Changes**

   - Run `git log origin/main..HEAD --oneline` to see commits since main
   - Run `git diff --stat origin/main...HEAD` to see file changes
   - Analyze the commits and changes to understand what was worked on

4. **Extract Issues**

   - Look for issue references in commit messages (e.g., #123, fixes #456)
   - Search commit messages for keywords like "fix", "feat", "refactor", "docs", etc.

5. **Generate PR Description**
   Based on the commits and changes, create a detailed PR description following this template:

   ```
   ## Summary
   - [Brief bullet points of main changes]

   ## Changes

   ### [Category] (closes #issue-id if applicable)
   - **File1**: Description of changes
   - **File2**: Description of changes
   - Key functionality added/modified

   ### [Another Category]
   - Details...

   ## Files changed
   - X files modified
   - +Y lines added
   - -Z lines removed

   ## Test plan
   - [ ] Test item 1
   - [ ] Test item 2
   - [ ] Test item 3

   Generated with [Claude Code](https://claude.com/claude-code)
   ```

6. **Create Pull Request**

   Use the GitHub MCP tools to create the PR:
   - Owner: Detected from git remote
   - Repo: Detected from git remote
   - Head branch: Current branch
   - Base branch: `main`
   - Title: Generate a concise title based on the main change (e.g., "feat: Add feature X", "fix: Resolve issue Y")
   - Body: Use the generated description from step 5

7. **Output Result**
   - Show the PR number and URL
   - Provide a summary of what was included

## Important Notes

- If `GITHUB_PERSONAL_ACCESS_TOKEN` is not set, ask the user to configure it
- The token needs `repo` scope for private repositories
- Generate descriptions in the same language as the commit messages
- Be thorough in analyzing changes to create an accurate description
- If there are uncommitted changes, ask user if they want to commit them first
- Use `closes #123` or `fixes #123` syntax to auto-close issues when PR is merged
