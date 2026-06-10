---
name: repo-reset
category: git
description: 重置 repo 到干净状态，丢弃本地修改并同步远程分支
params:
  - name: project
    description: 指定工程路径，如果没有指定则当前目录就是工程的根目录。
    example: workspace_rtos
  - name: branch
    description: 指定 git 分支的名称
    example: local-dev
---

查看 {{project}} 目录下，有哪些仓库正在 {{branch}} 分支上，hardreset 这些 {{branch}} 分支到它们各自跟踪的主线分支。
