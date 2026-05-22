# Ship Kit

从点子到产品，5 步搞定。

## 这是什么

Ship Kit 是一组 Claude Code 技能（skills），帮你把一个想法变成真正发布的产品。不需要手动分析、不需要写文档模板——输入方向，跟着走就行。

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

然后按提示走完 5 步：

```
/quick-validate  →  验证方向（5 分钟）
/rapid-prototype →  生成原型（10 分钟）
/build           →  完善功能（每轮 15 分钟）
/ship            →  发布上线（5 分钟）
/iterate         →  基于反馈优化
```

## 5 个命令详解

### 1. `/quick-validate` — 验证方向

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

### 2. `/rapid-prototype` — 生成原型

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

### 3. `/build` — 完善产品

**做什么：** 给原型加功能、打磨体验。每轮只做 1-2 个功能。

**输入：**
```
/build 项目名 [要加的功能]
```

**输出：**
- 更新的项目代码
- `brainstorm/<项目名>/build-report.md`
- 自动 git commit

**每个功能完成后自动验证：**
- 构建是否成功
- 是否有死链
- 图片是否有 alt 属性
- HTML 是否有 lang 属性

---

### 4. `/ship` — 发布上线

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

### 5. `/iterate` — 迭代优化

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

## 完整示例

```
# 1. 验证方向
/quick-validate "极简 Markdown 笔记应用" quick-note

# 2. 生成原型
/rapid-prototype quick-note

# 3. 加功能（多轮）
/build quick-note 加深色模式
/build quick-note 加标签筛选

# 4. 发布
/ship quick-note

# 5. 迭代
/iterate quick-note
```

## 文件结构

项目运行后会生成：

```
brainstorm/<项目名>/
├── validate-report.md    # 验证报告
├── prototype-report.md   # 原型报告
├── build-report.md       # 开发报告
├── ship-report.md        # 发布报告
└── iterate-report.md     # 迭代报告
```

## 版本历史

详见 [CHANGELOG.md](./CHANGELOG.md)。

当前版本：**v5.0.0**

## 技术规范

详见 [SPEC.md](./SPEC.md)。

## License

MIT
