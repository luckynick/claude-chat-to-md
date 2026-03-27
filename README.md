# claude-chat-to-md

Convert [Claude Code](https://docs.anthropic.com/en/docs/claude-code) chat sessions to clean, readable Markdown — including subagent conversations.

Claude Code persists full chat history as JSONL files under `~/.claude/projects/`. This tool reads those files and produces well-formatted Markdown with proper headings, code blocks, and collapsible sections for tool results and subagent conversations.

## Install

```bash
pipx install claude-chat-to-md
```

Or with pip:

```bash
pip install claude-chat-to-md
```

Or run directly from source:

```bash
git clone https://github.com/luckynick/claude-chat-to-md.git
cd claude-chat-to-md
pip install -e .
```

## Usage

### List sessions

```bash
claude-chat-to-md --list
```

```
#    Date                 ID           Title                                              Project
------------------------------------------------------------------------------------------------------------------------
1    2025-10-15 14:30     a1b2c3d4..   Refactor auth middleware                            dev/myapp
2    2025-10-14 09:15     e5f6a7b8..   Add user settings page                              dev/myapp
3    2025-10-13 16:45     c9d0e1f2..   Debug CI pipeline                                   dev/infra
```

### Convert a session

```bash
# By index (from --list)
claude-chat-to-md 1 -o chat.md

# By UUID prefix
claude-chat-to-md a1b2c3 -o chat.md

# By title substring
claude-chat-to-md "auth middleware" -o chat.md

# Most recent session
claude-chat-to-md --latest -o chat.md
```

### Filter by project

```bash
claude-chat-to-md --list --project myapp
claude-chat-to-md --latest --project myapp -o chat.md
```

### Export all sessions

```bash
claude-chat-to-md --all --output-dir ./exports/
```

### Options

| Flag | Description |
|---|---|
| `--list`, `-l` | List all available sessions |
| `--latest` | Convert the most recent session |
| `--all` | Convert all sessions |
| `--project`, `-p` | Filter sessions by project path substring |
| `--output`, `-o` | Output file (default: stdout) |
| `--output-dir`, `-d` | Output directory for `--all` mode |
| `--no-subagents` | Exclude subagent conversations |
| `--no-tool-results` | Exclude tool call results |

## Output format

- **User messages** → `## User` sections
- **Assistant messages** → `## Assistant` sections with text and tool calls
- **Tool results** → collapsible `<details>` blocks
- **Subagent conversations** → collapsible `<details>` blocks with full prompt/response
- **Code** → fenced code blocks with language hints
- **Diffs** → displayed as unified diff format
- System tags (`<ide_opened_file>`, `<system-reminder>`) are stripped

## How it works

Claude Code stores sessions at:

```
~/.claude/projects/<encoded-project-path>/<session-uuid>.jsonl
```

Each line is a JSON object: `user` messages, `assistant` messages (with tool_use blocks), `tool_result` responses, and metadata. Subagent conversations live in a `subagents/` subdirectory alongside the main session.

## Requirements

Python 3.10+ — no external dependencies.
