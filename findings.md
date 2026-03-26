# 项目对比分析 - 发现记录

## 概述

| 项目 | 定位 | 技术栈 | 核心理念 |
|------|------|--------|----------|
| DeerFlow | 超级 Agent 运行时平台 | Python + LangChain/LangGraph | 技能扩展 + 子代理并行 |
| Memoh | 多成员容器化 AI Agent 系统 | Go + Vue 3 + containerd | 容器隔离 + 多平台 + 记忆管理 |

---

## 核心差异对比

| 维度 | DeerFlow 优势 | Memoh 优势 |
|------|---------------|------------|
| **技能系统** | 20+ 预置技能，完整评估框架 | Skill V2 已对齐，.skill 格式支持 |
| **评估测试** | 触发率评估、基准测试、断言系统 | ❌ 缺失（核心差距） |
| **记忆系统** | JSON 文件 + LLM 提取 | 多 Provider、稀疏向量、向量可视化 |
| **容器化** | Docker/K8s 支持 | containerd + 容器快照 |
| **平台支持** | TG/Slack/Lark | TG/Discord/Lark/Email + 跨平台身份绑定 |
| **任务管理** | TodoList 中间件、Plan Mode | 定时任务、心跳任务 |

---

## 详细发现

### 1. Skill 系统架构 (DeerFlow)

**元数据格式：**
```yaml
---
name: skill-name
description: Skill description
version: 1.0.0
author: author-name
license: MIT
allowed-tools: [tool1, tool2]
compatibility: memoh>=2.0
category: productivity
---
```

**渐进式加载：**
- Level 1: Metadata (始终加载)
- Level 2: Content (按需加载)
- Level 3: Bundled Resources (on-demand)

### 2. Skill 评估框架 (核心差距)

**触发率评估** (`run_eval.py`):
- 使用 `claude -p` 子进程测试技能触发
- Eval Set 格式：query + should_trigger + expected_output
- 支持并行 workers，可配置阈值

**基准测试** (`aggregate_benchmark.py`):
- with_skill vs without_skill 对比
- 统计指标：pass_rate, time, tokens
- Grading.json 格式输出

**断言系统** (`evals/evals.json`):
```json
{
  "skill_name": "example-skill",
  "evals": [
    {
      "id": 1,
      "query": "帮我创建一个 React 组件",
      "should_trigger": true,
      "expected_output": "应该触发 react-frontend skill"
    }
  ]
}
```

### 3. TodoList 中间件

**实现要点：**
- `write_todos` 工具管理任务列表
- 上下文丢失检测（当 todos 离开上下文窗口时提醒）
- Plan Mode 支持

### 4. 技能市场机制

**关键发现：** DeerFlow 使用外部 `npx skills` npm 包，**没有自建市场后端**。

```bash
# DeerFlow 命令
npx skills find [query]
npx skills add owner/repo@skill-name -g -y
```

**Memoh 的 CLI 已对齐：**
```bash
memoh skill list
memoh skill install <name>      # 支持模板/skill.sh/本地文件
memoh skill uninstall <name>
memoh skill export <name>
```

---

## 实现状态

### 已对齐 (✅)

| 功能 | 实现文件 |
|------|----------|
| 扩展元数据 | `internal/agent/tools/skillv2.go` |
| 工具白名单 | `IsToolAllowed()` 方法 |
| 状态管理 | `internal/agent/tools/extensions_config.go` |
| 分类存储 | `public/custom` 目录结构 |
| .skill 打包 | `internal/agent/tools/skill_installer.go` |
| V2 API | `internal/handlers/skills_v2.go` (11端点) |
| CLI 命令 | `cmd/memoh/skill.go` |
| 预置技能库 | `skills/public/` (17个技能) |

### 待实现 (⏳)

| 功能 | 优先级 | 说明 |
|------|--------|------|
| 触发率评估 | 🔴 高 | 核心差距 |
| 基准测试框架 | 🔴 高 | with/without 对比 |
| 断言系统 | 🔴 高 | 定量断言 |
| TodoList 中间件 | 🟡 中 | write_todos 工具 |
| 技能发现工具 | 🟡 中 | find_skills Agent 工具 |
| 本地技能索引 | 🟡 中 | Bleve 全文搜索 |
| 远程仓库 | 🟢 低 | 可选 |

---

## 结论

**DeerFlow** 更像面向开发者的 Agent 开发平台，有完整的技能开发工具链和评估框架。

**Memoh** 更像面向终端用户的 Bot 管理平台，强调多用户、容器隔离和记忆管理。

**核心差距** 是技能评估测试框架（触发率评估、基准测试、断言系统），这是 Memoh 最需要补齐的能力。
