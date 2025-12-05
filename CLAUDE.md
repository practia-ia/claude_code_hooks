# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Code plugin marketplace containing the `conversation-logger` plugin. This plugin automatically logs conversations to local files with automatic rotation. Windows-only, uses PowerShell 5.1+.

## Installation

```bash
# Register the marketplace (auto-installs the plugin)
/plugin marketplace add practia-ia/claude_code_hooks

# Enable the plugin
/plugin enable conversation-logger@practia-ia
```

Requires Claude Code version 1.0.40+ (plugin support).

## Repository Structure

```
.claude-plugin/marketplace.json    # Marketplace definition (owner, plugin list)
conversation-logger/
├── .claude-plugin/plugin.json     # Plugin metadata (name, version, author)
├── hooks/
│   ├── hooks.json                 # Hook event definitions and commands
│   └── log-conversation.ps1       # Main PowerShell logging script
└── README.md                      # Plugin documentation (Spanish)
```

## Architecture

### Hook Event System

The plugin registers hooks for three Claude Code events in `hooks.json`:

| Event | Trigger | Timeout |
|-------|---------|---------|
| `UserPromptSubmit` | User sends a message | 10s |
| `PostToolUse` | Any tool completes (wildcard matcher) | 10s |
| `Stop` | Assistant response completes | 15s |

All hooks execute the same PowerShell script with different JSON input via stdin.

### Hook Input JSON

Claude Code passes a JSON object to the hook with these fields:
- `session_id`: Unique session identifier
- `hook_event_name`: Event type (`UserPromptSubmit`, `PostToolUse`, `Stop`)
- `prompt`: User message (UserPromptSubmit only)
- `tool_name`, `tool_input`: Tool details (PostToolUse only)
- `transcript_path`: Path to NDJSON transcript file (Stop only)
- `cwd`: Current working directory

### Logging Behavior

**Log Location**: `.claude/logs/conversation-YYYYMMDD.log` (project-relative)

**Rotation**: When log exceeds 512 KB, renamed to `conversation-YYYYMMDD-HHmmss.log`

**Tool Input Summarization** (to prevent log bloat):
- Read/Write/Edit: Logs file path only
- Bash: First 200 chars of command
- Grep/Glob: Search pattern only
- Task: Task description only
- Other tools: JSON input (max 300 chars)

**Response Logging**: Parses NDJSON transcript from end, collects assistant text blocks, truncates at 2000 chars.

## Key Files

- **`hooks.json`**: Defines which events trigger the hook and command to execute. Uses `${CLAUDE_PLUGIN_ROOT}` variable (expanded by Claude Code).

- **`log-conversation.ps1`**: Reads JSON from stdin, extracts fields, writes formatted log entries. Handles all three event types in a switch statement.

- **`marketplace.json`**: Required for `/plugin marketplace add`. Defines plugin source path and metadata.
