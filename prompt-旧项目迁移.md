重要：使用 Codex Agent 执行这个任务。

当前项目使用的是旧版 AI 协作体系，可能包含手写上下文文件、零散命令文档或其他非 Codex 原生结构。目标是迁移到 guides-codex 推荐体系：

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/*.rules`
- `.agents/skills/`
- `docs/roadmap/`
- `docs/specs/`
- `docs/architecture/`

执行规则：
1. 迁移不是删除旧文件再套模板，而是提取有效知识后迁移到正确位置
2. 每个 Phase 顺序执行，遇到检查点必须暂停
3. 最终验证必须实际运行

首先阅读以下 guides：

- `/Users/stephen/Downloads/00_project/guides-codex/README.md`
- `/Users/stephen/Downloads/00_project/guides-codex/00-日常使用说明.md`
- `/Users/stephen/Downloads/00_project/guides-codex/01-AGENTS配置架构指南.md`
- `/Users/stephen/Downloads/00_project/guides-codex/02-自动化与命令策略.md`
- `/Users/stephen/Downloads/00_project/guides-codex/03-Skills命令配置.md`
- `/Users/stephen/Downloads/00_project/guides-codex/04-工作流最佳实践.md`

---

## Phase 1：审计旧系统

先探索这些内容：

1. 旧上下文文件：如 `CONTEXT.md`、`CURRENT.md`、`docs/ai-context/`
2. 旧命令目录：如 `.claude/commands/`、自定义提示词目录
3. 旧 AI 配置：如 `CLAUDE.md`、`.claude/`
4. 项目真实技术栈与常用命令

把旧系统内容按以下方式分类：

```text
应迁移到 AGENTS.md 的内容:
应迁移到 config/rules 的内容:
应迁移到 Skills 的内容:
应迁移到 roadmap/spec 的内容:
可以废弃的内容:
```

检查点：输出分类结果后暂停，等待用户确认。

---

## Phase 2：设计迁移方案

根据旧系统内容，明确这些决策：

1. 根 `AGENTS.md` 写什么
2. 哪些旧命令升级为 Skill
3. 哪些旧“人工步骤”应改成 Hooks / Rules / CI
4. 哪些历史进度应沉淀到 roadmap/spec/session-notes
5. 哪些旧文件迁移后可删除，哪些应保留作为历史记录

检查点：输出“迁移方案摘要”后暂停，等待用户确认。

---

## Phase 3：执行迁移

确认后实施：

1. 创建或重写 `AGENTS.md`
2. 创建 `.codex/config.toml`
3. 创建 `.codex/rules/default.rules`
4. 创建或升级 `.agents/skills/`
5. 创建 `docs/roadmap/`、`docs/specs/`、`docs/architecture/` 基础结构
6. 把仍有效的未完成事项整理到 `session-notes.md` 或 roadmap/spec
7. 只有在迁移完成且内容已吸收后，才删除明确可废弃的旧文件

---

## Phase 4：验证

实际验证：

1. 新结构完整
2. 旧知识已迁移而不是丢失
3. 至少一条规则可自检
4. 至少一个项目命令可运行

最终输出：

```text
迁移后的新结构:
保留的旧文件:
删除的旧文件:
验证结果:
后续建议:
```
