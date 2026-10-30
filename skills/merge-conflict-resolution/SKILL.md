---
name: merge-conflict-resolution
license: MIT
description: 安全解决 Git merge、rebase、cherry-pick 或 revert 冲突，理解双方意图并验证结果。用于分支存在冲突、更新上游时遇到冲突，或操作因冲突停滞的场景。
---

# Merge Conflict Resolution

## 工作流程

1. 记录 `git status`、当前正在执行的 Git 操作，以及 `git diff --name-only --diff-filter=U` 列出的全部未合并路径。阅读仓库规则。
2. 逐个检查冲突文件的工作区内容，以及可用的共同基线、ours 和 theirs 版本：`git show :1:path`、`git show :2:path`、`git show :3:path`。
3. 结合邻近代码、调用方、类型、测试和近期提交，还原双方的修改意图。选择方案应以正确行为为依据，不能仅因为某一侧能够编译就采用它。
4. 整合双方修改，保留正确的组合行为、项目既有风格，以及用户与本次冲突无关的改动。清除所有冲突标记。
5. 仅当代码和历史仍无法确定产品或架构取舍时，向用户询问。
6. 执行 `git diff --check`，针对受影响的行为进行验证；共享契约发生变化时，扩大检查范围。
7. 已获准继续 Git 操作时，仅暂存已解决冲突的文件，并以非交互方式继续对应的 merge、rebase、cherry-pick 或 revert 操作。必需的验证未通过时，停在当前状态，不继续操作。

保留无关改动，如实说明语义上的不确定性。禁止使用破坏性的恢复命令或丢弃无关改动。

完成后报告已解决的文件、关键决策、验证结果、Git 操作的继续状态，以及剩余阻塞。
