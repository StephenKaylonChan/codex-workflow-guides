重要：使用 Codex Agent 执行这个任务。

当前项目已经在使用 Codex 协作体系，但本地 guides-codex 已更新。需要对当前项目做增量升级，而不是重建。

执行规则：
1. 先审计差距，再修改；不要直接大面积覆盖
2. 输出差距清单后必须暂停，等用户确认
3. 升级 Skill 时要以最新 guide 为准，必要时整段重写，而不是只改 frontmatter
4. 最终验证必须实际运行

首先阅读以下最新 guides：

- `/Users/stephen/Downloads/00_project/guides-codex/README.md`
- `/Users/stephen/Downloads/00_project/guides-codex/00-日常使用说明.md`
- `/Users/stephen/Downloads/00_project/guides-codex/01-AGENTS配置架构指南.md`
- `/Users/stephen/Downloads/00_project/guides-codex/02-自动化与命令策略.md`
- `/Users/stephen/Downloads/00_project/guides-codex/03-Skills命令配置.md`
- `/Users/stephen/Downloads/00_project/guides-codex/04-工作流最佳实践.md`

---

## Phase 1：审计现状

读取当前项目中所有现有配置：

1. `AGENTS.md` 与子目录 `AGENTS.md`
2. `.codex/config.toml`
3. `.codex/rules/*.rules`
4. `.agents/skills/*/SKILL.md`
5. `docs/roadmap/`
6. `docs/specs/`
7. `docs/architecture/`

重点检查：

- 是否仍符合最新 guides 的结构
- 是否有明显过时结论
- Rules 是否与项目现有命令一致
- Skills 是否仍匹配项目现状
- roadmap/spec 是否还在被真正使用

---

## Phase 2：输出差距清单

按以下结构输出：

```text
高优先级差距:
- ...

中优先级差距:
- ...

低优先级差距:
- ...

建议本次执行范围:
- 必改
- 可选
- 暂不处理
```

检查点：输出差距清单后暂停，等待用户确认。

---

## Phase 3：实施升级

确认后，只改需要更新的内容，优先级如下：

1. 修正文档或配置里的错误前提
2. 更新 `AGENTS.md` 的完成标准、约束和项目结构
3. 更新 `.codex/config.toml` 与 Rules
4. 更新基础 Skills
5. 补齐缺失的 roadmap/spec/architecture 入口文件

要求：

- 不要破坏项目已有可用配置
- 不要删除用户自定义约束，除非已经明显过时且有替代方案
- 对每个修改说明“为什么要改”

---

## Phase 4：验证与汇报

实际验证以下内容：

1. 配置文件无语法错误
2. 至少一条 Rules 自检通过
3. 至少一个 Skill 的结构完整
4. 如修改了命令，运行相关命令验证

最终输出：

```text
本次升级范围:
已更新文件:
验证结果:
仍建议后续处理的事项:
```
