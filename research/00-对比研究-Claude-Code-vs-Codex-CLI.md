# Claude Code vs Codex CLI 对比研究

> 基于 Claude Code guides v3.14 的维度框架，系统对比 OpenAI Codex CLI 的对应能力
>
> **研究日期**: 2026-03-28（在 2026-03-10 初稿基础上复核）
> **Claude Code 版本**: 2.x / guides v3.14
> **Codex CLI 版本**: 官方 changelog 已公开到 v0.115.0（2026-03）
>
> **指南编写进度**:
> - [x] 01-AGENTS配置架构指南.md（v1.1 复核修订）
> - [x] 02-自动化与命令策略.md（v1.1 复核修订，更新为 Hooks + Rules / ExecPolicy + CI/CD 组合设计）
> - [x] 03-Skills命令配置.md（v1.1 复核修订，明确 Skills 边界与维护方式）
> - [x] 04-工作流最佳实践.md（v1.1 复核修订，修正远程环境与 Subagents 口径）
> - [x] 00-日常使用说明.md（v1.1 复核修订，补 prompt 入口与新版日常操作）
>
> **深度研究中发现的重要修正**:
> - Codex **没有** Claude Code 那样的 Auto Memory 自动记忆系统
> - Codex 的 memories 机制细节未公开，实际持久化依赖 AGENTS.md + history
> - `/plan` 命令在 Codex 官方文档中已确认存在
> - Hooks 已进入 Codex 官方文档，不应再按“未发布”处理
> - Subagents 已进入官方文档，公开角色以 `default` / `worker` / `explorer` 为主
> - Plugins 已进入官方文档，说明 Codex 的“可扩展工作流”不再只靠本地 Skills

---

## 确认程度说明

| 标记 | 含义 |
|------|------|
| ✅ 已确认 | 官方文档明确记录，可直接引用 |
| ⚠️ 待验证 | 社区提及或文档描述模糊，需实测确认 |
| ❌ 未发布 | GitHub PR/Issue 阶段，尚未合并到正式版 |
| 🚫 不存在 | Codex 无对应功能 |
| 🆕 Codex 独有 | Claude Code 无对应功能 |

---

## 1. 指令文件系统

### 1.1 核心指令文件

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 文件名 | `CLAUDE.md` | `AGENTS.md` | ✅ |
| 全局位置 | `~/.claude/CLAUDE.md` | `~/.codex/AGENTS.md` | ✅ |
| 项目位置 | `./CLAUDE.md` | `./AGENTS.md`（Git root 起） | ✅ |
| 子目录 | `./src/CLAUDE.md`（懒加载） | `./src/AGENTS.md`（root→cwd 拼接） | ✅ |
| 覆盖文件 | `CLAUDE.local.md`（gitignore） | `AGENTS.override.md`（每层都可有） | ✅ |
| 大小限制 | 200 行（遵从度下降） | 32 KiB（`project_doc_max_bytes` 可配） | ✅ |
| 初始化 | `/init` | `/init` | ✅ |
| 引用语法 | `@path/to/file`（最多 5 层） | 🚫 无等价语法 | ⚠️ |
| 回退文件名 | 无 | `project_doc_fallback_filenames`（默认空） | ✅ |

**关键差异**：
- Claude Code 的子目录 CLAUDE.md 是**懒加载**（编辑对应目录才加载）；Codex 是**拼接式**（从 root 到 cwd 全部拼接，后面的覆盖前面的）
- Claude Code 有 `@` 引用语法可以拉入外部文件；Codex 需要通过回退文件名或直接放在 AGENTS.md 中
- Codex 的 `AGENTS.override.md` 每层都可以有，Claude Code 的 `CLAUDE.local.md` 只在根目录

---

### 1.2 配置文件

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 格式 | JSON | **TOML** | ✅ |
| 项目级 | `.claude/settings.json` | `.codex/config.toml` | ✅ |
| 用户级 | `~/.claude/settings.json` | `~/.codex/config.toml` | ✅ |
| 系统级 | 企业托管 CLAUDE.md | `/etc/codex/config.toml` | ✅ |
| 个人覆盖 | `.claude/settings.local.json` | 无单独文件（config.toml 合并） | ✅ |
| Profiles | 无 | `[profiles.<name>]` + `--profile` | ✅ 🆕 |
| 组织管控 | 企业托管（最高优先级） | `/etc/codex/requirements.toml` | ✅ |
| 环境变量 | 无统一 home 变量 | `CODEX_HOME`（默认 `~/.codex`） | ✅ |
| Schema | `$schema` JSON Schema | 无 | — |

