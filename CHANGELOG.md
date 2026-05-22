# Changelog

所有版本的变更记录。格式遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。
只增不删：旧版本条目永久保留。

---

## [1.3.0] - 2026-05-23

### Changed
- `/spec` 支持 `--existing` 模式：先读代码再分析，适用于已有项目重构
- `/spec` 技术栈推荐表扩展：新增桌面应用、系统工具、浏览器扩展、游戏、嵌入式等类别
- `/spec` 验收标准格式规范化：必须是可执行命令或可观察行为，不能模糊描述
- `/spec` 输出 Phase 元数据 `.phase-meta.json`，供 `/build` 自动读取
- `/build` 初始化时自动读取 `.phase-meta.json`，定位到当前 Phase
- `/build` 完成后自动更新 Phase 状态

### Added
- `/spec` 重构模式：扫描项目配置、入口文件、目录结构，分析已有能力和差距
- `/spec` 功能类型标记：新增/改造/重构/保留
- Phase 元数据文件 `.phase-meta.json`：skill 间自动串联

---

## [1.2.0] - 2026-05-23

### Added
- 新增 `/spec` skill：需求解析入口，把具体需求文档拆成可执行的功能列表
- 支持两种入口：模糊想法走 `/quick-validate`，具体任务走 `/spec`
- 需求理解 → 功能拆分 → 技术方案 → 风险评估 → 开发计划
- 每个功能点必须有验收标准和工时估算

### Changed
- 描述从 6 个技能更新为 7 个

---

## [1.1.0] - 2026-05-23

### Added
- 新增 `/release` skill：规范化版本管理（CHANGELOG + 版本号 + git tag + push）
- 从 git log 自动提取变更，分类到 Added/Changed/Fixed
- 发布前自动检查未提交变更
- 发布后自动验证 tag 和版本号

### Changed
- plugin.json name 从 `product-forge` 改为 `ship-kit`
- 描述从 5 个技能更新为 6 个

---

## [1.0.0] - 2026-05-23

### 核心变化
Ship Kit 首个正式版本。7 个 skill，从点子到产品一站式流程。

### 包含 Skill
- `/quick-validate` — 市场验证（搜 GitHub issues，痛点评分）
- `/spec` — 需求解析（功能拆分、技术方案、Phase 计划）
- `/rapid-prototype` — 快速原型（smoke test 自动检查）
- `/build` — 产品开发（每功能自动验证 + git commit）
- `/ship` — 发布部署（GitHub Pages + curl 验证）
- `/iterate` — 迭代优化（主动扫描问题）
- `/release` — 版本管理（CHANGELOG + tag + push）

### 特性
- `brainstorm/<项目名>/` 目录隔离，报告自动传递
- 每个 skill 内置 bash 验证命令
- 关键决策点门控：等用户确认后再继续
- gh CLI 不可用时自动降级为 curl

---

## [开发历史] - 2026-05-23

以下为仓库创建前的开发迭代记录，无对应 git tag。

### v4.0 — 从 9 个 skill 合并为 5 个
- 9 个独立 skill 合并为 5 个：quick-validate / rapid-prototype / build / ship / iterate
- 采用"分析中执行"哲学：不单独分析，边做边验证

### v3.0 — 完整 9-skill 流程
- 9 个 skill：pain-hunter / slicer / competitor-xray / flywheel / persona / tech-selector / mvp-engineer / go-to-market / iteration-radar
- 项目隔离、数据流、门控、progress.md

### v2.0 — Skill 链
- Skill 之间通过文件传递数据
- 每个 skill 自动读取前序报告

### v1.0 — 初始版本
- 7 个独立命令：/pain-hunter / slicer / competitor-xray / flywheel / persona / tech-selector / mvp-engineer
- 基本的 GitHub API 数据获取
