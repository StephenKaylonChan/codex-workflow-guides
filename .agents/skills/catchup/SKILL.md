---
name: catchup
description: |
  在清空上下文或开启新会话后，快速重建本仓库的工作上下文。
  适用于继续维护、恢复文档切片，或 `/clear` 后回到当前任务。
allowed-tools: Read Bash Glob
---

重建足够继续工作的上下文。

工作流：

1. 读取 `AGENTS.md`。
2. 读取 `.agents/session-notes.md`。
3. 读取 `docs/roadmap/README.md`。
4. 如果 session-notes 或 roadmap 指向活跃 spec，就继续读取该 spec。
5. 检查当前改动状态与最近变化。
6. 总结：
   - 当前仓库状态
   - 当前 roadmap 阶段
   - 当前活跃 spec 或维护切片
   - 已改动文件
   - 最可能的下一步

输出格式：

- 当前状态
- 当前阶段
- 当前活跃切片或 spec
- 已改动文件
- 下一步

规则：

- 总结保持简短，面向执行。
- 聚焦当前切片，不要把整个仓库从头复述一遍。
- 最后必须落成一个明确可执行的下一步。
