# 项目对比分析任务计划

## 目标
对比 deer-flow 和 memoh 项目，找出 memoh 可以优化的功能点。

---

## 分析阶段

| 阶段 | 任务 | 状态 | 关键发现 |
|------|------|------|----------|
| Phase 1 | DeerFlow 项目分析 | ✅ 完成 | 超级 Agent 运行时平台，技能系统 + 子代理 + 评估框架 |
| Phase 2 | Memoh 项目分析 | ✅ 完成 | 多成员容器化 AI Agent，containerd + 多 Provider 记忆 |
| Phase 3 | 功能对比 | ✅ 完成 | DeerFlow 强在技能生态和评估，Memoh 强在容器化和记忆 |
| Phase 4 | 生成优化建议 | ✅ 完成 | 高优先级：评估框架、预置技能库、技能触发优化 |
| Phase 5 | Skill System V2 实现 | ✅ 完成 | 与 DeerFlow 对齐，11个 API 端点，CLI 命令 |
| Phase 6 | 技能市场与发现机制 | ⏳ 规划中 | 详见 findings.md |

---

## Phase 5 实现详情

### 已完成组件

| 组件 | 文件 | 功能 |
|------|------|------|
| SkillV2 类型 | `internal/agent/tools/skillv2.go` | 元数据解析、验证、渐进加载 |
| 状态管理 | `internal/agent/tools/extensions_config.go` | JSON 持久化、线程安全 |
| 打包安装器 | `internal/agent/tools/skill_installer.go` | .skill ZIP 安装/导出 |
| V2 API | `internal/handlers/skills_v2.go` | 11个 REST 端点 |
| CLI 命令 | `cmd/memoh/skill.go` | list/get/install/uninstall/enable/disable/export |

### V2 API 端点

```
GET    /api/v1/bots/{bot_id}/skills/v2
GET    /api/v1/bots/{bot_id}/skills/v2/{name}
PUT    /api/v1/bots/{bot_id}/skills/v2/{name}
PATCH  /api/v1/bots/{bot_id}/skills/v2/{name}/state
DELETE /api/v1/bots/{bot_id}/skills/v2/{name}
POST   /api/v1/bots/{bot_id}/skills/v2/validate
POST   /api/v1/bots/{bot_id}/skills/v2/install
POST   /api/v1/bots/{bot_id}/skills/v2/install/template
POST   /api/v1/bots/{bot_id}/skills/v2/install/market
GET    /api/v1/bots/{bot_id}/skills/v2/{name}/export
```

---

## Phase 6: Skill 评估框架 (建议)

基于 DeerFlow 源码分析，建议按以下优先级实施：

| 优先级 | 功能 | 参考文件 |
|--------|------|----------|
| 🔴 高 | 触发率评估 | `skill-creator/scripts/run_eval.py` |
| 🔴 高 | 基准测试框架 | `skill-creator/scripts/aggregate_benchmark.py` |
| 🔴 高 | 断言系统 | `evals/evals.json` 格式 |
| 🟡 中 | TodoList 中间件 | `agents/middlewares/todo_middleware.py` |
| 🟡 中 | 技能发现工具 | `find-skills/` skill |
| 🟢 低 | 远程仓库 | `npx skills` 参考 |

---

## 参考文档

- **详细分析报告**: `findings.md`
- **进展记录**: `progress.md`
