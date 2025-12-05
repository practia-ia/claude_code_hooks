---
description: Generate a comprehensive summary of the current Claude Code session
---

# Document Session

Generate a comprehensive summary of the current Claude Code session and save it to `/docs/logs`.

## Instructions

You are tasked with creating a detailed documentation of the current coding session. Follow these steps:

1. **Prepare Directory Structure**
   - Create `/docs/logs` directory if it doesn't exist (relative to project root)
   - Generate a filename with timestamp: `session_YYYY-MM-DD_HH-MM-SS.md`

2. **Analyze Session Context**
   - Review the conversation history to understand what was accomplished
   - Identify key tasks, problems solved, and decisions made
   - Note any commands executed, files modified, or configurations changed
   - Capture important code snippets or terminal outputs

3. **Generate Session Summary**
   Create a markdown document with the following structure:

   ```markdown
   # Session Summary - [Date and Time]

   ## Overview
   - **Date**: YYYY-MM-DD
   - **Duration**: [Estimated session length]
   - **Focus Area**: [Brief description of main topic]

   ## Tasks Completed
   1. [First major task]
   2. [Second major task]
   3. [Additional tasks...]

   ## Problems Solved
   - **Problem 1**: [Description]
     - Solution: [How it was resolved]
   - **Problem 2**: [Description]
     - Solution: [How it was resolved]

   ## Files Modified
   - `path/to/file1` - [What changed]
   - `path/to/file2` - [What changed]

   ## Commands Executed
   ```bash
   # Key commands run during session
   command1
   command2
   ```

   ## Configuration Changes
   - [List any config files modified]
   - [Environment setup changes]

   ## Key Learnings
   - [Important insight 1]
   - [Important insight 2]

   ## Next Steps
   - [ ] [Suggested follow-up task 1]
   - [ ] [Suggested follow-up task 2]

   ## Code Snippets
   ### [Relevant code section 1]
   ```language
   code here
   ```

   ### [Relevant code section 2]
   ```language
   code here
   ```

   ## Notes
   - [Any additional observations or context]

   ---
   🤖 Generated with [Claude Code](https://claude.com/claude-code)
   ```

4. **Save Documentation**
   - Write the generated summary to `/docs/logs/session_[timestamp].md`
   - Use the Write tool to create the file
   - Confirm successful creation with file path

5. **Output Result**
   - Display a brief confirmation message
   - Show the file path where documentation was saved
   - Provide a quick summary of what was documented

## Important Notes
- Be thorough but concise - capture key information without excessive detail
- Include actual code snippets only when relevant to understanding the session
- Use proper markdown formatting for readability
- Timestamp format should be: `YYYY-MM-DD_HH-MM-SS` (24-hour format)
- Focus on actionable information and concrete outcomes
- If the session was brief, adjust the level of detail accordingly
