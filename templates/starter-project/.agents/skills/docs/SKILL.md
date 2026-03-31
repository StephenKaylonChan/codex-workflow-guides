---
name: docs
description: |
  刷新当前项目的开发文档、架构文档或 starter 使用说明。
  适用于功能入口变化、结构变化、发布前文档梳理或明显文档漂移修正。
argument-hint: "[范围，如 README | architecture | development | full]"
allowed-tools: Read Write Edit Bash Glob Grep
---

按最小必要范围刷新文档。

工作流：

1. 确定需要刷新的文档范围。
2. 识别本次变化的真实源头，不做平行重写。
3. 更新相关文档并跑与范围匹配的验证。
4. 输出：
   - 刷新范围
   - 已更新文档
   - 未更新但相关的文档
   - 验证状态
   - 剩余漂移
   - 下一步
