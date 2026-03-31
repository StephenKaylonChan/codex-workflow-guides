重要：使用 Codex Agent 执行这个任务。

你正在为一个全新项目配置 Codex 协作系统。项目此前没有任何 Codex 配置。

执行规则：
1. 按 Phase 顺序执行，遇到“检查点”必须暂停，先汇报结果，再等用户确认
2. 不要只生成骨架文件；要写出可实际使用的配置内容
3. 最终验证必须实际运行命令，不能只口头说明
4. 优先按 Codex 能力设计，不要把 Claude Code 的机制原样搬过来

首先阅读以下文档，完整理解这套 Codex guides：

- `/Users/stephen/Downloads/00_project/guides-codex/README.md`
- `/Users/stephen/Downloads/00_project/guides-codex/00-日常使用说明.md`
- `/Users/stephen/Downloads/00_project/guides-codex/01-AGENTS配置架构指南.md`
- `/Users/stephen/Downloads/00_project/guides-codex/02-自动化与命令策略.md`
- `/Users/stephen/Downloads/00_project/guides-codex/03-Skills命令配置.md`
- `/Users/stephen/Downloads/00_project/guides-codex/04-工作流最佳实践.md`
- `/Users/stephen/Downloads/00_project/guides-codex/templates/starter-project/README.md`

如果适合，优先把 `templates/starter-project/` 作为最小骨架参考，再按当前项目实际技术栈和命令做二次改写。

如果当前项目不是典型应用仓库，而是文档仓库、脚本仓库、CLI 工具仓库或模板仓库，必须先说明：

- 这个项目的“验证”不一定是 `test/build`
- `AGENTS.md` 和 Skills 要围绕真实维护动作设计
- 不要强行套用 Web/API 项目的默认命令

---

## Phase 1：探索当前项目

先不要改文件。先用只读探索建立项目画像，重点确认：

1. 项目类型：Web / API / CLI / SDK / Monorepo / 工具库
2. 技术栈与版本：前端、后端、数据库、包管理器、构建工具、测试工具
3. 目录结构：关键目录用途
4. 常用命令：启动、测试、lint、build、typecheck、格式化
5. 当前成熟度：新项目 / MVP / 生产维护中
6. 是否已有文档体系：`docs/roadmap/`、`docs/specs/`、`docs/architecture/`
7. 项目属于哪种维护形态：应用开发 / 工具维护 / 文档维护 / 模板维护

输出项目画像：

```text
项目类型:
技术栈:
包管理器:
测试命令:
Lint/格式化命令:
目录结构:
开发阶段:
维护形态:
需要特别注意的风险:
```

检查点：输出项目画像后暂停，等待用户确认。

---

## Phase 2：设计 Codex 版协作系统

根据项目画像，给出这几个设计决策：

1. `AGENTS.md` 如何分层
   - 根 `AGENTS.md` 放什么
   - 哪些子目录需要单独 `AGENTS.md`
   - 是否需要建议用户补 `~/.codex/AGENTS.md` 承载个人偏好
2. `.codex/config.toml` 如何配置
   - `approval_policy`
   - `sandbox_mode`
   - `profiles`
   - MCP 是否需要预留
3. `.codex/rules/default.rules` 如何设计
   - 哪些命令 `allow`
   - 哪些命令 `prompt`
   - 哪些命令 `forbidden`
4. Hooks 如何使用
   - 是否需要通知
   - 是否需要写文件后格式化
   - 是否需要提交前检查
5. Skills 如何落地
   - 必须创建：`audit`、`deep-audit`、`catchup`、`handoff`、`spec`、`task`、`done`、`docs`、`release`、`diagnose`
   - 是否需要项目专属 Skill
   - 哪些流程必须放项目内 `.agents/skills/`，哪些只建议放个人 `~/.codex/skills/`
6. 是否需要 `.codex/agents/`
   - 是否需要 reviewer / docs-researcher 这类自定义 agents
7. 项目文档持久化
   - 是否创建 `docs/roadmap/`
   - 是否创建 `docs/specs/`
   - 是否创建 `docs/architecture/`
   - 是否创建 `docs/development/`

额外要求：

- 如果是文档或模板仓库，要明确“验证命令”和“结构一致性检查命令”
- 如果是工具仓库，要明确哪些命令属于只读检查，哪些属于可能改环境
- 如果 starter 模板不完全适配，要说明保留了哪些结构、改掉了哪些假设
- 如果没有明确、确定性的 Hooks 需求，默认不要把 Hooks 直接塞进 starter 骨架；先用 Rules、Skills、AGENTS.md 和 CI/CD 落稳定底座
- 默认先从统一 starter 改写；只有在当前项目明显不适配这套骨架时，才说明为什么需要脱离 starter 单独设计

检查点：输出“设计方案摘要”后暂停，等待用户确认。

---

## Phase 3：实施初始化

确认后，按以下顺序创建或更新文件：

1. 根 `AGENTS.md`
2. 按需创建子目录 `AGENTS.md`
3. `.codex/config.toml`
4. `.codex/rules/default.rules`
5. `.agents/skills/` 下的 10 个默认 Skills
6. `.codex/agents/` 下按需补 reviewer / docs-researcher
7. `docs/roadmap/README.md`
8. 按需创建首个当前 Phase 文件
9. 如项目复杂，补 `docs/specs/README.md`、`docs/architecture/README.md` 与 `docs/development/README.md`

要求：

- `AGENTS.md` 必须包含：项目结构、常用命令、技术栈、开发约束、完成标准、Git 约束
- Rules 必须体现真实命令边界，不要写成泛泛模板
- Skills 必须按当前项目命令调整，不能保留示例命令
- 如果项目处于初始化早期，roadmap 可以先做成轻量版本，但必须可继续演进
- 如果决定启用 Hooks，要明确说明为什么这个项目值得承担 `Experimental` 能力的维护成本

---

## Phase 4：验证

实施完成后，实际执行并汇报：

1. 配置文件树
2. `AGENTS.md` 关键章节摘要
3. 至少一条规则自检
4. 至少一个项目命令验证（如 `test` / `lint` / `build`，按项目适用性选择）
5. 明确哪些地方因为项目尚未完善而无法验证
6. 用一句话说明“下次进入这个项目时，用户应该怎么开始”
7. 明确回答“这是从 starter 改出来的，还是完全按项目重新设计的”

其中“规则自检”优先使用类似下面的命令明确汇报：

```bash
codex execpolicy check --rules .codex/rules/default.rules -- <代表性命令>
```

最终输出格式：

```text
已创建/更新文件:
验证结果:
未完成项:
建议下一步:
```

在结束前，再按以下清单自查一次：

- `AGENTS.md` 是否已经足够让新会话立即开工
- Rules 是否真的反映了项目命令边界
- roadmap 是否已经建立最小可用结构
- 基础 Skills 是否已有明确职责
- 当前项目最可能踩坑的地方，是否已经写进 AGENTS.md 或验证结果
- 如果项目不是应用仓库，是否已经把“真实验证方式”写清楚
