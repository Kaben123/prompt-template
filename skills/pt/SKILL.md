---
name: pt
description: |-
  Prompt template manager — store, list, and invoke reusable parameterized prompt templates.
  Use this skill whenever the user types /pt followed by a subcommand (add, list, use, show, edit, delete).
  Triggers: /pt add, /pt list, /pt use, /pt show, /pt edit, /pt delete.
  Also trigger when user mentions "prompt template", "提示词模板", or wants to reuse a saved prompt.
---

# pt — Prompt Template Manager

Manage reusable prompt templates with `{{param}}` placeholders. Templates are stored as Markdown files in `~/.claude/prompt-templates/`.

## Template File Format

Each template is a `.md` file with YAML frontmatter:

```yaml
---
name: <template-name>
category: <category-tag>
description: <one-line description>
params:
  - name: <param_name>
    description: <what this param is>
    example: <example value>
---

<template body with {{param_name}} placeholders>
```

## Commands

Parse the user's input after `/pt` to determine which command to run.

---

### `add` — Add a new template

**One-shot mode:** If user provides `--name=<n>`, `--category=<c>`, `--body="<content>"`, use those values directly. Auto-detect `{{...}}` placeholders from body as params.

**Interactive mode (default):** Ask the following one at a time:
1. Template name (kebab-case)
2. Category tag
3. One-line description
4. Template body (tell user to use `{{param_name}}` for placeholders)
5. After receiving body, scan for `{{...}}` patterns. For each detected param, ask for description and example (user can skip).

**Before saving:**
- Check if `~/.claude/prompt-templates/<name>.md` already exists. If yes, ask whether to overwrite.
- If `~/.claude/prompt-templates/` directory doesn't exist, create it.

**Save:** Write the template file to `~/.claude/prompt-templates/<name>.md` with proper frontmatter and body.

**Confirm:** Output `✓ 模板已保存到 ~/.claude/prompt-templates/<name>.md`

---

### `list [category]` — List templates

1. Glob `~/.claude/prompt-templates/*.md`
2. Read frontmatter (name, category, description) from each file
3. Group by category

**Without argument:** Display all templates grouped by category:
```
[category]
  name           — description
```

**With category argument:** Display only that category, include param names:
```
[category]
  name           — description (params: p1, p2)
```

If no templates exist, output: `还没有模板。使用 /pt add 创建第一个。`

---

### `use [name] [--param=value ...]` — Render and execute a template

**Without name argument:** List all available templates with parameterized usage examples:
1. Glob `~/.claude/prompt-templates/*.md`
2. Read frontmatter (name, params) from each file
3. Display each template with its usage syntax:
```
可用模板：

  repo-reset     — 重置 repo 到干净状态
                   用法: /pt use repo-reset --project=<工程路径> --branch=<分支名>

  task-env       — 创建任务分支环境
                   用法: /pt use task-env --project=<工程根目录> --repo=<仓库列表> --task=<分支名>
```
4. If no templates exist, output: `还没有模板。使用 /pt add 创建第一个。`

**With name argument:**
1. Read `~/.claude/prompt-templates/<name>.md`. If not found, output error and suggest `/pt use` (without name) to see available templates.
2. Parse frontmatter for param definitions.
3. Collect param values:
   - From `--param=value` arguments in the command
   - For any missing params, ask interactively one by one, showing description and example as hints
   - If user enters empty value (presses enter with no input), leave `{{param}}` unrendered
4. Replace all `{{param_name}}` in body with provided values.
5. Execute the rendered text directly as the next instruction — act on it immediately as if the user typed it.

---

### `show <name>` — Display template without executing

1. Read `~/.claude/prompt-templates/<name>.md`. If not found, output error.
2. Display formatted:
```
名称: <name> | 分类: <category>
描述: <description>
参数: <p1> (<desc>), <p2> (<desc>)
---
<body content>
```

---

### `edit <name>` — Modify existing template

1. Read `~/.claude/prompt-templates/<name>.md`. If not found, output error.
2. Display current full content to user.
3. Ask: "你想怎么修改？"
4. Based on user's instructions, update the template file accordingly.
5. If new `{{params}}` are introduced, offer to add descriptions.
6. Save updated file.
7. Confirm: `✓ 模板已更新`

---

### `delete <name>` — Delete a template

1. Check if `~/.claude/prompt-templates/<name>.md` exists. If not, output error.
2. Read its category and description for confirmation display.
3. Ask: `确认删除模板 "<name>" (category: <cat>)？`
4. On confirmation, delete the file.
5. Confirm: `✓ 已删除`

---

## Error Handling

- Unknown command after `/pt`: show available commands (add, list, use, show, edit, delete)
- Template not found: `模板 "<name>" 不存在。使用 /pt list 查看可用模板。`
- Frontmatter parse error: `模板文件格式异常，请用 /pt show <name> 查看或 /pt edit <name> 修复。`
