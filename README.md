# Codex 参考文档 (Reference Guides)

> **文档性质**: 通用参考文档，可复用于任何项目
> **版本**: v1.2（自举与维护版，2026-03-28）
> **对标**: Claude Code guides v3.14

本目录包含 AI 协作系统的通用配置指南，核心对象仍是 Codex CLI，但结论同时参考了 Codex 官方文档、公开 GitHub 仓库、更新日志与社区公开资料。

这套文档的目标不是把 Claude Code guides 原样翻译成 Codex 版本，而是回答一个更实际的问题：Codex 能不能也沉淀出一套稳定的工程 guides，让软件工程开发、系统管理、上下文管理和团队协作可复制。结论是可以，但实现方式不同，重心会从 Claude 的 Auto Memory 和高密度 Hooks，转向 Codex 的 `AGENTS.md`、`.codex/config.toml`、Rules、Hooks、Skills、Subagents、Plugins、MCP，以及项目内的 roadmap/spec 文档。

---

## 背景判断

这套 Codex guides 值得做，原因有三点：

1. Claude guides 真正解决的不是“某个命令怎么用”，而是把 AI 协作中的规范、门禁、恢复、分工和交接固化下来，减少每次从零开始的随机性。
2. Codex 现在已经具备足够多的公开能力拼出类似体系。最新官方文档已经覆盖 `AGENTS.md`、Rules、Hooks、Skills、Subagents、Plugins、Workflows、Feature Maturity 等主题，说明它不再只是一个“裸 CLI”。
3. 但 Codex guides 不能照抄 Claude guides。Claude 更像“内置自动化很多、用户去配”；Codex 更像“原语更工程化、需要你主动组装”。因此文档设计应该更偏“系统拼装图”和“团队执行手册”，而不是功能对照表。

---

## 本仓库当前状态

