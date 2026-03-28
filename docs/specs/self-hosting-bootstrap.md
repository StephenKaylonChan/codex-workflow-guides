# 自举接入规格

## 问题

`guides-codex` 一直在讲项目应该如何使用 `AGENTS.md`、`.codex/config.toml`、Rules、Skills、roadmap、spec 与 session-notes，但在早期这个仓库自己并没有真正使用这套结构。这会造成可信度缺口，也让很多流程摩擦在纸面上看不出来。

## 范围

- 为本仓库落下项目级 Codex 配置与工作流文件
- 定义面向本仓库维护工作的基础 Skills
- 补齐 roadmap、spec、architecture、session-notes 等状态载体

## 非范围

- 不重新重写整套 guides
- 不在第一轮就加入所有高级 Skill
- 不在第一轮就引入复杂自动化

## 受影响文档或模板

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/default.rules`
- `.agents/skills/`
- `.agents/session-notes.md`
- `docs/roadmap/`
- `docs/specs/`
- `docs/architecture/`
- `docs/reports/`

## 实施方案

1. 先从 starter 模板结构出发。
2. 把应用仓库假设替换成适合文档仓库的规则。
3. 把验证方式改成一致性检查，而不是应用测试。
4. 补齐 roadmap 和 spec，让后续维护有持久化状态。

## 验证计划

- 确认项目级文件全部存在，并且与仓库现实相符。
- 确认 AGENTS 命令和 Rules 更像文档仓库，而不是应用仓库。
- 确认 roadmap、spec、session-notes 能明确指出下一次真实维护切片。

## 开放问题

- 后续是否需要超出基础六件套的专用维护 Skill
- starter 模板与本仓库自举配置，未来是否需要更多共享生成内容

## 推荐的第一实施切片

先用这套自举结构跑一轮真实维护任务，再根据实际摩擦继续收紧 Skills、Rules 和入口设计。

## 进展记录

第一轮真实切片已经完成：

- 优化了 starter 的首次上手说明
- 收紧了面向文档/模板/工具仓库的初始化 prompt
- 验证了本仓库已经能通过自己的结构维护 roadmap、spec 和 session 状态
