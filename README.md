# prompt-template

一个 Claude Code 插件，用于管理可复用的参数化提示词模板。

类似 shell 的 `history` 命令，但更智能——模板支持 `{{param}}` 参数占位符，可分类管理，跨会话复用。避免重复输入冗长的提示词，也避免因输入不规范导致处理质量下降。

## 安装

```bash
# 1. 添加 marketplace
claude plugin marketplace add prompt-template-marketplace --source github --repo Kaben123/prompt-template

# 2. 安装插件
claude plugin install prompt-template@prompt-template-marketplace
```

## 命令一览

| 命令 | 说明 |
|------|------|
| `/pt add` | 添加新的提示词模板（支持交互式或一次性传入） |
| `/pt list [category]` | 按分类列出所有模板 |
| `/pt use <name> [--param=value]` | 渲染并执行指定模板 |
| `/pt show <name>` | 查看模板内容（不执行） |
| `/pt edit <name>` | 修改已有模板 |
| `/pt delete <name>` | 删除模板 |

## 模板格式

模板以 Markdown 文件形式存储在 `~/.claude/prompt-templates/` 目录下：

```markdown
---
name: fix-bug
category: debug
description: 分析并修复指定 JIRA Bug
params:
  - name: jira_id
    description: JIRA 工单号
    example: VELAPLATFO-12345
  - name: file_path
    description: 问题所在文件路径
    example: drivers/sensor/sensor_hub.c
---

分析 JIRA {{jira_id}} 描述的 Bug，重点关注 {{file_path}} 文件。

请按以下步骤处理：
1. 阅读 JIRA 描述，理解问题现象
2. 阅读相关源码，定位根因
3. 提出修复方案并实现
4. 验证修复是否引入回归
```

## 使用示例

### 交互式添加模板

```
/pt add
```

Claude 会逐步询问模板名称、分类、描述、内容和参数说明。

### 一次性添加模板

```
/pt add --name=code-review --category=review --body="Review Gerrit patch {{gerrit_url}}，重点关注 {{aspect}}"
```

### 使用模板（带参数）

```
/pt use fix-bug --jira_id=VELAPLATFO-67890 --file_path=drivers/i2c.c
```

### 使用模板（交互式填参数）

```
/pt use fix-bug
```

Claude 会逐个询问缺失的参数，并显示描述和示例作为提示。

## 设计理念

- **零依赖** — 纯 Skill（Markdown 指令），无需安装脚本或二进制文件
- **个人全局** — 模板跨所有项目可用
- **混合输入** — 支持一次性命令行传参和交互式逐步询问
- **直接执行** — 渲染后的模板直接作为 Claude 指令执行

## 许可证

MIT
