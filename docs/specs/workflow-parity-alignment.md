# 工作流对齐规格

## 问题

`guides-codex` 已经完成了 Codex 自举骨架，但和对标仓库 `guides` 相比，默认开发流程仍然偏轻。当前缺少面向小任务、文档刷新、阶段整理、代码健康诊断和多代理审查的稳定入口，导致“能维护”与“像一个成熟开发流程那样维护”之间还有落差。

这次对齐的目标不是把 Claude 工作流原样搬过来，而是在 **不把 Hooks 设为 starter 默认层** 的前提下，把其他高频流程尽量补齐为 Codex 可执行版本。

## 范围

- 补齐项目级 Skills 入口：
  - `task`
  - `docs`
  - `release`
  - `diagnose`
- 补齐项目级自定义 agents 预设，明确 reviewer / docs researcher 这类多代理协作角色
- 为 starter 模板同步补齐同类骨架
- 补齐 `docs/development/` 目录与最小说明层
- 更新 `README.md`、`00-04`、prompt、release、roadmap、session-notes 等用户可见文档
- 如本次改动达到“可发布整理”级别，补发布记录并同步版本

## 非范围

- 不把 Hooks 改成 starter 默认配置
- 不把 Claude 的 Auto Memory、`/simplify`、`/loop` 之类能力写成 Codex 已有等价物
- 不为了对齐而引入复杂 CI 流水线
- 不要求 starter 一次覆盖所有仓库类型的深度定制

## 受影响文档或模板

- `README.md`
- `00-日常使用说明.md`
- `01-AGENTS配置架构指南.md`
- `02-自动化与命令策略.md`
- `03-Skills命令配置.md`
- `04-工作流最佳实践.md`
- `prompt-新项目初始化.md`
- `AGENTS.md`
- `.codex/config.toml`
- `.codex/agents/`
- `.agents/skills/`
- `.agents/session-notes.md`
- `docs/roadmap/`
- `docs/specs/`
- `docs/development/`
- `docs/release/README.md`
- `docs/reports/`
- `templates/starter-project/`

## 实施方案

1. 先定义“非 Hooks 默认流程对齐”的明确边界，并写入 roadmap/spec。
2. 在项目本仓库补齐 Skills、custom agents 和 `docs/development/` 骨架。
3. 把同样的默认契约同步到 `templates/starter-project/`，避免主仓库与 starter 再次漂移。
4. 更新核心 guides，使其能解释这些入口各自的职责边界，以及与 Hooks 的分层关系。
5. 如果这次改动已经构成明显的首次接入体验提升，则按 release 纪律更新版本与最小发布记录。

## 验证计划

- 结构验证：
  - 新增的 Skills、agents、development 文档在主仓库和 starter 中都存在
- 一致性验证：
  - `README.md`、`00-04`、starter README、prompt 中的入口描述不冲突
- 能力边界验证：
  - 任何关于 Hooks、Subagents、Skills、Rules 的时效性结论，都以官方文档为准
- 维护流程验证：
  - 新的默认路径能清楚回答“小任务怎么做”“文档什么时候刷”“阶段完成怎么整理”“多代理怎么配”

## 开放问题

- `diagnose` 是否应该在文档仓库保留较轻版本，而把更重的代码诊断留给真实项目模板
- `release` 在本仓库变成默认 Skill 后，未来是否仍需要保留“非高频动作”的限制说明

## 当前状态

已完成本轮默认流程对齐与 CLI 主路径收口审计。当前剩余事项不再属于本 spec 的实施缺口，而是后续 Phase 4 的持续观察：

- Hooks 默认策略是否需要随官方成熟度变化而调整
- 默认入口在更多真实项目中使用后，是否还需要继续收紧契约
- CLI 之外的 app / automations / GitHub Action 是否值得进入默认推荐层

## 推荐的第一实施切片

先补齐 `task`、`docs`、`release`、`diagnose` 四个 Skill，加上 `.codex/agents/` 下的最小 reviewer / docs_researcher 预设，再回写主文档和 starter。
