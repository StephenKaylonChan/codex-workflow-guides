---
name: diagnose
description: |
  对当前项目的结构、默认流程、文档契约和长期维护风险做系统性诊断。
  适用于准备大改前、阶段结束后或想评估项目健康度时。
argument-hint: "[--workflow | --docs | --architecture | --full]"
allowed-tools: Read Write Edit Bash Glob Grep
---

做一次结构化诊断，不把它混成普通审计。

工作流：

1. 读取 `AGENTS.md`、roadmap、活跃 spec、架构和开发文档。
2. 识别诊断范围。
3. 按结构、验证、文档、状态同步、长期维护风险几个维度给出判断。
4. 如有高置信度小漂移，可顺手修复；否则只报告。
5. 输出：
   - 诊断范围
   - 核心判断
   - 最高优先级问题
   - 已修复项
   - 剩余风险
   - 建议下一步
