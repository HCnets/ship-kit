# Changelog

所有版本的变更记录。格式遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。
只增不删：旧版本条目永久保留。

---

## [5.1.0] - 2026-05-23

### Added
- 新增 `/release` skill：规范化版本管理（CHANGELOG + 版本号 + git tag + push）
- 从 git log 自动提取变更，分类到 Added/Changed/Fixed
- 发布前自动检查未提交变更
- 发布后自动验证 tag 和版本号

### Changed
- plugin.json name 从 `product-forge` 改为 `ship-kit`
- 描述从 5 个技能更新为 6 个

---

## [5.0.0] - 2026-05-23

### 核心变化
从"提示词模板"升级为"带自动检查的技能系统"。

### Changed
- `/quick-validate` — 搜索 GitHub issues（不只搜仓库），痛点评分必须有证据支撑
- `/rapid-prototype` — 新增 smoke test 步骤，自动检查死链和 404
- `/build` — 每个功能完成后自动验证（死链/可访问性/HTML质量），自动 git commit
- `/ship` — 从"只 commit"升级为"真正部署"，支持 GitHub Pages，部署后验证可访问
- `/iterate` — 从"等反馈"升级为"主动扫描"（死链/外部链接/HTML质量/页面大小）

### Added
- 每个 skill 内置 bash 验证命令，不靠人记住要检查
- `/build` 自动 git checkpoint（验证通过后自动 commit）
- `/ship` 部署后 curl 验证（确认 200 + 标题正确）
- `/iterate` 主动扫描：死链检查、外部链接检查、HTML 质量检查（alt/description/lang/大小）

### Fixed
- 原型阶段不再生成假 URL（所有外部链接必须真实或标记 #placeholder）

---

## [4.0.0] - 2026-05-23

### 核心变化
从 9 个 skill 合并为 5 个，减少上下文切换。

### Changed
- 9 个独立 skill 合并为 5 个：quick-validate / rapid-prototype / build / ship / iterate
- 采用"分析中执行"哲学：不单独分析，边做边验证

### Added
- `brainstorm/<项目名>/` 目录隔离，报告自动传递
- 关键决策点门控：等用户确认后再继续
- gh CLI 不可用时自动降级为 `curl` 调 GitHub API

### Removed
- `/pain-hunter` — 合并入 `/quick-validate`
- `/slicer` — 合并入 `/quick-validate`
- `/competitor-xray` — 合并入 `/quick-validate`
- `/flywheel` — 删除（分析过重，执行不足）
- `/persona` — 删除（同上）
- `/tech-selector` — 合并入 `/rapid-prototype`
- `/go-to-market` — 合并入 `/ship`
- `/mvp-engineer` — 重命名为 `/rapid-prototype`
- `/iteration-radar` — 重命名为 `/iterate`

---

## [3.0.0] - 2026-05-23

### 核心变化
完整 9-skill 流程，引入项目隔离和数据流。

### Added
- 9 个 skill：pain-hunter / slicer / competitor-xray / flywheel / persona / tech-selector / mvp-engineer / go-to-market / iteration-radar
- 项目隔离：`brainstorm/<项目名>/` 目录
- 数据流：自动读取上一步报告
- 门控：关键决策点等用户确认
- progress.md 进度追踪
- iteration-radar 自动写 memory

---

## [2.0.0] - 2026-05-23

### 核心变化
从 v1 的独立命令升级为有数据流的 skill 链。

### Added
- Skill 之间通过文件传递数据
- 每个 skill 自动读取前序报告

### Fixed
- v1 的文件读写问题（allowed-tools 配置错误）

---

## [1.0.0] - 2026-05-23

### 核心变化
初始版本，独立的 slash commands。

### Added
- 7 个独立命令：/pain-hunter / slicer / competitor-xray / flywheel / persona / tech-selector / mvp-engineer
- 基本的 GitHub API 数据获取

### Known Issues
- 命令之间无数据流，需要手动传递
- 文件读写有时失败
- skill 过长（73-101 行）