**关键差异**：
- Codex 的 **Profiles** 系统是 Claude Code 没有的——可以为不同场景预设配置组合（如 `deep-review` profile 用 GPT-5-Pro + 自动审批）
- 优先级：Codex 是 CLI flags > `--profile` > 项目 > 用户 > 系统 > 默认值

---

### 1.3 路径感知规则

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 机制 | `.claude/rules/*.md`（Markdown + glob frontmatter） | `.codex/rules/*.rules`（**Starlark** 语言） | ✅ |
| 触发方式 | 文件 Read 操作时按 paths 条件加载 | 启动时扫描，命令级规则匹配 | ✅ |
| 用途 | 注入上下文指令（前端规范、后端规范） | 权限/策略执行（execpolicy：allow/prompt/forbidden） | ✅ |
| 全局规则 | 无 | `~/.codex/rules/` | ✅ |

**关键差异**：
- 这两个系统**目的完全不同**：Claude Code 的 rules 是「上下文指令注入」（告诉 AI 怎么写代码）；Codex 的 rules 是「命令权限策略」（决定哪些命令允许执行）
- 如果要在 Codex 中实现 Claude Code 的路径感知指令效果，应该用子目录 `AGENTS.md`

---

### 1.4 自动记忆系统

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 名称 | Auto Memory | memories 系统 | ✅ |
| 存储位置 | `~/.claude/projects/<hash>/memory/` | Codex 内部管理 | ✅ |
| 自动加载 | `MEMORY.md` 前 200 行自动加载 | 细节不明 | ⚠️ |
| 手动管理 | `/memory` 命令 | `codex debug clear-memories` | ✅ |
| 禁用方式 | `autoMemoryEnabled: false` | 细节不明 | ⚠️ |
| 会话历史 | Auto Memory 自动记录 | 独立 `[history]` 系统，保存对话记录 | ✅ |
| 历史持久化 | 始终保存 | `persistence = "save-all"` 或 `"none"` | ✅ |
| 会话恢复 | 自动恢复（无需操作） | `codex resume --last` 显式恢复 | ✅ |

**关键差异**：
- Claude Code 的 Auto Memory 是全自动的（自动学习偏好、自动恢复）；Codex 的 memories 系统细节较少公开，会话恢复需要显式 `resume` 命令
- Codex 的 history 和 memories 是两个独立系统

---

## 2. Hooks 自动化

### 2.1 生命周期钩子

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| Hook 事件数 | **19 个**（SessionStart/Stop/Pre/PostToolUse 等） | 已有官方 Hooks 文档，但覆盖面仍小于 Claude Code | ✅ |
| Handler 类型 | command / prompt / http / agent | 官方已公开 Hooks 配置；具体以文档页为准 | ✅ |
| 异步支持 | `"async": true` | 官方能力已公开，但成熟度仍需继续跟踪 | ⚠️ |
| Frontmatter Hooks | Skill/Agent 内部定义 Hooks | 当前官方文档未见直接等价 | ⚠️ |
| updatedInput | 透明修改工具输入参数 | 当前未见强等价公开能力 | ⚠️ |
| CLAUDE_ENV_FILE | SessionStart 独享环境变量注入 | 当前未见强等价公开能力 | ⚠️ |

**Codex 的替代方案**：

| Claude Code Hook | Codex 替代 | 确认 |
|------------------|-----------|------|
| SessionStart（会话启动） | Hooks + `AGENTS.md` + `resume`/项目文档组合 | ⚠️ |
| PreToolUse（工具调用前） | Hooks + `execpolicy`（命令级 allow/prompt/forbidden） | ✅ |
| PostToolUse（工具调用后） | Hooks + AGENTS.md / CI/CD | ✅ |
| Stop（完成通知） | `notify` + Hooks | ✅ |
| UserPromptSubmit | 当前仍主要依赖 `AGENTS.md` 和显式上下文管理 | ⚠️ |
| PreCompact | `compact_prompt` + 项目文档持久化 | ⚠️ |
| Notification | `notify` 配置 | ✅ |

