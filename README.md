# Ship Kit

从点子到产品，7 步搞定。支持两种入口：模糊想法和具体任务。

## 这是什么

Ship Kit 是一组 Claude Code 技能（skills），帮你把想法或需求变成真正发布的产品。两种入口：
- **模糊想法** → `/quick-validate`（搜数据验证方向）
- **具体任务** → `/spec`（解析需求拆功能）

后续流程共用：`/rapid-prototype` → `/build` → `/ship` → `/iterate` → `/release`

## 安装

```bash
git clone https://github.com/HCnets/ship-kit.git ~/.claude/plugins/ship-kit
```

重启 Claude Code 后生效。

## 快速开始

打开 Claude Code，输入：

```
/quick-validate "给未来的自己写信的应用" time-mailbox
```

然后按提示走完：

```
# 入口 A：模糊想法
/quick-validate  →  验证方向（5 分钟）

# 入口 B：具体任务
/spec            →  需求解析（10 分钟）

# 后续流程（两个入口共用）
/rapid-prototype →  生成原型（10 分钟）
/build           →  完善功能（每轮 15 分钟）
/ship            →  发布上线（5 分钟）
/iterate         →  基于反馈优化
/release         →  版本发布（CHANGELOG + tag）
```

## 7 个命令详解

### 1. `/spec` — 需求解析

**做什么：** 把需求或现有项目拆成可执行的功能列表、技术方案、Phase 计划。

**输入：**
```
/spec "需求描述" 项目名           # 新建模式
/spec "项目路径" --existing       # 重构模式（先读代码再分析）
```

**输出：** `brainstorm/<项目名>/spec-report.md` + `.phase-meta.json`
- 需求理解（目标/用户/核心功能/约束）
- 功能拆分（5-10 个功能点，每个有可执行的验收标准）
- 技术方案（框架/数据库/部署/关键依赖）
- 风险评估
- 开发计划（分 Phase，自动串联到 `/build`）

**示例：**
```
/spec "用户管理系统，JWT 认证，PostgreSQL，Docker" user-api
/spec "smtc-lyrics" --existing    # 重构已有项目
```

---

### 2. `/quick-validate` — 验证方向（模糊想法入口）

**做什么：** 搜 GitHub 仓库和 issues，验证你的方向是否有真实需求。

**输入：**
```
/quick-validate "方向描述" 项目名
```

**输出：** `brainstorm/<项目名>/validate-report.md`
- 痛点评分（频率/强度/付费意愿/竞品覆盖）
- 功能切片（5-8 个最小功能，选 MVP）
- 竞品快扫（3-5 个竞品对比）

**示例：**
```
/quick-validate "AI 代码审查工具" code-review
```

---

### 3. `/rapid-prototype` — 生成原型

**做什么：** 从验证报告直接生成可运行的代码。能跑就行，不追求完美。

**输入：**
```
/rapid-prototype 项目名
```

**前提：** 已运行 `/quick-validate`，有 `validate-report.md`。

**输出：**
- 项目代码文件
- `brainstorm/<项目名>/prototype-report.md`（文件清单 + 验证结果）

**自动检查：**
- 构建是否成功
- 内部链接是否有效
- 外部链接是否可访问

---

### 4. `/build` — 完善产品

**做什么：** 给原型加功能、打磨体验。每轮只做 1-2 个功能。自动读取 `/spec` 的 Phase 计划。

**输入：**
```
/build 项目名 [要加的功能]
```

**输出：**
- 更新的项目代码
- `brainstorm/<项目名>/build-report.md`
- 自动 git commit
- 自动更新 Phase 状态

**每个功能完成后自动验证：**
- 构建是否成功
- 是否有死链
- 图片是否有 alt 属性
- HTML 是否有 lang 属性

---

### 5. `/ship` — 发布上线

**做什么：** 打包、写 README、部署到 GitHub Pages。真正发出去。

**输入：**
```
/ship 项目名
```

**输出：**
- GitHub 仓库 + 部署
- `brainstorm/<项目名>/ship-report.md`

**发布前检查清单：**
- 构建成功
- 无死链
- README / LICENSE / .gitignore 存在
- 版本号已设置
- 部署后验证可访问

---

### 6. `/iterate` — 迭代优化

**做什么：** 主动扫描问题（不只等反馈），确定下一轮改进方向。

**输入：**
```
/iterate 项目名
```

**输出：** `brainstorm/<项目名>/iterate-report.md`

**主动扫描项：**
- 死链检查
- 外部链接可访问性
- 图片 alt 属性
- meta description
- lang 属性
- 页面大小

---

### 7. `/release` — 版本发布

**做什么：** 规范化发版——更新 CHANGELOG、bump 版本号、打 git tag、push。

**输入：**
```
/release [major|minor|patch] [发布说明]
```

**输出：**
- CHANGELOG.md 添加新版本条目
- plugin.json / package.json 版本号更新
- git tag（vX.Y.Z）
- push 到远程

**自动处理：**
- 从 git log 提取变更，自动分类到 Added/Changed/Fixed
- 发布前检查未提交变更
- 发布后验证 tag 和版本号
- CHANGELOG 只增不删

---

## 完整示例

**示例 A：从模糊想法开始**
```
/quick-validate "极简 Markdown 笔记应用" quick-note
/rapid-prototype quick-note
/build quick-note 加深色模式
/ship quick-note
/iterate quick-note
/release minor 加了深色模式
```

**示例 B：从具体任务开始**
```
/spec "用户管理系统，JWT 认证，PostgreSQL，Docker" user-api
/rapid-prototype user-api
/build user-api 加权限管理
/ship user-api
/release minor 加权限管理
```

**示例 C：重构已有项目**
```
/spec "smtc-lyrics" --existing
/build smtc-lyrics
/ship smtc-lyrics
```

## 文件结构

项目运行后会生成：

```
brainstorm/<项目名>/
├── validate-report.md    # 验证报告
├── spec-report.md        # 需求分析
├── .phase-meta.json      # Phase 元数据（/build 自动读取）
├── prototype-report.md   # 原型报告
├── build-report.md       # 开发报告
├── ship-report.md        # 发布报告
└── iterate-report.md     # 迭代报告
```

## 版本历史

详见 [CHANGELOG.md](./CHANGELOG.md)。

当前版本：**v5.3.0**

## 技术规范

详见 [SPEC.md](./SPEC.md)。

## License

MIT
