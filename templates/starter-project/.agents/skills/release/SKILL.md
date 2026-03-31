---
name: release
description: |
  在一个阶段性里程碑后整理版本信息、发布记录和文档状态。
  适用于主入口变化、starter 契约变化、重要工作流升级或 roadmap Phase 完成。
argument-hint: "[版本或发布主题]"
allowed-tools: Read Write Edit Bash Glob Grep
---

按项目自己的发布纪律做一次轻量整理。

工作流：

1. 读取 release 规则、roadmap、活跃 spec 和 session-notes。
2. 判断这次属于 `major / minor / patch` 哪一类。
3. 同步版本字段、发布记录、changelog 与状态文档。
4. 跑与范围匹配的一致性检查。
5. 输出：
   - 发布级别
   - 已同步文件
   - 已跑检查
   - 未跑检查
   - 仍存在的风险
   - 建议下一步
