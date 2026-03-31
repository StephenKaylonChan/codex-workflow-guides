---
name: docs
description: |
  刷新当前仓库的说明文档、starter 文档或维护文档。
  适用于功能入口变化、模板契约变化、主文档漂移修正和发布前文档梳理。
argument-hint: "[范围，如 README | starter | prompts | full]"
allowed-tools: Read Write Edit Bash Glob Grep
---

按最小必要范围刷新文档，不把它做成泛化重写。

工作流：

1. 识别本次需要刷新的范围：
   - 主入口文档
   - `00-04` 核心指南
   - `templates/starter-project/`
   - `prompt-*.md`
   - `docs/development/`、`docs/release/`、`docs/reports/`
2. 先找出文档漂移或契约变化的源头。
3. 只更新与本次变化直接相关的文档层。
4. 运行与范围匹配的一致性检查。
5. 输出：
   - 刷新范围
   - 已更新文档
   - 未更新但已知相关的文档
   - 验证状态
   - 剩余漂移
   - 下一步

输出格式：

- 刷新范围
- 已更新文档
- 未更新文档
- 验证状态
- 剩余漂移
- 下一步

规则：

- 优先修现有文档，不为同一主题平行造新文档。
- 如果某个结论依赖官方能力边界，先核对再写。
- 不要把文档刷新伪装成大规模内容重写。
