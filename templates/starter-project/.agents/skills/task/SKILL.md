---
name: task
description: |
  处理一个边界清楚、能在当前会话内闭环的小任务。
  适用于小需求、小 Bug、微调或局部文档修正。
argument-hint: "[本次要做什么]"
allowed-tools: Read Write Edit Bash Glob Grep
---

完成一个小任务，并保持最小闭环。

工作流：

1. 先判断这是不是小任务；如果明显会跨多个决策点或多个提交，建议改用 `$spec`。
2. 探索相关文件，实施改动，运行项目真实验证。
3. 说明是否需要同步 roadmap、spec 或 session-notes。
4. 输出：
   - 任务摘要
   - 已做改动
   - 验证状态
   - 状态同步
   - 剩余风险
   - 下一步