`guides-codex` 现在已经开始自举使用自己的体系。当前仓库根目录已落下：

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/default.rules`
- `.agents/skills/` 与 `.agents/session-notes.md`
- `docs/roadmap/`、`docs/specs/`、`docs/architecture/`、`docs/reports/`

这意味着后续对本仓库的维护，不再只是“写参考文档”，而是要按这套配置实际运行一遍。

当前仓库还额外建立了轻量 release 纪律，见 [docs/release/README.md](./docs/release/README.md)。

---

## 如何使用本仓库

按你的场景，直接走下面 3 条路径之一：

1. 直接落地到项目  
   用 `prompt-新项目初始化.md`、`prompt-guide版本升级.md`、`prompt-旧项目迁移.md`，把任务直接交给 Codex 执行。

2. 想先复制一套最小骨架  
   先看 `templates/starter-project/`，把模板复制到项目里，再让 Codex 二次适配。

3. 想先理解整套方法论  
   先读 `README.md` → `00-日常使用说明.md` → `01/02/03/04`。

### 3 分钟快速入口

如果你现在就想开工，不要先把整套文档从头读到尾。按场景直接走：

1. 新项目第一次接入  
   读 [prompt-新项目初始化](./prompt-新项目初始化.md) 和 [templates/starter-project](./templates/starter-project/README.md)，然后直接让 Codex执行。

2. 已有 Codex 项目升级  
   直接读 [prompt-guide版本升级](./prompt-guide版本升级.md)。

3. 只想先看日常怎么用  
   先读 [00-日常使用说明](./00-日常使用说明.md) 的“第一次：新项目初始化”和“日常开发流程”，其他章节按需回看。

### 推荐阅读顺序

如果你是第一次接触这套体系，推荐顺序：

1. [README.md](./README.md)
2. 按场景选一个入口：
   [prompt-新项目初始化.md](./prompt-新项目初始化.md) 或 [templates/starter-project](./templates/starter-project/README.md) 或 [00-日常使用说明.md](./00-日常使用说明.md)
3. 再按需读 [01-AGENTS配置架构指南.md](./01-AGENTS配置架构指南.md)、[02-自动化与命令策略.md](./02-自动化与命令策略.md)、[03-Skills命令配置.md](./03-Skills命令配置.md)、[04-工作流最佳实践.md](./04-工作流最佳实践.md)

---

## 文档列表

### 研究基础

| 文档 | 说明 |
|------|------|
| [research/00-对比研究](./research/00-对比研究-Claude-Code-vs-Codex-CLI.md) | Claude Code vs Codex CLI 10 维度系统对比，含确认程度标记 |

### 使用说明

| 文档 | 说明 | 何时用 |
|------|------|--------|
| [00-日常使用说明](./00-日常使用说明.md) | 完整使用指南：日常流程、命令速查、9 个常见场景 | 随时参考 |

### 仓库维护

| 文档 | 说明 | 何时用 |
|------|------|--------|
| [docs/release/README.md](./docs/release/README.md) | 本仓库的轻量发布与版本维护纪律 | 做阶段收尾或准备版本整理时 |
| [docs/reports/capability-watch-log.md](./docs/reports/capability-watch-log.md) | 官方能力变化与本仓库采用决策的持续记录 | 复查 Hooks、starter 策略或新能力时 |

### 初始化 / 升级 Prompt

| 文档 | 说明 | 何时用 |
|------|------|--------|
| [prompt-新项目初始化](./prompt-新项目初始化.md) | 直接交给 Codex，给一个全新项目落地这套体系 | 新项目第一次接入 |
| [prompt-guide版本升级](./prompt-guide版本升级.md) | 对照最新 guides，对已有 Codex 项目做增量升级 | guides 更新后 |
| [prompt-旧项目迁移](./prompt-旧项目迁移.md) | 把旧 AI 协作体系迁移到 Codex guides 结构 | 老项目迁移 |
| [prompt-更新指南规范](./prompt-更新指南规范.md) | 让 Codex 搜索最新公开资料并更新 guides-codex 本身 | 维护本仓库时 |

### Starter 模板

| 文档 | 说明 | 何时用 |
|------|------|--------|
| [templates/starter-project](./templates/starter-project/README.md) | 最小可用项目骨架，含基础配置和 6 个基础 Skills 模板 | 想直接复制起步时 |

### 配置参考文档

| 文档 | 说明 | 优先阅读 |
|------|------|----------|
| [01-AGENTS配置架构指南](./01-AGENTS配置架构指南.md) | AGENTS.md 层级 + config.toml + Profiles + 沙盒 | 第一读 |
| [02-自动化与命令策略](./02-自动化与命令策略.md) | Hooks + Rules/ExecPolicy + notify + CI/CD 分层防护 | 第二读 |
| [03-Skills命令配置](./03-Skills命令配置.md) | Skills 系统 + 6 个自定义 Skill + 社区生态 | 第三读 |
| [04-工作流最佳实践](./04-工作流最佳实践.md) | 开发循环 + Subagents + MCP + 远程环境/长任务 + 反模式 | 随时参考 |

---

## 与 Claude Code 的核心差异

### Claude 强、Codex 需要重组实现的

| Claude Code 能力 | Codex 替代方案 | 参见 |
|------------------|---------------|------|
| 高密度生命周期 Hooks | Codex Hooks + Rules + CI/CD；可做很多自动化，但覆盖面仍比 Claude 窄 | 文档 02 |
| Auto Memory（全自动记忆） | `AGENTS.md` + `resume` + roadmap/spec/session-notes | 文档 01、04 |
| `@path` 引用语法 | 子目录 `AGENTS.md` + 明确文件路径指令 | 文档 01 |
| `.claude/rules/*.md` 路径感知指令 | 子目录 `AGENTS.md` 负责上下文，Codex Rules 负责命令策略 | 文档 01、02 |
| `/simplify` 三维并行审查 | `/review` + Subagents | 文档 04 |
| `/batch` 高层工作树并行 | Subagents + worktrees + 批处理工作流 | 文档 04 |
| `/loop` 定时重复 | 当前官方文档未见直接等价 | — |
| `remote-control` QR 码 | 当前官方文档未见直接等价 | — |

### Codex 独有的

| Codex 能力 | 说明 | 参见 |
|------------|------|------|
| OS 级沙盒 | Seatbelt (macOS) / Landlock (Linux) | 文档 01 |
| Profiles | `--profile` 命名配置集合 | 文档 01、04 |
| Rules / ExecPolicy | Starlark 命令策略（allow/prompt/forbidden） | 文档 01、02 |
| Hooks | 官方已提供 Hooks 文档，可做前后置自动化、通知和门禁链路 | 文档 02 |
| Plugins | 可构建和分发插件，而不只是本地 Skills | 文档 03 |
| Subagents | 官方文档已公开，内置 `default` / `worker` / `explorer` 等角色 | 文档 04 |
| 远程环境 / Web 长任务 | 适合长时任务、异步执行、隔离环境 | 文档 04 |
| `$skill-installer` | 社区 Skill 一键安装（官方公开生态） | 文档 03 |
| `$skill-creator` | 交互式 Skill 创建向导 | 文档 03 |
| 桌面应用 | `codex app`（macOS，内置 Worktree UI） | 文档 04 |

---

## 命令体系

### 内置斜杠命令（无需配置）

| 命令 | 用途 | 频率 |
|------|------|------|
| `/plan` | Plan Mode 规划 | 复杂任务前 |
| `/review` | 代码审查 | 每次功能完成后 |
| `/clear` | 清空上下文 | 按需 |
| `/compact` | 有损压缩上下文 | 备选 |
| `/fork` | 分支对话 | 探索不同方案 |
| `/new` | 新对话（不清屏） | 按需 |
| `/model` | 切换模型 | 按需 |
| `/permissions` | 运行时切换审批模式 | 按需 |
| `/skills` | 浏览 Skills | 按需 |
| `/mcp` | MCP 管理 | 按需 |

### 自定义 Skills（建议项目内放 `.agents/skills/`）

| 命令 | 用途 | 频率 |
|------|------|------|
| `$catchup` | 清空上下文后快速恢复 | 按需 |
| `$handoff` | 提交变更 + 生成交接文档 | 中断前 |
| `$spec` | 讨论成果整理为设计文档 | 需求讨论后 |
| `$done` | 功能完成收尾检查 | 功能完成后 |
| `$audit` | 项目健康检查 | 每周 |
| `$deep-audit` | 全面深度审计 | Phase 完成后 |

### 内置系统 Skills（随 Codex 安装）

| 命令 | 用途 |
|------|------|
| `$skill-creator` | 交互式创建新 Skill |
| `$skill-installer` | 安装社区 Skill |

---

## 新项目目录结构

```
project-root/
├── AGENTS.md                      # AI 指令核心（< 8 KiB）
├── AGENTS.override.md             # 临时覆盖（gitignore）
│
├── .codex/
│   ├── config.toml                # 项目级配置（审批、沙盒、MCP）
│   └── rules/
│       └── default.rules          # ExecPolicy 命令策略（Starlark）
│
├── .agents/
│   ├── skills/                    # 自定义 Skills
│   │   ├── audit/SKILL.md
│   │   ├── deep-audit/SKILL.md
│   │   ├── catchup/SKILL.md
│   │   ├── handoff/SKILL.md
│   │   ├── spec/SKILL.md
│   │   └── done/SKILL.md
│   ├── session-notes.md           # 交接文档（$handoff 生成）
│   └── plan.md                    # 实施计划（临时）
│
├── apps/
│   ├── web/
│   │   └── AGENTS.md              # 前端专属规范
│   └── api/
│       └── AGENTS.md              # 后端专属规范
│
└── docs/
    ├── roadmap/                   # 项目路线图（进度跟踪）
    │   ├── README.md
    │   └── phase-N-xxx.md
    ├── specs/                     # 功能设计文档（$spec 生成）
    └── reports/                   # 审计报告（$deep-audit 生成）
```

---

## 新项目初始化步骤

1. 在项目根目录运行 `codex`
2. 优先读取 [prompt-新项目初始化](./prompt-新项目初始化.md)
3. 如果想更快起步，先复制 [templates/starter-project](./templates/starter-project/README.md) 的骨架
4. 让 Codex 按项目真实技术栈改写 `AGENTS.md`、config、rules、skills
5. 标记项目信任（`~/.codex/config.toml` 中添加 `[projects."/path"]`）
6. 运行至少一个真实项目命令，确认初始化不是空壳

---

## 重要注意事项

### 无 Auto Memory 的应对

Codex 不会自动记住你的纠正和偏好。**每次纠正 Codex 的行为后，立即在 AGENTS.md 中添加对应的 Always/Never 约束**。否则下次会话同样的问题会重复出现。

### Hooks 现状

Hooks 已经进入 Codex 官方文档，不应再按“未发布能力”来设计 guides。更准确的判断是：

- Codex 现在有 Hooks，但覆盖面和生态成熟度仍弱于 Claude Code
- 应优先把 Hooks 视为和 Rules / ExecPolicy / CI/CD 并列的一层自动化原语
- 某些 Claude 式能力仍没有直接等价，例如 Auto Memory 和更强的前置上下文注入

### 信息源

所有内容基于 2026-03-28 前后的官方文档、Codex 更新日志、GitHub 开源仓库与公开社区资料复核，详见 [对比研究文档](./research/00-对比研究-Claude-Code-vs-Codex-CLI.md)。

---

**文档性质**: 通用参考模板（可跨项目复用）
**最后更新**: 2026-03-28（v1.2）
