# prompt-template

A Claude Code plugin for managing reusable parameterized prompt templates.

Like shell `history` but smarter — templates are parameterized with `{{param}}` placeholders, categorized, and reusable across sessions.

## Installation

```bash
claude plugin add <repo-url>
```

## Usage

| Command | Description |
|---------|-------------|
| `/pt add` | Add a new prompt template (interactive or one-shot) |
| `/pt list [category]` | List templates grouped by category |
| `/pt use <name> [--param=value]` | Render and execute a template |
| `/pt show <name>` | Display template content without executing |
| `/pt edit <name>` | Modify an existing template |
| `/pt delete <name>` | Delete a template |

## Template Format

Templates are stored as Markdown files in `~/.claude/prompt-templates/`:

```markdown
---
name: fix-bug
category: debug
description: Analyze and fix a JIRA bug
params:
  - name: jira_id
    description: JIRA ticket ID
    example: PROJ-12345
  - name: file_path
    description: Target file path
    example: src/main.c
---

Analyze JIRA {{jira_id}} bug, focus on {{file_path}}.

Steps:
1. Read JIRA description
2. Read source code, locate root cause
3. Propose and implement fix
4. Verify no regressions
```

## Examples

### Add a template interactively

```
/pt add
```

Claude will ask for name, category, description, body, and param details one by one.

### Add a template in one shot

```
/pt add --name=code-review --category=review --body="Review the Gerrit patch at {{gerrit_url}}, focus on {{aspect}}"
```

### Use a template

```
/pt use fix-bug --jira_id=PROJ-67890 --file_path=drivers/i2c.c
```

Or just `/pt use fix-bug` and Claude will ask for each parameter interactively.

## Design

- **Zero dependencies** — pure skill (Markdown instructions), no scripts or binaries
- **Global personal scope** — templates available across all projects
- **Hybrid input** — supports both one-shot CLI args and interactive prompting
- **Direct execution** — rendered template is executed immediately as Claude instruction

## License

MIT
