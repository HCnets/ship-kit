---
name: spec
description: 需求解析 - 把需求或现有项目拆成可执行的功能列表、技术方案、Phase 计划。支持新项目和已有项目重构。
argument-hint: <需求描述或项目路径> [--existing]
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch]
---

# 需求解析

输入：$ARGUMENTS

## Step 0：判断模式

解析输入参数：
- 如果包含 `--existing` 或指向已有目录 → **重构模式**
- 否则 → **新建模式**

### 重构模式

1. 扫描项目目录，读取：
   - 项目配置文件（package.json / *.csproj / pyproject.toml / Cargo.toml 等）
   - 入口文件和核心模块
   - 目录结构（`ls` 或 `tree`）
   - README / 已有文档
2. 分析现有功能、技术栈、架构
3. 结合需求描述，识别哪些是新增、哪些是改造

### 新建模式

1. 解析需求描述文本
2. 创建 `brainstorm/<项目名>/`

## Step 1：需求理解

读取需求内容（新建模式）或代码+需求（重构模式），提取：

```
目标：[一句话说清楚要做什么]
用户：[谁会用这个东西]
核心功能：[列出所有提到的功能点]
约束条件：[技术限制、时间限制、平台限制]
非功能需求：[性能、安全、可访问性等]
已有能力：[重构模式：现有项目已实现了什么]
差距分析：[重构模式：需求和现有之间的差距]
```

如果需求不清晰，列出需要确认的问题，等用户回答后再继续。

## Step 2：功能拆分

把需求拆成 5-10 个可验证的功能点：

```
# | 功能名 | 一句话 | 输入 | 输出 | 验收标准 | 工时估算 | 类型
```

- 验收标准必须是**可执行的命令或可观察的行为**，不能是模糊描述
- 类型标记：`新增` / `改造` / `重构` / `保留`
- 工时估算基于：单人全职开发，熟悉技术栈

### 验收标准格式

每个功能的验收标准必须是以下之一：
- **命令验证**：`dotnet build → 0 errors`、`curl http://localhost:3000 → 200`
- **行为验证**：`点击按钮 → 弹出对话框`、`拖拽文件 → 自动加载`
- **状态验证**：`文件存在 AppData/config.json`、`托盘图标可见`

## Step 3：技术方案

### 技术栈推荐表

```
需求类型 → 推荐方案
─────────────────────────────
Web 前端    → Astro / Next.js / Vue / Svelte
REST API    → FastAPI / Express / Flask / Go gin
CLI 工具    → Python click / Node commander / Rust clap
全栈应用    → Next.js / Astro + API routes
AI 应用     → Python + LLM API
数据处理    → Python pandas / Node
移动端      → React Native / Flutter
桌面应用    → C# WPF / Electron / Tauri / Qt
系统工具    → C# / Rust / Go
浏览器扩展  → Chrome Extension / WebExtension
游戏        → Unity / Godot / Bevy
嵌入式      → C / Rust + embedded-hal
```

输出技术方案：
```
框架：[推荐框架] — 理由：[为什么选这个]
数据库：[如需要]
部署：[推荐部署方式]
关键依赖：[核心第三方库]
```

如果需求涉及多个方案可选，列出对比让用户选。

### 重构模式额外输出

```
现有技术栈：[从代码中读取]
保留/复用：[哪些模块直接保留]
需要改造：[哪些模块需要修改]
新增模块：[需要新建的部分]
```

## Step 4：风险评估

```
风险 | 影响 | 概率 | 缓解方案
```

常见风险：
- 技术可行性不确定 → 先做 spike 验证
- 第三方 API 依赖 → 准备 fallback
- 性能瓶颈 → 提前做 benchmark
- 需求可能变更 → 先做核心，留扩展点
- 重构兼容性 → 保留旧逻辑的降级路径

## Step 5：开发计划

按依赖关系排序功能，输出开发顺序：

```
Phase 1（MVP）：功能 1, 2, 3 — 可独立运行
Phase 2（增强）：功能 4, 5 — 丰富体验
Phase 3（完善）：功能 6, 7 — 锦上添花
```

每个 Phase 必须：
- 可独立构建和测试
- 有明确的完成标准
- 输出 Phase 元数据（见 Step 6）

**等用户确认方案和开发顺序后再继续。**

## Step 6：输出元数据

写入 `brainstorm/<项目名>/spec-report.md`，同时写入 `brainstorm/<项目名>/.phase-meta.json`：

```json
{
  "project": "项目名",
  "mode": "new|refactor",
  "techStack": { "framework": "...", "language": "...", "platform": "..." },
  "phases": [
    {
      "id": 1,
      "name": "MVP",
      "features": ["功能1", "功能2", "功能3"],
      "verification": ["命令1", "命令2"],
      "status": "pending"
    }
  ],
  "currentPhase": 1
}
```

`/build` 命令会自动读取此文件，知道该构建哪个 Phase。

## 输出

1. 写入 `brainstorm/<项目名>/spec-report.md`
2. 写入 `brainstorm/<项目名>/.phase-meta.json`
3. 输出：`下一步：/rapid-prototype <项目名> 或 /build <项目名>`

## 约束

- 整个过程不超过 15 分钟
- 功能点必须有可执行的验收标准
- 工时估算要务实，宁可多估不少估
- 如果需求不清晰，必须问清楚再继续，不能猜
- 技术方案要有理由，不能只说"推荐"
- 重构模式必须先读代码再分析，不能假设
