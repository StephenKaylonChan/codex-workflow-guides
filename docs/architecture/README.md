# 架构说明

本仓库可以分成 4 层：

1. 主指南：`README.md`、`00-04`
2. 动作入口：`prompt-*.md`
3. 可复用项目骨架：`templates/starter-project/`
4. 自举维护系统：`AGENTS.md`、`.codex/`、`.agents/`、`docs/`

各层职责：

- 主指南负责解释整套系统
- prompt 文件负责把系统直接交给 Codex 执行
- starter 模板负责提供可复制的最小骨架
- 自举维护系统负责让本仓库本身长期可维护

保持这份说明简短即可，它的目标是帮助新会话快速定位仓库结构。
