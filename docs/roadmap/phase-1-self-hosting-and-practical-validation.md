# Phase 1 - 自举接入与实战验证

## 目标

让 `guides-codex` 真正使用自己的 Codex 项目系统，这样后续维护就能按它推荐给别人的方式运行。

## 清单

- [x] 补齐项目级 `AGENTS.md`、`.codex/`、`.agents/`、`docs/`
- [x] 创建面向本仓库的基础 Skills，而不是只依赖 starter 模板
- [x] 用新的项目级结构跑完一轮真实维护切片
- [x] 根据第一轮真实运行结果，继续收紧 prompt、rules 与 skills

## 退出标准

- 已经至少完成一项真实维护任务，而不是只生成骨架文件
- 验证和状态同步发生在 commit 之前，而不是事后补
- 下一次会话可以通过 `.agents/session-notes.md` 恢复，不再依赖旧 `.claude/` 记忆

## 备注

- 第一轮真实切片的重点，是提升本仓库和 `templates/starter-project/` 的首次使用清晰度。
- 实战里暴露出的主要摩擦是：starter 和初始化 prompt 对文档/模板仓库的引导还不够强，需要额外补清。
