# Phase 3 Hooks 决策记录

## 检查范围

- OpenAI Codex Hooks 官方文档
- OpenAI Codex Feature Maturity 官方文档
- `02-自动化与命令策略.md`
- `templates/starter-project/README.md`
- `prompt-新项目初始化.md`

## 来源说明

- Hooks 文档：https://developers.openai.com/codex/hooks
- 成熟度文档：https://developers.openai.com/codex/feature-maturity

本次主要依据：

- Hooks 官方文档说明其属于 `Experimental`
- 文档明确指出 Hooks 仍在 active development，并需要通过 `codex_hooks = true` 显式启用
- Feature Maturity 将 `Experimental` 定义为可能变化、可能移除、稳定性不足

## 决策

Hooks 继续保留在 guides 中作为官方支持能力说明，但**不进入** `templates/starter-project/` 的默认层。

## 原因

- Hooks 已经是官方文档能力，guides 不能忽略。
- 但 starter 的职责是提供尽量稳定、跨项目都能复用的最低配底座。
- `AGENTS.md`、Rules、Skills、roadmap、spec 已经足够构成最小可用协作系统，不需要把每个项目都拉进实验性能力维护。
- 真正需要生命周期自动化的项目，仍然可以后续按需启用 Hooks。

## 已落修改

- 在 starter README 中明确写出：Hooks 是可选增强项，不默认附带。
- 在初始化 prompt 中明确：不要把 Hooks 自动塞进每个项目。
- 在 `02-自动化与命令策略.md` 中补充 starter 默认策略说明。

## 剩余风险

- 某些高级项目仍然可能从第一天就需要 Hooks，因此初始化流程必须继续允许显式 opt-in。
- 如果 Hooks 后续进入更稳定阶段，这个判断应重新复核。

## 建议下一步

继续完成 Phase 3，判断本仓库是否需要专门的 maintenance 或 release Skill。
