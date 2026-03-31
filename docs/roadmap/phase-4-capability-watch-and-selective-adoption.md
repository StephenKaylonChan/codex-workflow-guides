# Phase 4 - 能力观察与选择性采用

## 目标

持续跟踪 Codex 官方能力变化，只在它们足够稳定且确实改善默认实践时，才纳入 guides。

## 清单

- [x] 补齐非 Hooks 默认开发流程，对齐 `guides` 中已被验证的高频入口
- [ ] 当官方成熟度变化时，重新判断 Hooks 默认策略
- [x] 重新评估并决定 release / maintenance Skill 是否进入默认层
- [x] 判断 starter 是否需要按仓库类型拆分

## 退出标准

- 新能力的采用有明确记录和理由，不再靠临时改文档
- starter 和 guides 继续保持“默认保守，但能响应真实平台进步”

## 备注

- 当前判断：starter 先保持一个统一骨架，通过“仓库类型适配矩阵”覆盖差异，不急着拆多模板
- 已增加 `docs/reports/capability-watch-log.md`，后续复查不需要再从零开始
- `docs/specs/workflow-parity-alignment.md` 已完成本轮对齐实现与收口审计
- 当前新增结论：在不把 Hooks 设为默认层的前提下，补齐 `task / docs / release / diagnose` 与 custom agents 作为默认工作流入口