### 2.2 通知系统

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 配置方式 | Hook 脚本（自定义） | `notify` 配置项（argv 数组） | ✅ |
| 支持事件 | Stop / Notification / 任意 Hook | 仅 `agent-turn-complete` | ✅ |
| 输入格式 | Hook stdin JSON | 脚本参数 JSON | ✅ |

```toml
# Codex config.toml
notify = ["notify-send", "Codex"]
```

### 2.3 命令执行策略 (execpolicy)

Codex 独有的命令级权限控制系统，替代了部分 PreToolUse Hook 的功能：

```bash
# 测试命令是否被允许
codex execpolicy --rules /path/to/rules.toml COMMAND...
```

决策类型：`allow`（自动执行）、`prompt`（询问用户）、`forbidden`（禁止）

**这是 Codex 指南中需要重点覆盖的内容**，因为它替代了 Claude Code 用 Hook 实现的测试门禁、危险命令拦截等功能。

---

### 2.4 差异总结

**这仍然是 Claude Code 和 Codex 之间的核心差距之一。** 但差距的描述需要更新：Codex 不再是“没有 Hooks”，而是“Hooks 已公开，但仍比 Claude Code 更窄、更工程化、需要与 Rules / CI 组合使用”。

**对指南的影响**：
- Claude Code 指南中文档 02（Hooks）不能直接照搬，但也不能再完全绕开 Hooks
- Codex 指南应采用 `Hooks + Rules/ExecPolicy + AGENTS.md + CI/CD` 的组合式设计
- 某些自动化仍需要替代方案，尤其是 Auto Memory 和更强的前置上下文注入

---

## 3. Skills / 命令系统

### 3.1 自定义 Skills

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 目录结构 | `.claude/skills/*/SKILL.md` | `.agents/skills/*/SKILL.md` | ✅ |
| 格式 | YAML frontmatter + Markdown | YAML frontmatter + Markdown | ✅ |
| 必填字段 | `name` | `name` + `description` | ✅ |
| 调用方式 | `/skill-name` | `$skill-name` 或 `/skills` 浏览 | ✅ |
| 自动触发 | 基于 `description` 自动检测 | 同样支持 | ✅ |
| 参数传递 | `$ARGUMENTS`, `$1`, `$2` | 类似 | ⚠️ |
| 子代理执行 | `context: fork` | 未明确 | ⚠️ |
| 模型覆盖 | `model: haiku/sonnet/opus` | 未明确 | ⚠️ |
| 动态上下文 | `` !`command` `` 语法 | 未明确 | ⚠️ |
| Skill 内 Hooks | 支持 frontmatter hooks | 未明确 | ⚠️ |
| 社区安装 | 无 | `$skill-installer` 🆕 | ✅ |
| 创建向导 | 无 | `$skill-creator` 🆕 | ✅ |

**关键差异**：
- 调用前缀不同：Claude Code 用 `/`，Codex 用 `$`
- Codex 有社区生态：`$skill-installer` 可安装社区 skills（如 `gh-fix-ci`, `notion-knowledge-capture`）
- Codex 的 skills 目录名是 `.agents/skills/`，不是 `.codex/skills/`

### 3.2 内置命令对比

| Claude Code 命令 | Codex 等价 | 确认 |
|-----------------|-----------|------|
| `/simplify`（三维并行审查） | `/review`（审查预设） | ✅（功能不完全等价） |
| `/batch`（大规模并行变更） | `spawn_agents_on_csv`（CSV 批处理） | ✅（机制不同） |
| `/loop`（定时重复） | 🚫 无等价 | 🚫 |
| `/clear` | `/clear` | ✅ |
| `/compact` | `/compact` | ✅ |
| `/cost` | `/status`（含 Token 使用） | ✅ |
| `/context` | `/statusline`（自定义状态栏） | ✅（不完全等价） |
| `/plan` | `/plan`（确认存在，支持内联提示） | ✅ |
| `/diff` | `/diff`（含 untracked 文件） | ⚠️ |
| `/rewind` | 🚫 未确认 | ⚠️ |
| `/fork` | `/fork` | ✅ |
| `/model` | `/model` | ✅ |
| `/fast` | 🚫（通过 `/model` 调整 effort） | 🚫 |
| `/memory` | `codex debug clear-memories` | ✅（功能较弱） |
| `/hooks` | 🚫 | 🚫 |
| `/mcp` | `/mcp` | ✅ |
| `/skills` | `/skills` | ✅ |
| `/tasks` (Ctrl+T) | `/agent`（线程管理） | ✅（不完全等价） |
| `/copy` | `/copy` | ✅ |

