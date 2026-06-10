---
name: task-env
category: git
description: 在指定的 git 仓库中基于主线分支创建临时任务分支并切换到任务分支，其它仓库切换到主线分支，为当前任务构造一个独立的代码环境。
params:
  - name: project
    description: 指定工程根目录的路径
    example: ./workspace
  - name: repo
    description: 指定仓库路径列表
    example: apps、vendor
  - name: task
    description: 指定任务分支的名字
    example: task-001
---

在 {{project}} 工程中，找到 {{repo}} 这些仓库，基于这些仓库各自跟踪的主线分支创建名为 {{task}} 的任务分支并切换过去。其它仓库确保切换到各自跟踪的主线分支上。如果任务分支已存在则提醒用户是否发生了分支冲突，是否直接切换到任务分支，还是重新取一个分支名字。
