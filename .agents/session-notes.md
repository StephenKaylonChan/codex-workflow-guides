# 会话交接记录

最后更新：2026-03-28
项目：guides-codex

## 已完成

- 仓库已经落下项目级 Codex 协作骨架：
  - `AGENTS.md`
  - `.codex/config.toml`
  - `.codex/rules/default.rules`
  - `.agents/skills/`
  - `docs/roadmap/`、`docs/specs/`、`docs/architecture/`、`docs/reports/`
- `00-日常使用说明.md` 与 `04-工作流最佳实践.md` 已完成去重，不再争抢同一职责。
- starter 模板和主文档已成为可复用的默认入口。
- 第一轮真实自举维护切片已经完成：
  - 优化了 `templates/starter-project/README.md`
  - 收紧了面向非应用仓库的 `prompt-新项目初始化.md`
  - 推进并完成了 Phase 1
- Phase 2 已完成：
  - 统一了基础六个 Skills 的输出结构
  - 同步把相同输出协议回灌到 `templates/starter-project/`
  - 简化了 `README.md` 与 `00-日常使用说明.md` 的首次使用路径
  - 做了 starter-template 漂移审计
  - 修正了 starter `AGENTS.md` 的应用仓库偏置
  - 写入了对应报告
- Phase 3 已完成：
  - 建立了轻量发布纪律：`docs/release/README.md`
  - 把发布要求接回 `README.md` 与 `AGENTS.md`
  - 决定 Hooks 保留在 guides 中，但不进入 starter 默认层
  - 决定当前不增加专门的 maintenance/release Skill
  - 增加了最小发布记录模板
  - 写入了 Hooks 决策报告与 `v1.2` 发布记录
- 已完成 `v1.2` 小版本整理：
  - 写入 `docs/reports/2026-03-28-v1.2-release.md`
  - 把 `README.md` 和 `00-04` 的版本字段统一升到 `v1.2`
  - 把 `docs/release/README.md` 的基线从 `v1.1` 更新为 `v1.2`
- Phase 4 的第一轮能力观察决策已完成：
  - 当前保持一个统一 starter，不拆多模板
  - 用仓库类型适配矩阵覆盖差异
  - 新增 `docs/reports/capability-watch-log.md`，记录官方能力变化与采用决策
- 已完成一轮说明层中文化收口：
  - 活跃维护层文档已改为中文描述
  - starter 目录下的核心说明文档已改为中文
  - roadmap、spec、report、release 说明层文档已大面积中文化

## 进行中

- 当前处于 Phase 4：能力观察与选择性采用。
- 目前没有正在执行的 Phase 4 子切片。

## 关键决策

- 本仓库不再把自己当成“例外项目”，而是按自己的 guides 自己维护自己。
- `00-日常使用说明.md` 是操作层唯一事实来源。
- `04-工作流最佳实践.md` 负责策略、模式切换与高级玩法说明。
- `.claude/` 仅保留为历史参考，不能覆盖当前 Codex 配置。
- 当前默认策略：
  - Hooks 有官方文档，但仍属实验性，不进入 starter 默认层
  - starter 暂不拆多模板
  - release 纪律保留轻量，不引入专门 release Skill

## 最近涉及的文件

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/default.rules`
- `.agents/skills/`
- `.agents/session-notes.md`
- `docs/roadmap/`
- `docs/specs/`
- `docs/architecture/`
- `docs/reports/`
- `docs/release/README.md`
- `templates/starter-project/README.md`
- `templates/starter-project/AGENTS.md`
- `prompt-新项目初始化.md`
- `templates/starter-project/docs/roadmap/README.md`
- `templates/starter-project/docs/roadmap/phase-1-foundation.md`
- `templates/starter-project/docs/specs/README.md`
- `templates/starter-project/docs/architecture/README.md`

## 下一步

下一次进入会话后，先读：

1. `docs/roadmap/README.md`
2. `docs/reports/capability-watch-log.md`
3. 本文件 `.agents/session-notes.md`

然后从 Phase 4 里选一个切片继续：

1. 当官方成熟度变化时，重新判断 Hooks 默认策略
2. 继续观察 release 整理频率，确认是否真的需要专门 Skill
3. 收集更多真实项目接入样本，再决定是否拆分 starter

如果下一次仍然要继续做中文化，只需处理“说明性英文术语”残留，不要碰配置键、命令、路径、文件名和正式功能名。

## 已知风险或阻塞

- 基础 Skills 和 starter 虽然已经更稳定，但仍需要更多真实使用样本来验证契约是否足够稳。
- 当前 Rules 偏保守，后续可能要按真实维护节奏继续微调。
- 当前目录不是 git 仓库，所以这里无法直接使用 `git status`、`git diff`、commit、push 这类 git 流程。