### 3.3 Codex 独有命令

| 命令 | 用途 | 确认 |
|------|------|------|
| `/permissions` | 运行时切换审批模式 | ✅ 🆕 |
| `/personality` | 切换沟通风格（friendly/pragmatic/none） | ✅ 🆕 |
| `/new` | 新对话（不清屏） | ✅ 🆕 |
| `/realtime` | 实时语音模式 | ✅ 🆕 |
| `/audio` | 选择麦克风/扬声器 | ✅ 🆕 |
| `/review` | 代码审查预设 | ✅ 🆕 |
| `/experimental` | 切换实验功能（如多代理） | ✅ 🆕 |
| `/statusline` | 自定义状态栏字段 | ✅ 🆕 |
| `/agent` | 切换代理线程 | ✅ 🆕 |
| `/mention` | 附加文件到对话 | ✅ 🆕 |
| `/resume` | 恢复保存的对话 | ✅ 🆕 |
| `/debug-config` | 配置层诊断 | ✅ 🆕 |
| `/feedback` | 提交日志给维护者 | ✅ 🆕 |

---

## 4. 权限与沙盒

### 4.1 审批策略

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 配置方式 | `permissions.allow/ask/deny`（按工具+模式匹配） | `approval_policy` 全局策略 | ✅ |
| 策略类型 | allow / ask / deny（细粒度） | `on-request` / `untrusted` / `never` + `reject` | ✅ |
| 运行时切换 | 无 | `/permissions` | ✅ |
| 快捷模式 | 无 | `--full-auto` / `--yolo` | ✅ |

**Claude Code 的权限更细粒度**：
```json
{
  "allow": ["Bash(git status)", "Bash(pnpm *)"],
  "ask": ["Bash(git push *)"],
  "deny": ["Bash(curl * | bash)"]
}
```

