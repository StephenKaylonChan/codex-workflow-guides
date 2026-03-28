# Phase 2 Starter 漂移审计

## 检查范围

- `templates/starter-project/README.md`
- `templates/starter-project/AGENTS.md`
- `templates/starter-project/.agents/skills/`
- `README.md`
- `prompt-新项目初始化.md`

## 未运行的检查

- 本仓库不存在应用级运行测试
- 本轮没有把模板拿到另一个真实项目里再次演练

## 严重发现

- 无

## 中等级发现

- `templates/starter-project/AGENTS.md` 仍然偏向应用/monorepo 假设，与后来形成的“同样可接文档、脚本、工具仓库”的定位不完全一致

## 低优先级发现

- starter 与自举仓库里的基础 Skills 已开始共享输出协议，但还需要更多真实使用来验证文案是否足够稳

## 已修复项

- 已把 `templates/starter-project/AGENTS.md` 泛化，不再默认假设 Web/API monorepo
- starter Rules 继续保留为显式示例，但 starter README 与初始化 prompt 已明确要求项目级替换

## 剩余风险

- starter 仍然会保留占位命令，所以如果用户复制后不改，仍然可能误用
- 如果后续真实使用进一步分化，未来仍可能需要专门的“文档/工具仓库 starter”

## 建议下一步

进入 Phase 3，开始判断哪些高级自动化和发布纪律值得标准化。
