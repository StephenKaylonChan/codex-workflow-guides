---
name: release
description: |
  在一个阶段性切片完成后，整理版本信息、发布记录和相关状态同步。
  适用于 starter 契约变化、主入口变化、重要工作流升级或 roadmap Phase 完成。
argument-hint: "[版本或发布主题]"
allowed-tools: Read Write Edit Bash Glob Grep
---

按本仓库的轻量发布纪律做一次完整整理。

工作流：

1. 读取 `docs/release/README.md`、roadmap、活跃 spec、session-notes。
2. 判断这次变化属于 `major / minor / patch` 哪一类。
3. 同步以下内容中与发布相关的部分：
   - `README.md`
   - `00-04` 核心文档版本字段
   - 需要更新的 roadmap / spec / session-notes
   - `docs/reports/` 下的最小发布记录
   - `docs/development/changelog.md`
4. 跑与改动范围匹配的一致性检查。
5. 输出：
   - 发布级别
   - 已同步文件
   - 已跑检查
   - 未跑检查
   - 仍存在的风险
   - 建议下一步

输出格式：

- 发布级别
- 已同步文件
- 已跑检查
- 未跑检查
- 仍存在的风险
- 建议下一步

规则：

- 如果状态同步不完整，不要先改版本号。
- 没有实际运行的一致性检查，不能写成通过。
- 这仍然是轻量发布纪律，不引入额外流水线。