**Codex 的权限更粗粒度但有 OS 级沙盒**：
```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

### 4.2 沙盒模型

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 沙盒类型 | 无原生沙盒 | **OS 级沙盒** | ✅ |
| macOS | — | Seatbelt (`sandbox-exec`) | ✅ |
| Linux | — | Landlock + seccomp | ✅ |
| Windows | — | Native sandbox | ✅ |
| 沙盒模式 | — | `read-only` / `workspace-write` / `danger-full-access` | ✅ |
| 网络控制 | 无 | 默认禁用，`network_access = true` 开启 | ✅ |
| 受保护路径 | — | `.git/`, `.agents/`, `.codex/` 只读 | ✅ |
| 调试 | — | `codex debug seatbelt` / `codex debug landlock` | ✅ |

**这是 Codex 的核心优势之一**。Claude Code 依赖 Hook + 权限配置做软性约束，Codex 直接在操作系统层面做硬性隔离。

---

## 5. 工作流

### 5.1 核心开发循环

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 推荐流程 | Explore → Plan → Code → Verify → Simplify → Commit | 无明确的标准循环 | — |
| Plan Mode | `Shift+Tab` / `/plan` | `/plan`（确认存在） | ✅ |
| 编辑器集成 | `Ctrl+G` 打开编辑器 | `Ctrl+G` 打开 `$VISUAL`/`$EDITOR` | ✅ |
| 代码审查 | `/simplify`（三维并行 agent） | `/review`（审查预设） | ✅ |
| 批量变更 | `/batch`（worktree agent 并行） | `spawn_agents_on_csv`（CSV 行并行） | ✅ |

### 5.2 并行开发

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| Worktrees | `claude --worktree <branch>` | 桌面应用内置 worktree 支持 | ✅ |
| 多目录 | 无 | `--add-dir` 授权额外目录 | ✅ 🆕 |
| 后台任务 | `Ctrl+B` 转后台 / `/tasks` | `/ps` 查看后台终端 | ✅ |

### 5.3 多代理

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 名称 | Agent Teams | Multi-agent | ✅ |
| 状态 | 实验性 | 实验性 | ✅ |
| 启用方式 | 环境变量 | `/experimental` 或 `[features] multi_agent = true` | ✅ |
| 最大线程 | 未明确 | `max_threads = 6` | ✅ |
| 嵌套深度 | 未明确 | `max_depth = 1` | ✅ |
| 内置角色 | 无预设角色 | default / worker / explorer | ✅ 🆕 |
| 自定义角色 | Agent YAML 文件 | `[agents.<name>]` 配置段 | ✅ |
| 批量处理 | `/batch` | `spawn_agents_on_csv` | ✅ |
| 线程切换 | 无 | `/agent` | ✅ 🆕 |
| worker 超时 | 无 | `job_max_runtime_seconds = 1800` | ✅ 🆕 |

### 5.4 MCP 支持

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| STDIO 服务器 | ✅ | ✅ | ✅ |
| HTTP 服务器 | ✅ | ✅（Streamable HTTP） | ✅ |
| 项目配置 | `.mcp.json` | `[mcp_servers.*]` in config.toml | ✅ |
| 工具白名单 | 无 | `enabled_tools` / `disabled_tools` | ✅ 🆕 |
| 超时控制 | 无 | `startup_timeout_sec` / `tool_timeout_sec` | ✅ 🆕 |
| 作为 MCP 服务器 | 无 | `codex mcp-server` 🆕 | ✅ |
| CLI 管理 | `claude mcp add` | `codex mcp add/get/list/remove/login/logout` | ✅ |
| 懒加载 | MCP Tool Search 自动启用 | 未明确 | ⚠️ |

### 5.5 上下文管理

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 压缩 | `/compact`（有损，不推荐） | `/compact` | ✅ |
| 清空 | `/clear` | `/clear` | ✅ |
| 自动压缩 | 无（依赖手动管理） | `model_auto_compact_token_limit` | ✅ 🆕 |
| Document-and-Clear | `/handoff` → `/clear` → `/catchup` | 无内置等价（需自建 skill） | 🚫 |
| 会话恢复 | Auto Memory 自动 | `codex resume --last` | ✅ |
| 新对话 | 重启 `claude` | `/new`（不清屏） | ✅ |
| 分支对话 | `/fork` | `/fork` | ✅ |
| 上下文可视化 | `/context`（彩色格子） | `/statusline`（状态栏） | ✅ |

---

## 6. GitHub 集成

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| GitHub Action | `anthropics/claude-code-action@v1` | `openai/codex-action@v1` | ✅ |
| PR 自动审查 | PR 创建时自动 | `@codex review` + 可配自动 | ✅ |
| Issue 实现 | `@claude implement this` | `@codex <any request>` | ✅ |
| 远程控制 | `claude remote-control`（QR 码） | 🚫 无等价 | 🚫 |
| 云任务 | 无 | `codex cloud exec` 🆕 | ✅ |
| Best-of-N | 无 | `--attempts 1-4` 🆕 | ✅ |
| 安全策略 | — | `safety-strategy: drop-sudo` | ✅ |

---

## 7. 其他特性

### 7.1 Codex 独有功能

| 功能 | 说明 | 确认 |
|------|------|------|
| 实时语音 | `/realtime` + `/audio` 语音交互 | ✅ |
| 内置搜索 | `web_search = "cached"/"live"/"disabled"` | ✅ |
| 云任务 | `codex cloud` 远程沙盒执行 | ✅ |
| 桌面应用 | `codex app`（macOS + Windows） | ✅ |
| 图片输入 | `--image` 附加截图 | ✅ |
| 性格选择 | `/personality`（friendly/pragmatic/none） | ✅ |
| Shell 直通 | `!` 前缀直接执行 shell 命令 | ✅ |
| 文件提及 | `@` 模糊搜索文件附加到对话 | ✅ |
| Shell 补全 | `codex completion bash/zsh/fish` | ✅ |
| Profiles | 命名配置集合，一键切换 | ✅ |
| 推理控制 | `model_reasoning_effort`（minimal→xhigh） | ✅ |
| MCP 服务器模式 | `codex mcp-server` 将自身暴露为 MCP 工具 | ✅ |
| 自动压缩 | `model_auto_compact_token_limit` 阈值触发 | ✅ |

### 7.2 Claude Code 独有功能

| 功能 | 说明 |
|------|------|
| 19 个 Hook 事件 | 完整的生命周期自动化（Codex 仅有通知+execpolicy） |
| `@` 引用语法 | CLAUDE.md 中拉入外部文件内容 |
| 路径感知 Markdown 规则 | `.claude/rules/*.md` 按文件路径注入上下文指令 |
| `/simplify` 三维审查 | 三个并行 agent（复用/质量/效率）并自动修复 |
| `/batch` worktree 并行 | 每个子任务在独立 worktree 中工作 |
| `/loop` 定时重复 | 定时轮询执行命令 |
| Document-and-Clear 工作流 | `/handoff` → `/clear` → `/catchup` 完整上下文管理 |
| `remote-control` | QR 码远程继续本地会话 |
| Auto Memory 全自动 | 无需手动 resume，自动恢复偏好和上下文 |
| Frontmatter Hooks | Skill 内定义作用域内的临时 Hooks |
| updatedInput | Hook 透明修改工具输入参数 |
| Plan Mode 快捷键 | `Shift+Tab` 快速切换 |

---

## 8. 模型与计费

| 维度 | Claude Code | Codex CLI | 确认 |
|------|------------|-----------|------|
| 默认模型 | Sonnet 4.6 | GPT-5.4（推测） | ⚠️ |
| 切换方式 | `/model` | `/model` 或 `--model` | ✅ |
| 推理控制 | 无（模型固有） | `model_reasoning_effort`（5 级） | ✅ |
| 推理摘要 | 无 | `model_reasoning_summary`（auto/concise/detailed/none） | ✅ |
| 快速模式 | `/fast` | 通过降低 effort 实现 | — |
| 上下文窗口 | 200k（固定） | `model_context_window` 可配 | ✅ |
| 自定义 Provider | 无 | 支持 Azure/Ollama/任意 OpenAI 兼容 API | ✅ 🆕 |

---

## 9. 指南编写优先级建议

基于对比研究，建议按以下优先级编写 Codex 指南：

### 第一优先级（核心差异，需要重新设计）

| 对应 Claude Code 文档 | Codex 指南策略 |
|----------------------|---------------|
| 02-Hooks 自动化 | **需要重新设计**：以 execpolicy + AGENTS.md 约束 + notify 为核心，补充说明 Hooks 缺失的影响和替代方案 |
| 01-CLAUDE 配置架构 | 对标编写：AGENTS.md 层级 + config.toml + memories，整体结构类似但细节全部不同 |

### 第二优先级（结构类似，内容替换）

| 对应 Claude Code 文档 | Codex 指南策略 |
|----------------------|---------------|
| 03-Skills 命令配置 | 结构相似度高：`.agents/skills/` 替换 `.claude/skills/`，补充 `$skill-installer` 生态 |
| 04-工作流最佳实践 | 部分对标：多代理/MCP/上下文管理有对应，但缺少 Plan Mode 和 Document-and-Clear 工作流 |

### 第三优先级（整合编写）

| 对应 Claude Code 文档 | Codex 指南策略 |
|----------------------|---------------|
| 00-日常使用说明 | 等前面 4 份完成后再整合编写 |

### 需要新增的内容（Claude Code 没有的）

- **沙盒安全模型**：Codex 的 OS 级沙盒是核心特性，需单独成章或融入配置指南
- **远程环境 / Web 长任务**：远程任务执行，新的工作范式
- **Profiles**：多配置集合管理
- **语音/桌面应用**：独有功能简介

---

## 10. 信息源

### 官方文档（已确认）

- [CLI Features](https://developers.openai.com/codex/cli/features)
- [Slash commands in Codex CLI](https://developers.openai.com/codex/cli/slash-commands)
- [Workflows](https://developers.openai.com/codex/workflows)
- [Subagents](https://developers.openai.com/codex/concepts/subagents)
- [Feature Maturity](https://developers.openai.com/codex/feature-maturity)
- [Hooks](https://developers.openai.com/codex/hooks)
- [Open Source](https://developers.openai.com/codex/open-source)
- [Changelog](https://developers.openai.com/codex/changelog)

### GitHub 仓库

- [openai/codex](https://github.com/openai/codex)
- [openai/codex-action](https://github.com/openai/codex-action)
- [openai/skills](https://github.com/openai/skills)
- [Codex Releases](https://github.com/openai/codex/releases)

### 公开社区/生态信号

- [openai/codex issues](https://github.com/openai/codex/issues)
- [openai/codex discussions](https://github.com/openai/codex/discussions)
- [Using skills to accelerate OSS maintenance](https://developers.openai.com/codex/open-source)

---

**下一步**: 根据优先级，逐维度深度研究并编写 Codex 指南。
