# 技术规范

Ship Kit 的技术细节。每个 skill 的输入、输出、约束、工作流。

---

## 目录结构

```
ship-kit/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据（name, description, version）
├── skills/
│   ├── quick-validate/
│   │   └── SKILL.md         # 快速验证 skill
│   ├── rapid-prototype/
│   │   └── SKILL.md         # 快速原型 skill
│   ├── build/
│   │   └── SKILL.md         # 产品开发 skill
│   ├── ship/
│   │   └── SKILL.md         # 发布部署 skill
│   └── iterate/
│       └── SKILL.md         # 迭代优化 skill
├── CHANGELOG.md             # 版本变更记录
├── SPEC.md                  # 本文件
├── README.md                # 使用指南
└── LICENSE                  # MIT
```

---

## Skill 规范

每个 SKILL.md 必须包含：

```yaml
---
name: <skill-name>           # 命令名（小写，连字符分隔）
description: <一句话描述>      # 显示在命令列表中
argument-hint: <参数提示>      # 提示用户输入什么
allowed-tools: [工具列表]      # 允许使用的工具
---
```

### 前置要求（Frontmatter）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 命令名，与目录名一致 |
| description | string | 是 | 一句话描述 |
| argument-hint | string | 是 | 参数格式提示 |
| allowed-tools | array | 是 | 允许调用的工具列表 |

### 工具清单

| 工具 | 用途 |
|------|------|
| Read | 读取文件 |
| Write | 创建/覆盖文件 |
| Edit | 编辑文件（部分替换） |
| Glob | 文件模式匹配 |
| Grep | 内容搜索 |
| Bash | 执行 shell 命令 |
| Agent | 启动子代理 |
| WebSearch | 网页搜索 |
| WebFetch | 获取网页内容 |

---

## 数据流

```
用户输入方向
    ↓
/quick-validate → brainstorm/<项目>/validate-report.md
    ↓
/rapid-prototype → brainstorm/<项目>/prototype-report.md + 项目代码
    ↓
/build → brainstorm/<项目>/build-report.md + 代码更新 + git commit
    ↓
/ship → brainstorm/<项目>/ship-report.md + 部署
    ↓
/iterate → brainstorm/<项目>/iterate-report.md
    ↓
/build（循环）
```

### 报告格式

所有报告存放在 `brainstorm/<项目名>/` 目录：

| 文件 | 生成者 | 内容 |
|------|--------|------|
| `validate-report.md` | quick-validate | 痛点评分、功能切片、竞品分析 |
| `prototype-report.md` | rapid-prototype | 文件清单、验证结果、已知限制 |
| `build-report.md` | build | 本轮完成内容、验证结果、下一步建议 |
| `ship-report.md` | ship | 部署链接、验证结果、后续计划 |
| `iterate-report.md` | iterate | 扫描结果、反馈分析、改进计划 |

---

## 验证规范

### quick-validate 验证

| 检查项 | 方法 | 通过标准 |
|--------|------|----------|
| 数据来源 | GitHub API | 返回有效 JSON |
| 痛点评分 | 每项必须有证据 | 无证据的评分为无效 |
| 竞品数量 | 搜索结果 | 至少 3 个 |

### rapid-prototype 验证

| 检查项 | 方法 | 通过标准 |
|--------|------|----------|
| 构建成功 | `npm run build` 或对应命令 | exit code 0 |
| 无死链 | `grep` 检查内部链接 | 所有链接对应文件存在 |
| 外部链接 | `curl` 检查前 3 个 | HTTP 200 |
| 产物存在 | `ls dist/` | 文件非空 |

### build 验证

| 检查项 | 方法 | 通过标准 |
|--------|------|----------|
| 构建成功 | 构建命令 | exit code 0 |
| 无死链 | `grep` + 文件检查 | 无 ❌ 输出 |
| 图片 alt | `grep '<img'` | 无缺失 alt 的 img 标签 |
| lang 属性 | `grep 'lang='` | 所有 HTML 文件有 lang |
| Git commit | `git status` | 无未提交变更 |

### ship 验证

| 检查项 | 方法 | 通过标准 |
|--------|------|----------|
| 构建成功 | 构建命令 | exit code 0 |
| 无死链 | 链接检查 | 无死链 |
| README | 文件检查 | 存在且非空 |
| LICENSE | 文件检查 | 存在 |
| .gitignore | 文件检查 | 存在 |
| 版本号 | `package.json` 或 `plugin.json` | 已设置 |
| 部署可访问 | `curl` 检查 URL | HTTP 200 |

### iterate 验证

| 检查项 | 方法 | 通过标准 |
|--------|------|----------|
| 死链检查 | `grep` + 文件检查 | 无死链 |
| 外部链接 | `curl` 检查前 10 个 | 全部可访问 |
| 图片 alt | `grep` | 无缺失 |
| meta description | `grep` | 所有页面有 |
| lang 属性 | `grep` | 所有页面有 |
| 页面大小 | `du -sh` | < 500KB |

---

## 版本管理规范

### 版本号

遵循 [Semantic Versioning](https://semver.org/lang/zh-CN/)：

```
MAJOR.MINOR.PATCH

MAJOR — 不兼容的变更（删除 skill、改变输入格式）
MINOR — 新增功能（新 skill、新验证步骤）
PATCH — 修复（bug 修复、文档更新）
```

### Tag 规范

```bash
git tag -a v5.1.0 -m "v5.1.0 - 新增 xxx 功能"
git push origin v5.1.0
```

### CHANGELOG 规范

- 每个版本必须有日期和变更分类
- 分类：Added / Changed / Deprecated / Removed / Fixed / Security
- 只增不删：旧版本条目永久保留
- 新版本条目添加在文件顶部

### 提交规范

```
<类型>: <描述>

类型：
- feat: 新功能
- fix: 修复
- docs: 文档
- refactor: 重构
- test: 测试
- chore: 构建/工具
```
