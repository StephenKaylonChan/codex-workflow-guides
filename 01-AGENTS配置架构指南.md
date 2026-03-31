# AGENTS.md 配置架构指南

> Codex CLI 的指令与配置系统 — 项目规范、多层配置、持久化记忆

**版本**: v1.3（工作流对齐版）
**适用**: Codex CLI v0.115.0 附近（2026-03）
**对标**: Claude Code guides v3.14《01-CLAUDE配置架构指南》

---

## 目录

1. [与 Claude Code 的核心差异](#1-与-claude-code-的核心差异)
2. [两层指令系统](#2-两层指令系统)
3. [AGENTS.md 层级结构](#3-agentsmd-层级结构)
4. [内容规范](#4-内容规范)
5. [项目根 AGENTS.md 模板](#5-项目根-agentsmd-模板)
6. [全局 AGENTS.md 模板](#6-全局-agentsmd-模板)
7. [config.toml 配置体系](#7-configtoml-配置体系)
8. [Profiles 配置集合](#8-profiles-配置集合)
9. [权限与沙盒](#9-权限与沙盒)
10. [ExecPolicy 命令策略](#10-execpolicy-命令策略)
11. [持久化与会话恢复](#11-持久化与会话恢复)
12. [初始化流程](#12-初始化流程)
13. [维护指南](#13-维护指南)

---

## 1. 与 Claude Code 的核心差异

> 如果你从 Claude Code 迁移过来，先看这节。

| 维度 | Claude Code | Codex CLI |
|------|------------|-----------|
| 指令文件 | `CLAUDE.md` | `AGENTS.md` |
| 配置格式 | JSON（`settings.json`） | **TOML**（`config.toml`） |
| 子目录加载 | 懒加载（编辑文件时才加载） | **拼接式**（root→cwd 全部拼接） |
| 文件引用 | `@path/to/file` 语法 | 无等价语法 |
| 覆盖机制 | `CLAUDE.local.md`（仅根目录） | `AGENTS.override.md`（每层都可有） |
| 大小限制 | 200 行（软限制） | **32 KiB**（硬限制，可配） |
| 路径感知规则 | `.claude/rules/*.md`（Markdown 指令注入） | `.codex/rules/*.rules`（**Starlark** 命令策略） |
| 自动记忆 | Auto Memory（全自动学习偏好） | **无等价系统**（依赖 AGENTS.md 手动维护） |
| 权限模型 | 工具+命令模式细粒度匹配 | 全局策略 + OS 级沙盒 |
| Profiles | 无 | `[profiles.<name>]` 命名配置集合 |

**最大差异**：
1. **没有自动记忆** — Claude Code 的 Auto Memory 会自动学习你的偏好并跨会话保持。Codex 没有这个能力，所有持久化指令必须手动写入 AGENTS.md 或 config.toml
2. **OS 级沙盒** — Codex 有操作系统层面的硬性隔离（Seatbelt/Landlock），这是 Claude Code 没有的安全能力
3. **拼接式加载** — 子目录 AGENTS.md 不是按需加载，而是从 root 到 cwd 全部拼接，需要更注意总大小

---

## 2. 两层指令系统

### 2.1 AGENTS.md（你负责维护）

稳定的项目知识，版本控制，团队共享。

**特性**：
- 全局 + 项目根 + 子目录，**root→cwd 拼接**（后面的内容优先级更高）
- 合并大小上限 **32 KiB**（`project_doc_max_bytes` 可调）
- `AGENTS.override.md` 可临时覆盖同层的 `AGENTS.md`
- 纯 Markdown 格式，无特殊语法
- 每次会话重建（无缓存，修改后立即生效）

### 2.2 config.toml（你负责维护）

行为配置，控制 Codex 的工作方式。

**特性**：
- 系统级 → 用户级 → 项目级 → Profile → CLI flags（后者覆盖前者）
- 控制模型、审批策略、沙盒模式、MCP 服务器等
- 项目级 config 需要信任标记才加载

### 2.3 如何分工

| 内容类型 | 存放位置 |
|---------|---------|
| 编码规范、架构约束 | `AGENTS.md`（版本控制，团队共享） |
| 构建/测试/启动命令 | `AGENTS.md` |
| 技术栈版本声明 | `AGENTS.md` |
| 个人开发偏好 | `~/.codex/AGENTS.md` |
| 个人跨项目 Skills | `~/.codex/skills/` |
| 模型选择、审批策略 | `config.toml` |
| MCP 服务器配置 | `config.toml` |
| 沙盒/网络策略 | `config.toml` |
| 临时覆盖指令 | `AGENTS.override.md`（不提交 Git） |
| 命令执行策略 | `.codex/rules/*.rules`（Starlark） |
| 个人命令批准积累 | `~/.codex/rules/default.rules` |

> **与 Claude Code 的关键区别**：Claude Code 的 Auto Memory 会自动记住你的纠正和偏好。Codex 没有这个能力 — 如果你纠正了 Codex 的某个行为，需要**手动写入 AGENTS.md** 才能跨会话保持。

推荐把 Codex 配成“两层复用”：

- 仓库层：`AGENTS.md`、`.codex/`、`.agents/`、`docs/`，放团队共享和可审计状态
- 个人层：`~/.codex/AGENTS.md`、`~/.codex/skills/`、`~/.codex/rules/`、`notify`，放跨仓库个人习惯和机器相关配置

团队流程不要依赖个人层才能工作；个人层应该是增强，而不是前置条件。

---

## 3. AGENTS.md 层级结构

### 3.1 文件发现算法

```
Step 1: 全局作用域
  ~/.codex/AGENTS.override.md   ← 优先检查（存在则用，跳过下一个）
  ~/.codex/AGENTS.md            ← 回退

Step 2: 项目作用域（从 Git root → cwd 逐层）
  <git-root>/AGENTS.override.md → AGENTS.md → 回退文件名
  <git-root>/src/AGENTS.override.md → AGENTS.md → 回退文件名
  <git-root>/src/api/AGENTS.override.md → AGENTS.md → 回退文件名
  ...直到 cwd
```

**规则**：
- 每层最多取**一个文件**（override 优先，然后 AGENTS.md，然后回退文件名）
- 所有层的文件**拼接**在一起，root 在前、cwd 在后
- 后面的内容优先级更高（可以细化或覆盖前面的指令）
- 超过 32 KiB 总量后**停止添加**（后续文件截断或忽略）
- 空文件自动跳过
- Git root 通过 `project_root_markers`（默认 `[".git"]`）检测

### 3.2 Monorepo 推荐目录结构

```
project-root/
├── AGENTS.md                      # 项目总览、共享命令、Git 规范
├── AGENTS.override.md             # 临时覆盖（gitignore，按需创建）
│
├── .codex/
│   ├── config.toml                # 项目级配置（审批、沙盒、MCP）
│   ├── agents/                    # 项目级自定义 subagents
│   │   ├── reviewer.toml
│   │   └── docs-researcher.toml
│   └── rules/
│       └── default.rules          # 命令执行策略（Starlark）
│
├── .agents/
│   └── skills/                    # 自定义 Skills
│       ├── audit/SKILL.md
│       └── catchup/SKILL.md
│
├── apps/
│   ├── web/
│   │   └── AGENTS.md              # 前端专属规范
│   └── api/
│       └── AGENTS.md              # 后端专属规范
│
├── packages/
│   └── shared/
│       └── AGENTS.md              # 共享包规范
│
└── docs/
    ├── roadmap/                   # 项目路线图
    ├── specs/                     # 功能设计文档
    └── development/               # 开发与维护文档
```

**注意**：Codex 的 Skills 目录是 `.agents/skills/`（不是 `.codex/skills/`）。

### 3.3 大小预算参考

| 文件 | 建议大小 | 说明 |
|------|---------|------|
| 全局 `~/.codex/AGENTS.md` | < 2 KiB | 个人偏好，简洁为主 |
| 根 `AGENTS.md` | < 8 KiB | 项目核心规范 |
| 子目录 `AGENTS.md`（各） | < 4 KiB | 模块专属规范 |
| 合计 | < 32 KiB | 超出部分被截断 |

> 如果项目复杂需要更多指令空间，可调整上限：
> ```toml
> project_doc_max_bytes = 65536  # 扩展到 64 KiB
> ```

---

## 4. 内容规范

### 4.1 应该写什么

- **项目结构**：一段话说明各目录用途
- **常用命令**：构建/测试/启动，可直接复制执行
- **硬性约束**：用 **Always / Never / Prefer / Avoid / Do not** 语言表达
- **技术栈声明**：框架名 + 版本号
- **反模式**：明确列出不允许做的事
- **关键决策**：团队选型原因（一行即可）

### 4.2 不应该写什么

- ❌ 密钥、API Key、凭证
- ❌ 冗长的文档内容（保持简洁，用引用替代）
- ❌ 频繁变化的临时指令（放 `AGENTS.override.md`）
- ❌ 代码格式规则（应在 linter 配置中强制执行）
- ❌ 已过时的功能描述

### 4.3 指令语言规范

Codex 官方推荐直接祈使句风格：

| 模糊（❌） | 明确（✅） |
|-----------|---------|
| "尽量写测试" | "Always run `pnpm test` before committing" |
| "保持代码整洁" | "Never commit code with ESLint errors" |
| "注意安全" | "Never hard-code secrets or passwords" |
| "用 TypeScript" | "Always use TypeScript strict mode (`strict: true`)" |
| "优先用现有库" | "Do not reinvent functionality available in established libraries" |
| "代码要简洁" | "Do not create small helper methods that are referenced only once" |

> **与 Claude Code 的区别**：Claude Code 推荐 RFC 2119 语义词（MUST/MUST NOT/SHOULD）。Codex 推荐更自然的 Always/Never/Prefer/Avoid 表述。两者效果类似，选择与你的项目风格一致的即可。

---

## 5. 项目根 AGENTS.md 模板

```markdown
# [项目名称]

> [一句话描述：这是什么项目，解决什么问题]

## 项目结构

- `apps/web/` — React 前端，开发端口 3000
- `apps/api/` — FastAPI 后端，API 端口 8000
- `packages/shared/` — 共享类型定义和工具函数
- `docs/` — 架构文档和 ADR 记录

## 常用命令

```bash
pnpm install          # 安装依赖
pnpm dev              # 启动所有服务
pnpm dev:web          # 仅启动前端
pnpm dev:api          # 仅启动后端
pnpm test             # 运行所有测试
pnpm lint             # 代码检查
pnpm lint:fix         # 自动修复
pnpm build            # 生产构建
```

## 技术栈

- **前端**: React 18, TypeScript 5.x, Vite 6, Tailwind CSS 4, Zustand
- **后端**: FastAPI 0.11x, Python 3.12, SQLAlchemy 2.0, Alembic
- **数据库**: PostgreSQL 17
- **包管理**: pnpm 10

## 开发约束

- Always run `pnpm test` before committing; do not commit if tests fail
- Always use TypeScript strict mode (`strict: true`); never use `any` type
- Never hard-code secrets, passwords, or sensitive config (use environment variables)
- Never modify test files to fit a broken implementation — fix the implementation first
- Do not reinvent functionality available in established libraries
- Prefer one PR per feature; keep diffs readable

## 完成标准

功能实现后，按顺序完成以下验证再报告"完成"：

1. Run `pnpm test` — all tests pass
2. Run `pnpm lint` — no errors
3. Check edge cases: null values, invalid input, insufficient permissions
4. Confirm changes do not break existing functionality (regression check)

## Git 提交规范

```
<type>(<scope>): <subject>

type: feat | fix | docs | refactor | perf | test | chore
Example: feat(auth): add JWT refresh mechanism
```

## 关键架构决策

- State management: Zustand (not Redux — avoid boilerplate)
- API communication: REST (not GraphQL — business complexity doesn't warrant it)
- Authentication: JWT + Refresh Token (not Session — mobile support required)
- Detailed ADR records: `docs/architecture/adr/`
```

---

## 6. 全局 AGENTS.md 模板

存放于 `~/.codex/AGENTS.md`，适用于所有项目的个人偏好，保持 **2 KiB 以内**：

```markdown
# 全局开发偏好

## 工作节奏

- Always explain your approach before writing code; wait for confirmation
- Always wait for my feedback after each independent step
- Prefer explaining the "why" behind each change, not just the "what"

## 代码风格

- Write comments in Chinese
- Prefer readability over cleverness; use meaningful variable names
- Do not add JSDoc to every function unless the logic is non-obvious

## 工具偏好

- Package manager: pnpm
- Git commits: use Conventional Commits format
- Do not auto-push; wait for my confirmation after committing
```

---

## 7. config.toml 配置体系

### 7.1 文件位置与优先级

```
/etc/codex/config.toml          ← 系统级（管理员默认值）
~/.codex/config.toml            ← 用户级（个人配置）
.codex/config.toml              ← 项目级（需信任标记，版本控制）
[profiles.<name>]               ← Profile 覆盖
CLI flags / --config key=val    ← 最高优先级
```

**规则**：后面的覆盖前面的，嵌套表按 key 合并（不是整体替换）。

### 7.2 项目级信任

项目级 `.codex/config.toml` 只在项目被标记为 trusted 时加载：

```toml
# ~/.codex/config.toml
[projects."/Users/stephen/my-project"]
trust_level = "trusted"
```

### 7.3 核心配置项

```toml
# === 模型 ===
model = "gpt-5.4"                           # 模型选择
model_reasoning_effort = "medium"            # minimal | low | medium | high | xhigh
model_reasoning_summary = "auto"             # auto | concise | detailed | none
model_context_window = 128000                # 上下文窗口大小
model_auto_compact_token_limit = 64000       # 自动压缩阈值

# === 审批与沙盒 ===
approval_policy = "on-request"               # untrusted | on-request | never
sandbox_mode = "workspace-write"             # read-only | workspace-write | danger-full-access

# === 交互 ===
personality = "pragmatic"                    # none | friendly | pragmatic
web_search = "cached"                        # disabled | cached | live
file_opener = "vscode"                       # vscode | cursor | windsurf | none

# === 项目文档 ===
project_doc_max_bytes = 32768                # AGENTS.md 大小上限
project_doc_fallback_filenames = []          # 回退文件名

# === 通知 ===
notify = ["notify-send", "Codex"]            # 完成通知命令

# === 提交归属 ===
commit_attribution = "Name <email>"          # Co-author 签名（空字符串=禁用）
```

### 7.4 沙盒网络控制

```toml
[sandbox_workspace_write]
network_access = false                       # 默认禁用网络
writable_roots = ["/Users/YOU/.pyenv/shims"] # 额外可写路径
```

### 7.5 Shell 环境策略

```toml
[shell_environment_policy]
inherit = "all"                    # all | core | none
ignore_default_excludes = false    # 默认过滤含 KEY/SECRET/TOKEN 的变量
exclude = ["AWS_*", "AZURE_*"]     # 排除模式
set = { MY_FLAG = "1" }           # 强制注入
```

### 7.6 MCP 服务器

```toml
# STDIO 方式
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp@latest"]
startup_timeout_sec = 10.0
tool_timeout_sec = 60.0
enabled_tools = ["search", "summarize"]    # 工具白名单

# HTTP 方式
[mcp_servers.github]
url = "https://api.github.com/mcp"
bearer_token_env_var = "GITHUB_TOKEN"
```

### 7.7 TUI 定制

```toml
[tui]
animations = true
show_tooltips = true
status_line = ["model", "context-remaining", "git-branch"]
theme = "catppuccin-mocha"
```

### 7.8 会话历史

```toml
[history]
persistence = "save-all"           # save-all | none
max_bytes = 5242880                # 最大 5 MiB
```

### 7.9 实验性功能

```toml
[features]
multi_agent = false                # 多代理协作
undo = true                       # 撤销支持
fast_mode = true                   # 快速模式
```

---

## 8. Profiles 配置集合

> Codex 独有功能 — Claude Code 没有等价特性。

Profiles 允许为不同场景预设配置组合，一键切换：

```toml
# ~/.codex/config.toml

# 默认 profile
profile = "standard"

[profiles.standard]
model = "gpt-5.4"
model_reasoning_effort = "medium"
approval_policy = "on-request"

[profiles.deep-review]
model = "gpt-5.4"
model_reasoning_effort = "high"
approval_policy = "on-request"

[profiles.lightweight]
model = "gpt-5.4-mini"
approval_policy = "on-request"
model_reasoning_effort = "low"
```

使用方式：

```bash
codex --profile deep-review    # 复杂任务用高配
codex --profile lightweight    # 简单任务省 Token
codex                          # 使用默认 profile
```

**可覆盖的字段**：`model`、`model_provider`、`model_reasoning_effort`、`approval_policy`、`sandbox_mode`、`personality`、`web_search`、`features` 等大部分顶级配置项。

---

## 9. 权限与沙盒

### 9.1 审批策略

| 策略 | 行为 | 适合场景 |
|------|------|---------|
| `untrusted` | 仅已知安全的只读操作自动执行；状态变更需审批 | 不信任的代码库 |
| `on-request` | 默认。沙盒内操作自动执行；沙盒外/危险操作需审批 | 日常开发 |
| `never` | 沙盒内全自动，无需审批 | 完全信任的环境 |

细粒度拒绝：

```toml
approval_policy = { reject = { sandbox_approval = true, rules = false, mcp_elicitations = false } }
```

### 9.2 沙盒模式

| 模式 | 文件 | 命令 | 网络 |
|------|------|------|------|
| `read-only` | 只读 | 不可执行 | 禁用 |
| `workspace-write` | 工作区内读写（`.git/` `.codex/` 只读） | 可执行 | 默认禁用 |
| `danger-full-access` | 全部权限 | 全部权限 | 全部权限 |

**OS 级实现**：
- macOS: Apple Seatbelt (`sandbox-exec`)
- Linux: Landlock + seccomp
- Windows: Native sandbox

调试命令：`codex debug seatbelt` / `codex debug landlock`

### 9.3 快捷模式

```bash
codex --full-auto        # = --sandbox workspace-write + --ask-for-approval on-request
codex --yolo             # = 绕过所有审批和沙盒（仅在隔离 VM 中使用！）
```

### 9.4 运行时切换

```bash
/permissions             # 在会话中切换审批模式
```

### 9.5 与 Claude Code 权限的对比

Claude Code 的权限更**细粒度**，按工具+命令模式匹配：
```json
{"allow": ["Bash(git status)", "Bash(pnpm *)"], "deny": ["Bash(curl * | bash)"]}
```

Codex 的权限更**粗粒度**，但有 OS 级沙盒兜底。如需细粒度命令控制，使用 ExecPolicy（见下节）。

---

## 10. ExecPolicy 命令策略

> 部分替代 Claude Code 的 PreToolUse Hook 命令拦截功能。

ExecPolicy 使用 **Starlark 语言**（类 Python 的安全脚本语言）定义命令执行规则：

### 10.1 规则文件

```python
# ~/.codex/rules/default.rules

# 允许 Git 常规操作
prefix_rule(
    pattern = ["git", "status"],
    decision = "allow",
    justification = "Git status is read-only"
)

prefix_rule(
    pattern = ["git", "commit"],
    decision = "allow",
    justification = "Git commits are safe within workspace"
)

# 危险操作需要确认
prefix_rule(
    pattern = ["git", "push", "--force"],
    decision = "prompt",
    justification = "Force push is destructive"
)

prefix_rule(
    pattern = ["rm", "-rf"],
    decision = "forbidden",
    justification = "Recursive forced deletion is too dangerous"
)

# 允许项目构建命令
prefix_rule(
    pattern = ["pnpm"],
    decision = "allow",
    justification = "Package manager commands are safe"
)
```

### 10.2 决策层级

`forbidden` > `prompt` > `allow`（最严格的规则胜出）

### 10.3 测试规则

```bash
codex execpolicy check --rules ~/.codex/rules/default.rules -- git push --force
# 输出 JSON，显示匹配的规则和最终决策
```

### 10.4 与 Claude Code Hook 的对比

| 功能 | Claude Code Hook | Codex 现状 |
|------|------------------|-----------|
| 命令白名单/黑名单 | ✅ | ✅，Rules / ExecPolicy 更适合这类事情 |
| 测试门禁（commit 前跑测试） | ✅ | 可用 Hooks + Rules + CI 组合实现，但不应只靠 ExecPolicy |
| 自动格式化（写文件后触发） | ✅ | 可优先尝试 Hooks；不足时再用 AGENTS.md + CI 补 |
| 上下文注入（每次 prompt 前） | ✅ | 仍主要依赖 `AGENTS.md`、resume、项目文档 |
| 环境变量注入 | ✅（CLAUDE_ENV_FILE） | 可通过配置与 shell 策略处理 |

**关键判断**：这部分最容易被误解。Codex 不是只能靠 `AGENTS.md` 软约束；更准确的设计是 `Hooks + Rules / ExecPolicy + AGENTS.md + CI/CD` 分层组合。

---

## 11. 持久化与会话恢复

### 11.1 会话历史

Codex 将对话记录保存在 `~/.codex/history.jsonl`：

```toml
[history]
persistence = "save-all"    # save-all | none
max_bytes = 5242880         # 超出后丢弃最旧条目
```

### 11.2 会话恢复

```bash
codex resume --last         # 恢复最近一次会话
codex resume --all          # 列出所有可恢复会话
codex resume SESSION_ID     # 恢复指定会话
```

> **与 Claude Code 的差异**：Claude Code 的 Auto Memory 自动恢复偏好和上下文，无需手动操作。Codex 需要显式 `resume` 来恢复对话历史。

### 11.3 替代 Claude Code 的 handoff/catchup

Codex 没有内置的 `/handoff` `/catchup` 工作流。推荐的替代方案：

**方案 A：用 AGENTS.override.md 做手动交接**
```bash
# 中断前：让 Codex 将进度写入临时文件
"请将当前工作状态写入 AGENTS.override.md，包括已完成、待完成、关键决策"

# 下次恢复：Codex 自动读取 AGENTS.override.md
codex
# 完成后删除 override 文件
```

**方案 B：用 resume 恢复对话**
```bash
# 直接恢复上次对话
codex resume --last
```

**方案 C：自建 Skill（推荐）**
创建 `.agents/skills/handoff/SKILL.md` 和 `.agents/skills/catchup/SKILL.md`，模仿 Claude Code 的行为（详见 Skills 指南）。

---

## 12. 初始化流程

### 新项目初始化

```bash
# 推荐：直接把初始化 prompt 交给 Codex 执行
codex
"读取 /Users/stephen/Downloads/00_project/guides-codex/prompt-新项目初始化.md，按其中的 Phase 帮我配置当前项目"
```

如果你更喜欢先复制一个最小骨架，再让 Codex二次适配，也可以先参考：

`/Users/stephen/Downloads/00_project/guides-codex/templates/starter-project/`

### 信任项目

首次使用项目级 config 时，需要标记信任：

```toml
# ~/.codex/config.toml
[projects."/path/to/project"]
trust_level = "trusted"
```

---

## 13. 维护指南

### 每月检查清单

- [ ] `AGENTS.md` 技术栈版本是否仍然准确？
- [ ] 是否有 Codex 反复犯的错误，还没有加入 Always/Never 约束？
- [ ] 总大小是否接近 32 KiB？需要精简或调大上限？
- [ ] `.codex/config.toml` 配置是否仍然合适？
- [ ] ExecPolicy 规则是否覆盖了应该控制的命令？

### 信号与响应

| 信号 | 响应 |
|------|------|
| Codex 反复忽略某条规定 | 用更强的 Never/Always 语言重写；检查是否被 32 KiB 截断 |
| Codex 反复问同样的背景问题 | 将答案加入 AGENTS.md |
| 同一个 Bug 出现两次 | 在 AGENTS.md 加入防范约束 |
| 某个命令应该禁止但 Codex 执行了 | 在 `.codex/rules/` 添加 `forbidden` 规则 |
| 想临时改变行为但不想修改 AGENTS.md | 创建 `AGENTS.override.md`（用完删除） |

> **没有 Auto Memory 的应对策略**：由于 Codex 不会自动记住你的纠正，建议养成习惯——每次纠正 Codex 的行为后，立即在 AGENTS.md 中添加对应的约束。否则下次会话同样的问题会重复出现。

---

## 附录：环境变量速查

| 变量 | 用途 |
|------|------|
| `CODEX_HOME` | 覆盖配置目录（默认 `~/.codex/`） |
| `OPENAI_API_KEY` | API 密钥 |
| `OPENAI_BASE_URL` | API 端点（代理/自建） |
| `VISUAL` / `EDITOR` | Ctrl+G 打开的外部编辑器 |

---

## 附录：完整配置示例

```toml
# .codex/config.toml — 项目级配置（保守示例）

model = "gpt-5.4"
model_reasoning_effort = "medium"
approval_policy = "on-request"
sandbox_mode = "workspace-write"
personality = "pragmatic"
project_doc_max_bytes = 32768

[sandbox_workspace_write]
network_access = false

[history]
persistence = "save-all"
max_bytes = 5242880

[tui]
status_line = ["model", "context-remaining", "git-branch"]

[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp@latest"]
startup_timeout_sec = 10.0
tool_timeout_sec = 60.0
```

---

**版本**: v1.3（工作流对齐版）
**更新日期**: 2026-03-31
