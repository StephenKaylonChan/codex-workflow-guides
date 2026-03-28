# Skills 命令配置指南

> Agent Skills — Codex CLI 的可扩展工作流命令系统

**版本**: v1.2（自举与维护版）
**适用**: Codex CLI v0.115.0 附近（2026-03）
**对标**: Claude Code guides v3.14《03-Skills命令配置》

---

## 目录

1. [与 Claude Code Skills 的核心差异](#1-与-claude-code-skills-的核心差异)
2. [Skills 架构概览](#2-skills-架构概览)
3. [目录结构与发现算法](#3-目录结构与发现算法)
4. [SKILL.md 格式规范](#4-skillmd-格式规范)
5. [调用方式与参数传递](#5-调用方式与参数传递)
6. [内置 Skills](#6-内置-skills)
7. [社区生态与安装](#7-社区生态与安装)
8. [自定义 Skills — 移植指南](#8-自定义-skills--移植指南)
9. [Skill 管理与配置](#9-skill-管理与配置)
10. [安装说明](#10-安装说明)

---

## 1. 与 Claude Code Skills 的核心差异

> 如果你从 Claude Code 迁移过来，先看这节。

| 维度 | Claude Code | Codex CLI |
|------|------------|-----------|
| 目录位置 | `.claude/skills/*/SKILL.md` | `.agents/skills/*/SKILL.md` |
| 调用前缀 | `/skill-name` | **`$skill-name`** |
| 子代理执行 | `context: fork` | 无等价字段（隐式处理） |
| 模型覆盖 | `model: haiku/sonnet/opus` | 当前公开文档未见直接等价字段 |
| 动态上下文 | `` !`command` `` 内联替换 | `!command` 行前缀（非内联） |
| Skill 内 Hooks | 支持 frontmatter hooks | 当前公开文档未见直接等价 |
| 社区安装 | 无 | **`$skill-installer`** 一键安装 |
| 创建向导 | 无 | **`$skill-creator`** 交互式创建 |
| 开放标准 | Agent Skills 开放标准发起者 | 采用同一标准 |
| 渐进加载 | 全量加载 | **渐进披露**（启动时仅加载 ~100 token 元数据） |
| 可选资源 | 仅 `SKILL.md` | `SKILL.md` + `scripts/` + `references/` + `assets/` + `agents/openai.yaml` |

**最大差异**：
1. **前缀不同** — `$` 而非 `/`。这是最直觉的变化
2. **社区生态** — Codex 有完整的 Skill 安装/创建工具链和 35+ 社区精选 Skills
3. **渐进加载** — 启动时只加载 name + description（~100 tokens），Skill 激活时才加载完整内容
4. **Skill 边界更克制** — 当前公开文档里，Codex Skills 更像“按需加载的工作流指令包”，而不是携带大量运行时控制能力的迷你应用

---

## 2. Skills 架构概览

### 2.1 什么是 Skill

Skill 是一个包含 `SKILL.md` 的目录，定义了一组可复用的工作流指令。与 AGENTS.md（始终加载）不同，Skill 按需激活——只有当用户显式调用或 Codex 检测到匹配的任务时才加载。

### 2.2 Skill 目录完整结构

```
skill-name/
├── SKILL.md              # 必须 — 指令 + 元数据（YAML frontmatter + Markdown）
├── agents/
│   └── openai.yaml       # 推荐 — UI 元数据、策略配置、依赖声明
├── scripts/              # 可选 — 可执行脚本（Python/Bash/等）
├── references/           # 可选 — 参考文档（按需加载到上下文）
└── assets/               # 可选 — 模板/图片/字体（不加载到上下文）
```

| 目录 | 加载时机 | 用途 |
|------|---------|------|
| `SKILL.md` | Skill 激活时 | 核心指令和工作流定义 |
| `agents/openai.yaml` | 启动扫描时 | UI 显示信息、策略配置 |
| `scripts/` | Codex 判断需要时 | 辅助脚本（初始化、验证等） |
| `references/` | Codex 判断需要时 | 参考文档（API 文档、规范等） |
| `assets/` | 不自动加载 | 模板、图片等静态资源 |

> **渐进披露设计**：启动时只加载 name + description（~100 tokens/skill），避免大量 Skills 拖慢启动。完整 SKILL.md 内容在 Skill 实际激活时才加载。`scripts/`、`references/`、`assets/` 只在 Codex 判断需要时按需读取。

### 2.3 与 AGENTS.md 的关系

| 维度 | AGENTS.md | Skills |
|------|-----------|--------|
| 加载方式 | 每次会话自动拼接 | 按需激活 |
| 作用 | 持久性项目规范 | 可复用工作流 |
| 目录 | 项目各级目录根 | `.agents/skills/` |
| 触发 | 始终生效 | 显式 `$` 调用或隐式匹配 |
| 大小限制 | 合计 32 KiB | 单个建议 < 500 行 |

---

## 3. 目录结构与发现算法

### 3.1 扫描顺序（优先级从高到低）

```
Step 1: 当前目录（最高优先级）
  $CWD/.agents/skills/

Step 2: 父目录
  $CWD/../.agents/skills/

Step 3: 仓库根
  $REPO_ROOT/.agents/skills/

Step 4: 用户级
  ~/.agents/skills/            ← 个人跨项目 Skills
  ~/.codex/skills/             ← $skill-installer 安装位置

Step 5: 系统级
  /etc/codex/skills/           ← 管理员共享 Skills

Step 6: 内置
  Codex 内置的系统 Skills       ← skill-creator, skill-installer, openai-docs
```

**规则**：
- 同名 Skill 按优先级覆盖（项目级 > 用户级 > 系统级）
- Codex 自动检测 Skill 变更，通常无需重启（安装新 Skill 后建议重启）
- 支持符号链接（symlink）
- `name` 字段必须与目录名一致

### 3.2 Monorepo 下的 Skills 布局

```
project-root/
├── .agents/
│   └── skills/
│       ├── audit/SKILL.md         # 项目级 — 全项目通用
│       ├── deep-audit/SKILL.md
│       ├── catchup/SKILL.md
│       └── handoff/SKILL.md
│
├── apps/
│   ├── web/
│   │   └── .agents/
│   │       └── skills/
│   │           └── component-gen/SKILL.md   # 前端专属
│   └── api/
│       └── .agents/
│           └── skills/
│               └── api-test/SKILL.md        # 后端专属
│
└── ...
```

> **与 Claude Code 的区别**：Claude Code 的 Skills 主要在 `.claude/skills/` 目录；Codex 支持项目级和更高层级的 Skill 发现，同时还有 Plugins 这条单独的扩展路径。

---

## 4. SKILL.md 格式规范

### 4.1 Frontmatter 字段参考

```yaml
---
name: skill-name                      # 必填。1-64 字符，小写字母+数字+连字符
                                      # 必须与目录名一致
description: |                        # 必填。1-1024 字符
  当用户需要做 X 时使用此 Skill。
  触发关键词：X、Y、Z
allowed-tools: Read Grep Bash         # 可选。空格分隔的预授权工具列表
                                      # 实验性功能
argument-hint: "[--quick | --full]"   # 可选。参数提示，显示在 UI 中
license: MIT                          # 可选。许可证
compatibility: "Requires git, pnpm"   # 可选。环境要求（最多 500 字符）
metadata:                             # 可选。任意键值对
  author: "your-name"
  version: "1.0.0"
---
```

### 4.2 字段详解

**`name`（必填）**：
- 小写字母、数字、连字符
- 不能以连字符开头或结尾，不能连续连字符
- 必须与父目录名一致（目录名 = Skill 名）
- 示例：`audit`、`deep-audit`、`gh-fix-ci`

**`description`（必填）**：
- 控制 Codex 是否会**隐式（自动）激活** Skill
- 写得越具体，自动触发越准确
- 必须包含"何时使用"的说明
- 所有触发条件都应写在 description 中，不要写在正文里

**`allowed-tools`（实验性）**：
- 格式：`Bash(git:*) Bash(jq:*) Read`
- 预授权 Skill 内可用的工具，跳过交互式审批
- 仍处于实验阶段

**`argument-hint`**：
- 显示在 Skill 浏览器的名称后面
- 纯 UI 提示，不影响实际参数解析
- 示例：`"[file] [priority]"`、`"[--quick | --full | --security]"`

### 4.3 agents/openai.yaml 扩展配置

```yaml
# agents/openai.yaml — UI 元数据和策略配置

interface:
  display_name: "项目健康检查"           # UI 显示名称
  short_description: "扫描代码质量和依赖安全"
  icon_small: "./assets/icon-sm.svg"    # 小图标
  icon_large: "./assets/icon-lg.png"    # 大图标
  brand_color: "#3B82F6"                # 品牌色
  default_prompt: "执行标准模式检查"      # 默认提示（无参数时使用）

policy:
  allow_implicit_invocation: true       # 是否允许隐式激活（默认 true）

dependencies:
  tools:
    - type: "mcp"
      value: "some-mcp-tool"
      description: "用途说明"
      transport: "streamable_http"
      url: "https://api.example.com/mcp"
```

**`allow_implicit_invocation`**：
- `true`（默认）：Codex 可以根据用户任务自动匹配并激活 Skill
- `false`：仅 `$skill-name` 显式调用时才激活
- 对于只想手动触发的 Skill，设为 `false`

> **与 Claude Code 的对比**：Claude Code 用 `disable-model-invocation: true` 禁止自动触发。Codex 用 `agents/openai.yaml` 中的 `allow_implicit_invocation: false`（注意：字段名和位置不同）。

### 4.4 SKILL.md 正文编写指南

基于 `$skill-creator` 的最佳实践：

1. **保持 500 行以内** — 超出会稀释效果
2. **只写 Codex 不知道的信息** — 不要重复通用编程知识
3. **用祈使句** — "Run X", "Check Y", "Do not Z"
4. **匹配任务脆弱性** — 高风险操作（部署、删除）详细约束；灵活任务给足自由度
5. **不要创建辅助文档** — 不需要 README.md、CHANGELOG.md 等

---

## 5. 调用方式与参数传递

### 5.1 三种调用方式

| 方式 | 语法 | 说明 |
|------|------|------|
| 显式调用 | `$skill-name` | 最常用，直接在输入框中使用 |
| 浏览器选择 | `/skills` | 打开 Skill 浏览器，查看所有可用 Skills |
| 隐式激活 | 自然语言 | Codex 根据 `description` 自动匹配并激活 |

> **与 Claude Code 的区别**：Claude Code 用 `/skill-name`（斜杠前缀），Codex 用 `$skill-name`（美元符前缀）。

### 5.2 位置参数

当 SKILL.md 正文中包含 `$1`、`$2` ... `$9` 或 `$ARGUMENTS` 时，启用位置参数模式：

```yaml
---
name: review-file
description: "审查指定文件的代码质量"
argument-hint: "[file] [priority]"
---

审查文件 $1，优先级为 $2。

完整参数：$ARGUMENTS
```

调用示例：
```
$review-file main.py high
```

展开结果：
```
审查文件 main.py，优先级为 high。

完整参数：main.py high
```

| 变量 | 含义 |
|------|------|
| `$1` ~ `$9` | 第 N 个位置参数（shlex 分词，支持引号） |
| `$ARGUMENTS` | 所有参数拼接（空格分隔） |
| `$$` | 转义，输出字面 `$` 字符 |

### 5.3 命名参数

当 SKILL.md 正文中包含 `$VARNAME`（全大写，非 `$ARGUMENTS`）时，启用命名参数模式：

```yaml
---
name: deploy
description: "部署到指定环境"
argument-hint: "ENV=staging BRANCH=main"
---

部署分支 $BRANCH 到环境 $ENV。
```

调用示例：
```
$deploy ENV=production BRANCH=release/v2
```

- 参数格式：`KEY=VALUE`（支持引号：`USER="Alice Smith"`）
- 缺少必需参数时，Codex 会提示需要哪些参数
- `$$VARNAME` 输出字面值（转义）

### 5.4 动态上下文

Codex 支持 `!` 前缀执行 shell 命令，但与 Claude Code 的机制不同：

| 特性 | Claude Code | Codex |
|------|------------|-------|
| 语法 | `` !`git status` ``（反引号包裹，内联） | `!git status`（行前缀，整行） |
| 位置 | SKILL.md 正文任意位置 | 用户输入框 |
| 替换 | 命令输出替换到提示中 | 命令输出作为上下文追加 |

> **注意**：Codex 的 `!` 前缀是在用户输入层面执行 shell 命令，不是 SKILL.md 内的模板替换。如果需要在 Skill 执行前获取动态信息，在 SKILL.md 中用指令要求 Codex 主动执行命令（如 "First run `git status --short` to understand current state"）。

---

## 6. 内置 Skills

### 6.1 系统 Skills（随 Codex 安装）

Codex 内置 3 个系统 Skill，无需安装：

| Skill | 调用 | 用途 |
|-------|------|------|
| **skill-creator** | `$skill-creator` | 交互式创建新 Skill |
| **skill-installer** | `$skill-installer` | 从 GitHub 安装社区 Skills |
| **openai-docs** | 隐式激活 | 查询 OpenAI API/产品文档时自动触发 |

### 6.2 `$skill-creator` 工作流

交互式 6 步创建流程：

```
Step 1: 理解需求
  → 询问 Skill 用途、触发时机、使用示例

Step 2: 规划资源
  → 分析需要哪些 scripts/、references/、assets/

Step 3: 初始化目录
  → 运行 init_skill.py 创建目录结构

Step 4: 编写内容
  → 实现 SKILL.md、脚本和资源文件

Step 5: 验证
  → 运行 quick_validate.py 检查格式和命名

Step 6: 迭代
  → 实际测试并完善
```

使用方式：
```
$skill-creator

# 或带描述
$skill-creator 创建一个自动生成 API 文档的 Skill
```

### 6.3 `$skill-installer` 使用

```bash
# 按名称安装（精选 Skills）
$skill-installer gh-fix-ci

# 安装实验性 Skill
$skill-installer install the create-plan skill from the .experimental folder

# 按 URL 安装
$skill-installer install https://github.com/openai/skills/tree/main/skills/.curated/playwright

# 安装后重启 Codex 以加载新 Skill
```

安装位置：`~/.codex/skills/`（用户级，所有项目可用）

### 6.4 内置斜杠命令（非 Skills）

以下是 Codex 内置的**斜杠命令**，不属于 Skills 系统：

| 命令 | 用途 |
|------|------|
| `/review` | 代码审查预设（启动专用审查代理） |
| `/clear` | 清空上下文 |
| `/compact` | 压缩上下文 |
| `/model` | 切换模型 |
| `/permissions` | 运行时切换审批模式 |
| `/personality` | 切换沟通风格 |
| `/skills` | 浏览所有可用 Skills |
| `/diff` | 查看代码变更 |
| `/fork` | 分支对话 |
| `/new` | 新对话（不清屏） |
| `/resume` | 恢复保存的对话 |
| `/init` | 生成 AGENTS.md |
| `/mcp` | MCP 管理 |
| `/experimental` | 切换实验功能 |
| `/agent` | 切换代理线程 |
| `/mention` | 附加文件 |
| `/statusline` | 自定义状态栏 |
| `/debug-config` | 配置诊断 |

> **与 Claude Code 的区别**：Claude Code 的 `/simplify`、`/batch` 是 Anthropic 维护的 Bundled Skills。Codex 的 `/review` 等是内置斜杠命令（非 Skills 系统），不在 `.agents/skills/` 中，也无法自定义。

---

## 7. 社区生态与安装

### 7.1 Agent Skills 开放标准

Skills 格式基于 **Agent Skills 开放标准**（`agentskills.io/specification`），已被 30+ 代理产品采用，包括 Claude Code、Cursor、GitHub Copilot、VS Code、Gemini CLI、Roo Code 等。这意味着同一个 Skill 可以跨工具使用。

### 7.2 openai/skills 仓库

GitHub 仓库 [`openai/skills`](https://github.com/openai/skills) 是官方 Skill 仓库：

| 分类 | 目录 | 数量 | 说明 |
|------|------|------|------|
| 系统级 | `.system/` | 3 | 随 Codex 安装（skill-creator/installer/openai-docs） |
| 精选级 | `.curated/` | 35 | 社区贡献、官方审核，可按名称安装 |
| 实验级 | `.experimental/` | 若干 | 测试阶段，功能可能不稳定 |

### 7.3 精选 Skills 速查

| Skill | 用途 | 适合场景 |
|-------|------|---------|
| `gh-fix-ci` | 修复 GitHub CI 失败 | CI 红了时 |
| `gh-address-comments` | 处理 PR 审查评论 | 代码审查后 |
| `playwright` | Playwright 端到端测试 | 前端测试 |
| `playwright-interactive` | 交互式 Playwright 调试 | 前端调试 |
| `security-best-practices` | 安全最佳实践检查 | 安全审查 |
| `security-threat-model` | 威胁模型分析 | 安全评估 |
| `security-ownership-map` | 安全责任映射 | 组织安全 |
| `doc` | 文档生成 | 写文档 |
| `pdf` | PDF 处理 | PDF 操作 |
| `spreadsheet` | 电子表格处理 | 数据处理 |
| `slides` | 幻灯片生成 | 演示文稿 |
| `jupyter-notebook` | Jupyter 笔记本操作 | 数据科学 |
| `screenshot` | 截图处理 | UI 反馈 |
| `figma` | Figma 设计稿读取 | 设计还原 |
| `figma-implement-design` | 将 Figma 设计实现为代码 | 设计转代码 |
| `linear` | Linear 项目管理集成 | 任务管理 |
| `notion-*`（4 个） | Notion 集成系列 | 知识管理 |
| `sentry` | Sentry 错误追踪集成 | 错误排查 |
| `vercel-deploy` | Vercel 部署 | 前端部署 |
| `netlify-deploy` | Netlify 部署 | 前端部署 |
| `cloudflare-deploy` | Cloudflare 部署 | 边缘部署 |
| `render-deploy` | Render 部署 | 后端部署 |
| `imagegen` | 图片生成 | AI 绘图 |
| `sora` | Sora 视频生成 | AI 视频 |
| `speech` | 语音合成 | TTS |
| `transcribe` | 语音转文字 | STT |
| `chatgpt-apps` | ChatGPT 应用构建 | AI 应用 |
| `develop-web-game` | Web 游戏开发 | 游戏开发 |
| `winui-app` | WinUI 应用开发 | Windows 应用 |
| `aspnet-core` | ASP.NET Core 开发 | .NET 后端 |
| `openai-docs` | OpenAI 文档查询 | API 开发 |
| `yeet` | 快速发布（实验性） | 发布流程 |

### 7.4 安装精选 Skill

```bash
# 方式 1：按名称（最简单）
$skill-installer playwright

# 方式 2：批量安装
$skill-installer install gh-fix-ci, gh-address-comments, security-best-practices

# 方式 3：从 URL
$skill-installer install https://github.com/openai/skills/tree/main/skills/.curated/sentry

# 安装到项目级（而非用户级）
# 手动 clone 或复制到 .agents/skills/
```

> **注意**：`$skill-installer` 默认安装到 `~/.codex/skills/`（用户级）。如果想让 Skill 只对当前项目可用，手动复制到 `.agents/skills/`。

---

## 8. 自定义 Skills — 移植指南

> 将 Claude Code 指南中的 6 个自定义 Skills 移植到 Codex 格式。

### 移植注意事项

| 变化点 | Claude Code 原版 | Codex 版本 |
|--------|----------------|-----------|
| 目录位置 | `.claude/skills/` | `.agents/skills/` |
| frontmatter 字段 | `disable-model-invocation` | `agents/openai.yaml` → `allow_implicit_invocation: false` |
| `context: fork` | 子代理执行 | 移除（Codex 隐式处理） |
| `model: haiku` | 模型覆盖 | 移除（无等价功能） |
| 引用 CLAUDE.md | 多处提及 | 替换为 AGENTS.md |
| `/simplify` | Bundled Skill | 替换为 `/review` 或手动审查 |
| Auto Memory | 自动恢复 | 替换为 `codex resume --last` |
| Hook 依赖 | SessionStart/Stop/PostToolUse | 替换为 AGENTS.md 指令 |

---

### 8.1 $audit — 项目健康检查

**文件路径**: `.agents/skills/audit/SKILL.md`

````markdown
---
name: audit
description: |
  项目健康检查。当需要检查代码质量、依赖安全、文档同步状态时使用。
  触发关键词：健康检查、audit、代码质量检查、依赖检查
argument-hint: "[--quick | --full | --security | --docs]"
allowed-tools: Read Bash Grep Glob
---

<task>
对项目进行健康检查，根据参数决定检查深度。
</task>

<workflow>

## Step 0: 获取基本信息

```bash
echo "=== 项目审计 $(date '+%Y-%m-%d %H:%M') ==="
echo "--- 最近 5 个 commit ---"
git log --oneline -5
echo "--- 未提交文件 ---"
git status --short | head -20
```

## Step 1: 解析参数

| 参数 | 检查范围 | 适用场景 |
|------|---------|---------|
| `--quick` | Git 状态 + AGENTS.md 大小 | 每天快速检查 |
| 无参数 | 代码质量 + 依赖 + 文档同步 | 每周常规 |
| `--full` | 全部 + 构建测试 | 大版本发布前 |
| `--security` | 安全漏洞 + 敏感信息扫描 | 上线前 |
| `--docs` | 文档与代码同步深度检查 | Phase 完成后 |

## Step 2: Quick 模式检查

```bash
# AGENTS.md 大小（超过 32 KiB 会被截断）
wc -c AGENTS.md 2>/dev/null || echo "AGENTS.md 不存在"

# 未提交文件数量
git status --short | wc -l
```

## Step 3: 标准模式检查（无参数）

**代码质量**：
```bash
# ESLint 检查
pnpm lint 2>&1 | tail -5

# TODO/FIXME 统计
grep -r "TODO\|FIXME\|HACK\|XXX" apps/ --include="*.ts" --include="*.tsx" --include="*.py" | wc -l
```

**依赖健康**：
```bash
# 过时依赖
pnpm outdated 2>/dev/null | head -20

# 安全漏洞
pnpm audit --audit-level=high 2>&1 | tail -10
```

**文档同步**：
- AGENTS.md 是否在 32 KiB 以内？
- 技术栈版本是否与 package.json 一致？
- `.codex/rules/` 中的规则是否仍然有效？
- `docs/roadmap/` 是否存在？当前 Phase 文件是否准确？
- `docs/specs/` 中是否有状态为"实施中"超过 2 周且无相关 commit 的 spec？

## Step 4: Full 模式额外检查（--full）

```bash
# 前端构建
pnpm build:web 2>&1 | tail -5

# 测试覆盖率
pnpm test --coverage 2>&1 | tail -10
```

## Step 5: Security 模式（--security）

```bash
# 扫描硬编码密钥
grep -r "password\|secret\|api_key\|token" apps/ --include="*.ts" --include="*.py" \
  -i | grep -v "test\|spec\|example\|.env" | head -10

# .env 是否在 .gitignore
grep "^\.env" .gitignore || echo "WARNING: .env 可能未被忽略"
```

## Step 6: 输出审计报告

```
## 项目审计报告

**时间**: [当前时间]
**模式**: [quick/标准/full/security/docs]

### 总览
| 维度 | 状态 | 说明 |
|------|------|------|
| 代码质量 | OK/WARN/FAIL | [ESLint errors/warnings 数量] |
| 依赖健康 | OK/WARN/FAIL | [过时依赖数] |
| 文档同步 | OK/WARN/FAIL | [AGENTS.md 大小] |
| Git 状态 | OK/WARN/FAIL | [未提交文件数] |

### 需要立即处理
[列出 Critical 问题]

### 建议本周处理
[列出 Warning 问题]

### 行动建议（优先级排序）
1. [最重要的问题]
2. [次要问题]
```

</workflow>
````

**策略配置**（禁止隐式触发）：

```yaml
# .agents/skills/audit/agents/openai.yaml
policy:
  allow_implicit_invocation: false
```

---

### 8.2 $deep-audit — 全面深度审计

**文件路径**: `.agents/skills/deep-audit/SKILL.md`

````markdown
---
name: deep-audit
description: |
  全面深度审计，逐文件检查代码与文档一致性，自动修复并提交。
  Phase 完成后、大版本发布前使用。
argument-hint: "[--no-fix | --no-push]"
allowed-tools: Read Write Edit Bash Glob Grep
---

<task>
执行全面深度审计：扫描所有代码文件和文档，识别不一致，自动修复，提交变更。

**默认行为**：审计 → 修复 → 提交
**参数**：
- `--no-fix`: 仅审计报告，不修复
- `--no-push`: 修复并提交，但不推送
</task>

<workflow>

## Step 0: 初始化

```bash
AUDIT_DATE=$(date +%Y-%m-%d)
AUDIT_TIME=$(date +%H:%M)
echo "=== 深度审计开始 $AUDIT_DATE $AUDIT_TIME ==="
```

## Step 1: 代码结构全面扫描

扫描所有源文件，记录实际状态：

```bash
# 前端文件统计
find apps/web/src -name "*.tsx" -o -name "*.ts" | wc -l
find apps/web/src/components -name "*.tsx" | sort

# 后端文件统计
find apps/api -name "*.py" | wc -l

# 所有文档文件
find . -name "*.md" -not -path "*/node_modules/*" | sort
```

## Step 2: 文档系统检查

逐一读取并验证：

- `AGENTS.md`：大小是否 < 32 KiB？内容是否准确？
- `.codex/rules/`：规则是否仍然有效？是否有需要更新的命令？
- `docs/roadmap/`：各 Phase 功能描述是否准确反映代码现状？
- `docs/specs/`：各 spec 的设计描述是否仍然准确？状态是否正确？
- `docs/architecture/`：ADR 是否反映实际决策？
- `docs/development/`：API 文档是否与代码同步？

## Step 3: 对比分析

**组件 vs 文档**：
- 实际组件数 vs 文档记录数
- 找出文档缺失的组件

**API vs 文档**：
- 实际 API 端点（扫描 router 文件）vs api.md 记录
- 找出文档缺失的端点

**package.json vs AGENTS.md**：
- 实际依赖版本 vs AGENTS.md 声明的版本

## Step 4: 识别问题，按优先级分类

```
P0 - 严重（立即修复）:
1. AGENTS.md 内容与代码不符（会让 Codex 理解错误）
2. 安全漏洞或硬编码密钥

P1 - 中等（今日修复）:
1. 文档缺失的组件/API
2. 版本声明不一致

P2 - 轻微（本周修复）:
1. 冗余文档内容
2. 过期的注释
```

## Step 5: 生成审计报告

写入 `docs/reports/deep-audit-$AUDIT_DATE.md`

## Step 6: 执行修复（除非 --no-fix）

按 P0 → P1 → P2 顺序修复：
- 更新 AGENTS.md
- 更新 .codex/rules/
- 更新 docs/ 各文档

## Step 7: 提交（除非 --no-push）

```bash
git add .
git commit -m "docs: 深度审计与自动优化 $AUDIT_DATE

- 修复 P0 问题: [数量] 处
- 修复 P1 问题: [数量] 处
- 生成审计报告: docs/reports/deep-audit-$AUDIT_DATE.md"

# 如果没有 --no-push 参数
git push
```

## Step 8: 输出完成报告

```
====================================
深度审计完成
====================================

扫描统计:
  代码文件: [X] 个
  文档文件: [X] 个
  检查项目: [X] 项

修复统计:
  P0 严重: [X] 处
  P1 中等: [X] 处
  P2 轻微: [X] 处

报告: docs/reports/deep-audit-$AUDIT_DATE.md
下次建议: 下一个 Phase 完成后
====================================
```

</workflow>
````

```yaml
# .agents/skills/deep-audit/agents/openai.yaml
policy:
  allow_implicit_invocation: false
```

---

### 8.3 $catchup — 上下文快速恢复

**用途**：清空上下文后（`/clear`）或新会话开始时，快速重建工作状态。

> **与 Claude Code 的差异**：Claude Code 有 Auto Memory 自动恢复；Codex 需要手动 `$catchup` 或 `codex resume --last`。如果项目把 roadmap/spec/session-notes 维护得足够好，这个差距会显著缩小。

**文件路径**: `.agents/skills/catchup/SKILL.md`

````markdown
---
name: catchup
description: |
  快速恢复工作上下文。当用户说"重新开始"、"清空后需要继续"、"帮我恢复上下文"时使用。
  也适用于新会话开始时快速了解项目现状。
argument-hint: ""
allowed-tools: Read Bash Glob
---

<task>
快速恢复工作上下文，让我们继续之前的工作。
</task>

<workflow>

## Step 0: 获取当前状态

```bash
echo "=== 当前状态 $(date '+%Y-%m-%d %H:%M') ==="
git log --oneline -5
echo "--- 修改的文件 ---"
git status --short
echo "--- 未推送 commit ---"
git log --oneline @{u}.. 2>/dev/null || echo "（无法获取，可能没有追踪分支）"
```

## Step 1: 读取关键文件

依次读取（按重要性）：
1. `AGENTS.md`（项目规范）
2. `.claude/session-notes.md` 或 `.agents/session-notes.md`（如果存在，是压缩前保存的进度）
3. `docs/roadmap/README.md`（如果存在，项目整体进度）
4. 当前 Phase 文件（从 README.md 中确定当前 Phase，读取对应文件）
5. `docs/specs/` 中状态为"实施中"或"已确认"的 spec 文件
6. 最近修改的源文件（`git diff HEAD~3 --name-only` 列出的文件）

## Step 2: 输出恢复摘要

```
上下文已恢复

## 项目: [项目名称]
**技术栈**: [从 AGENTS.md 获取]

## 项目路线图
[当前 Phase 名称及进度，如："Phase 2 核心业务 3/5"]
[列出当前 Phase 中进行中和待办的条目]

## 当前设计文档
[如有状态为"实施中"或"已确认"的 spec，列出文件名和概要]

## 最近工作
[最近 5 个 commit 摘要]

## 修改中的文件
[git status --short 输出]

## 下一步建议
[根据 session-notes.md + 路线图当前 Phase 的待办项推断]

---
**准备好继续了。我们从哪里开始？**
```

</workflow>
````

---

### 8.4 $handoff — 会话交接文档

**用途**：关闭会话前生成结构化交接笔记，供下次会话通过 `$catchup` 或 `codex resume --last` 恢复。

> **与 Claude Code 的差异**：Claude Code 有 Auto Memory + Stop Hook 自动记录进度。Codex 需要手动执行 `$handoff`。建议养成习惯——每次长会话结束前执行。

**文件路径**: `.agents/skills/handoff/SKILL.md`

````markdown
---
name: handoff
description: |
  会话结束前生成交接文档。当用户说"生成交接文档"、"我要关闭了"、"记录一下进度"时使用。
argument-hint: ""
allowed-tools: Read Write Edit Bash Glob Grep
---

<task>
提交当前所有变更，更新项目路线图进度，然后生成结构化会话交接文档。
</task>

<workflow>

## Step 0: 收集当前状态

```bash
echo "=== 会话信息 ==="
date '+%Y-%m-%d %H:%M'
git log --oneline -10
git status --short
git diff --stat HEAD 2>/dev/null | tail -5
```

## Step 1: 提交当前变更

检查是否有未提交的变更（`git status --short` 有输出）。

**如果有未提交文件**：

1. 精确 stage 变更文件（不用 `git add .`）：
   - 读取 `git status --short` 输出，识别已修改和新增文件
   - 排除 `.env`、`*.log`、`node_modules/`、构建产物等
   - 执行 `git add <具体文件列表>`

2. 分析变更内容，生成符合 Conventional Commits 规范的 commit message，尝试正常提交：
   ```bash
   git commit -m "<type>(<scope>): <subject>"
   ```

3. 根据结果：
   - **成功** → 记录"正常 commit: `<message>`"，继续 Step 2
   - **失败（测试不通过）** → 降级为 WIP 提交：
     ```bash
     git commit --no-verify -m "wip: <简要描述当前开发状态>"
     ```
     记录"WIP commit: `wip: <描述>`"，继续 Step 2

**如果没有未提交文件** → 记录"无变更"，直接跳到 Step 2。

## Step 2: 更新项目路线图

检查 `docs/roadmap/` 目录是否存在。

**如果存在**：

1. 读取 `docs/roadmap/README.md` 确定当前 Phase
2. 读取当前 Phase 文件
3. 根据本次会话完成的工作，**仅更新 checkbox 状态**：
   - 已完成：`[ ]` → `[x] YYYY-MM-DD`
   - 进行中：`[ ]` → `[-] YYYY-MM-DD`
4. 更新 README.md 中的进度统计

**重要**：只更新状态，不添加新功能条目。

**提交文档更新**：
```bash
git add docs/roadmap/ docs/specs/ 2>/dev/null
git commit -m "docs: 更新路线图和设计文档状态" 2>/dev/null || true
```

## Step 3: 生成交接文档

写入 `.agents/session-notes.md`：

```markdown
# 会话交接文档

**生成时间**: [当前时间]
**当前分支**: [git branch --show-current]

## 本次会话完成的工作

[总结这次会话完成了什么]

## 关键技术决策

[记录做了什么重要决策，为什么这样决定]

## 代码变更摘要

[git diff --stat HEAD 输出]

## 路线图进度

[当前 Phase 名称及进度]

## 遗留问题 / 下次继续

[还没完成什么，下次从哪里接手]

## 注意事项

[有什么需要特别注意的]

---
*下次会话运行 `$catchup` 恢复此上下文*
```

## Step 4: 确认

```
交接完成

提交状态: [正常 commit / WIP commit / 无变更]
路线图更新: [已更新 Phase X: N/M | 无 roadmap 目录]
交接文档: .agents/session-notes.md

下次会话运行 $catchup 或 codex resume --last 可恢复上下文。
```

</workflow>
````

---

### 8.5 $spec — 讨论成果整理为设计文档

**文件路径**: `.agents/skills/spec/SKILL.md`

````markdown
---
name: spec
description: |
  将讨论成果整理为结构化设计文档。当需求讨论、技术方案探讨到一定程度时使用。
  触发关键词：整理讨论、写 spec、保存设计、记录方案
argument-hint: "[功能名称]"
allowed-tools: Read Write Edit Bash Glob Grep
---

<task>
将当前对话中的讨论成果整理为结构化设计文档，写入 docs/specs/ 目录。
如果目标文件已存在，执行增量更新（合并新内容，保留已有内容）。
</task>

<workflow>

## Step 0: 确定文件名和模式

- 如果提供了参数（如 `$spec user-auth`），用参数作为文件名（kebab-case）
- 如果没有参数，根据讨论主题自动命名
- 检查 `docs/specs/<name>.md` 是否已存在：
  - **已存在** → 增量更新模式
  - **不存在** → 新建模式

```bash
mkdir -p docs/specs
```

## Step 1: 提取讨论成果

回顾当前对话，提取（按需包含，不强制全部有）：

- 功能背景与目标
- 需求要点和验收标准
- 讨论过的方案及取舍理由
- 最终确定的设计方案
- UI/交互设计细节
- API 设计
- 数据模型设计
- 业务逻辑和处理流程
- 调研发现
- 约束条件和注意事项
- 实施建议

## Step 2: 写入/更新 Spec 文件

**新建模式** — 写入 `docs/specs/<name>.md`：

```markdown
---
title: [功能名称]
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
phase: phase-N
---

# [功能名称] 设计文档

## 背景与目标
[为什么要做这个功能]

## 需求概要
[核心功能点，验收标准]

## 设计方案

### 讨论过的方案
[方案 A vs B，优缺点，选择理由]

### 最终方案
[确定的方案]

## 详细设计
（以下模块按需包含）

### UI/交互设计
### API 设计
### 数据模型
### 业务逻辑

## 约束与注意事项

## 实施备注
```

**增量更新模式**：
- 保留已有内容不删除
- 新增内容融入对应章节
- 更新 `updated` 日期

## Step 3: 判断状态

| status | 含义 | 转换时机 |
|--------|------|---------|
| `draft` | 讨论中 | 首次生成 |
| `approved` | 方案已确认 | 用户确认 |
| `implementing` | 实施中 | 开始编码时 |
| `implemented` | 已完成 | 功能完成 |

## Step 4: 输出确认

```
Spec 已生成/更新

文件：docs/specs/<name>.md
状态：草稿 / 已确认
内容：需求 [N] 项、设计方案 [N] 个、...

建议下一步：
- 继续讨论 → 讨论后再次 $spec 更新
- 开始实施 → /clear 后 "读取 docs/specs/<name>.md，开始实施"
- 确认内容 → 告诉我"确认"
```

</workflow>
````

```yaml
# .agents/skills/spec/agents/openai.yaml
policy:
  allow_implicit_invocation: false
```

---

### 8.6 $done — 功能完成收尾

**文件路径**: `.agents/skills/done/SKILL.md`

````markdown
---
name: done
description: |
  功能完成收尾检查。验证代码质量，同步文档状态（Roadmap + Spec），确认无遗漏。
  触发关键词：功能完成、收尾检查、done、wrap up
allowed-tools: Read Write Edit Bash Glob Grep
---

<task>
对刚完成的功能执行完整的收尾检查：代码验证 + 文档同步 + 状态汇总。
</task>

<workflow>

## Step 0: 识别刚完成的功能

- 查看最近的 git log 和 diff，确定刚完成了什么功能
- 如果用户提供了功能名称，以用户说明为准

```bash
git log --oneline -5
git diff --stat HEAD~3
```

## Step 1: 代码验证

运行项目的测试和 lint 命令：

```bash
pnpm test
pnpm lint
```

检查项：
- [ ] 所有测试通过
- [ ] Lint 无 error
- [ ] 边界条件已考虑
- [ ] 改动不影响现有功能

## Step 2: Roadmap 更新

如果 `docs/roadmap/` 存在：
- 找到当前功能对应的 Phase 文件
- 将 checkbox 从 `- [ ]` 改为 `- [x] YYYY-MM-DD`
- 更新 `docs/roadmap/README.md` 中的进度统计

## Step 3: Spec 状态更新

如果 `docs/specs/` 存在：
- 找到关联的 spec 文件
- 将 `status` 从 `implementing` 更新为 `implemented`
- 更新 `updated` 日期

## Step 4: 代码审查

运行 `/review` 对本次改动做审查（如果本次还未运行过）。

## Step 5: 提交文档变更

```bash
git add docs/roadmap/ docs/specs/
git commit -m "docs: 更新 [功能名] 的 roadmap 和 spec 状态"
```

## Step 6: 输出状态汇总

```
功能收尾完成

功能：[功能名称]
代码验证：测试通过 | Lint 通过
Roadmap：Phase N — [条目] 已勾选 / 无关联条目
Spec：[spec名].md → implemented / 无关联 Spec
代码审查：/review 已执行 / 之前已执行

下一步建议：
- 继续下一个功能
- git push 推送到远程
- $deep-audit（如果当前 Phase 接近完成）
```

</workflow>
````

```yaml
# .agents/skills/done/agents/openai.yaml
policy:
  allow_implicit_invocation: false
```

---

## 9. Skill 管理与配置

### 9.1 查看已安装的 Skills

```bash
/skills              # 打开 Skill 浏览器，查看所有可用 Skills
```

### 9.2 禁用 Skill

在 `~/.codex/config.toml` 或 `.codex/config.toml` 中：

```toml
[[skills.config]]
path = "/Users/me/.codex/skills/some-skill/SKILL.md"
enabled = false
```

### 9.3 调试 Skill 加载

```bash
/debug-config        # 查看完整配置诊断，包括 Skills 加载情况
```

### 9.4 Skill 优先级冲突

同名 Skill 存在于多个作用域时，按发现顺序（CWD → 仓库根 → 用户 → 系统）取第一个匹配。要覆盖系统 Skill，在项目级创建同名 Skill 即可。

---

## 10. 安装说明

### 10.1 创建目录结构

```bash
# 项目级 Skills
mkdir -p .agents/skills/audit
mkdir -p .agents/skills/deep-audit
mkdir -p .agents/skills/catchup
mkdir -p .agents/skills/handoff
mkdir -p .agents/skills/spec
mkdir -p .agents/skills/done

# 需要禁止隐式触发的 Skills 还需创建 agents/openai.yaml
mkdir -p .agents/skills/audit/agents
mkdir -p .agents/skills/deep-audit/agents
mkdir -p .agents/skills/spec/agents
mkdir -p .agents/skills/done/agents
```

如果你不想从文档里手动复制，优先直接参考：

`/Users/stephen/Downloads/00_project/guides-codex/templates/starter-project/.agents/skills/`

### 10.2 文件创建

将上述各 Skill 内容分别写入对应的 `SKILL.md` 文件，以及需要的 `agents/openai.yaml` 文件。

### 10.3 验证安装

```bash
# 启动 Codex
codex

# 查看 Skills
/skills

# 测试调用
$audit --quick
```

### 10.4 使用方式

```bash
$audit              # 标准健康检查
$audit --quick      # 快速检查
$audit --security   # 安全扫描（上线前）
$audit --full       # 完整检查（大版本后）

$deep-audit         # 全面深度审计（Phase 完成后）
$deep-audit --no-fix    # 仅生成报告

$catchup            # 清空上下文后恢复
$handoff            # 会话结束前生成交接文档

$spec               # 将讨论成果整理为设计文档
$spec user-auth     # 指定功能名称

$done               # 功能完成收尾检查
```

### 10.5 推荐安装的社区 Skills

根据项目需求，考虑安装以下社区精选 Skills：

```bash
# CI/CD 相关
$skill-installer gh-fix-ci
$skill-installer gh-address-comments

# 安全相关
$skill-installer security-best-practices

# 测试相关
$skill-installer playwright

# 文档相关
$skill-installer doc
```

---

## 附录：Claude Code Skills → Codex Skills 快速对照

| Claude Code | Codex | 变化说明 |
|------------|-------|---------|
| `.claude/skills/` | `.agents/skills/` | 目录名变化 |
| `/skill-name` | `$skill-name` | 前缀变化 |
| `disable-model-invocation: true` | `agents/openai.yaml` → `allow_implicit_invocation: false` | 字段位置和名称变化 |
| `context: fork` | 移除 | Codex 无等价功能 |
| `model: haiku` | 移除 | Codex 无等价功能 |
| `user-invocable: false` | 无直接等价 | — |
| `` !`command` `` | `!command`（行前缀） | 语法不同，非内联替换 |
| `/simplify` Bundled Skill | `/review` 内置命令 | 功能不完全等价 |
| `/batch` Bundled Skill | 无直接等价 | — |
| Auto Memory 自动恢复 | `codex resume --last` + `$catchup` | 需手动操作 |

---

## 11. 当前仓库的 Skill 取舍说明

对 `guides-codex` 这个仓库本身，当前默认只保留基础六件套：

- `audit`
- `deep-audit`
- `catchup`
- `handoff`
- `spec`
- `done`

**当前不额外增加专门的 maintenance / release Skill**。原因不是这类 Skill 永远没价值，而是目前这类动作的频率还不高，直接用：

- `$deep-audit`
- roadmap / spec / session-notes
- `docs/release/README.md`
- `docs/reports/release-note-template.md`

这套组合已经足够，而且更透明。

如果未来出现下面任一情况，再考虑抽出专门 Skill：

- 版本整理动作明显高频
- 发布记录格式开始反复手工重复
- 阶段收尾需要稳定复用一套固定报告

---

**版本**: v1.2（自举与维护版）
**更新日期**: 2026-03-28
