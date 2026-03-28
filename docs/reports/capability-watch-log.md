# 能力观察记录

用这个文件持续记录“官方能力变化 -> guides 是否采用 -> 为什么”的判断，避免每次都从零回溯。

## 2026-03-28

### Hooks

- 来源：
  - https://developers.openai.com/codex/hooks
  - https://developers.openai.com/codex/feature-maturity
- 观察：
  - Hooks 已有官方文档
  - 当前官方成熟度仍为 `Experimental`
  - 文档说明其处于 active development，且需要显式启用 `codex_hooks`
- 采用决策：
  - guides 继续保留 Hooks 章节
  - starter 默认不附带 Hooks
- 原因：
  - 这是有效能力，但还不适合进入默认稳定底座

### Starter 是否拆分

- 观察：
  - 当前真实差异主要集中在命令、验证方式、目录说明
  - 共享底座仍然成立：`AGENTS.md`、`.codex/`、`.agents/`、`docs/`
- 采用决策：
  - 继续保留一个 starter
  - 用仓库类型适配矩阵覆盖差异
- 原因：
  - 现在拆多个 starter 的维护成本高于收益

## 使用方式

出现下面任一情况时，就追加一条记录：

- 官方文档把某项能力从 `Experimental` 提升到更稳定阶段
- 本仓库改变了某项默认策略
- starter、prompt、核心 guides 对某项能力的默认态度发生变化
