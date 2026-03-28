# Phase 3 - 高级自动化与发布纪律

## 目标

判断哪些高摩擦但高收益的自动化层，值得进入默认 guides；同时建立更明确的版本与发布整理纪律。

## 清单

- [x] 判断 Hooks 是否应成为 starter 默认层
- [x] 判断是否需要专门的 maintenance / release Skill
- [x] 为本仓库建立轻量 release / versioning 纪律

## 退出标准

- 默认 guides 对哪些自动化是推荐、哪些是可选、哪些故意不纳入，已经说清楚
- 仓库已经拥有可重复的版本整理节奏，而不是完全临时发挥

## 备注

- 轻量发布纪律已经落在 `docs/release/README.md`
- 当前策略明确偏向“显式状态同步 + 小报告”，而不是复杂 changelog 或 CI 发布流水线
- 当前默认判断：
  - Hooks 继续保留在 guides 里，但在官方仍属 `Experimental` 时，不进入 starter 默认层
  - 当前不增加专门的 maintenance/release Skill，而是使用 `$deep-audit` + 发布纪律 + 报告模板的组合
