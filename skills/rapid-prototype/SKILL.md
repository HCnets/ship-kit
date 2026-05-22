---
name: rapid-prototype
description: 快速原型 - 从验证报告生成可运行原型。能跑就行，但不能有 bug。
argument-hint: <项目名>
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch]
---

# 快速原型

从报告到能跑的代码。输入：$ARGUMENTS

## 初始化

1. 解析项目名。读取 `brainstorm/<项目名>/validate-report.md`
2. 如果没有验证报告，用 $ARGUMENTS 作为方向直接开始
3. 创建项目目录

## Step 1：技术选型（快速版）

根据产品类型直接选，不纠结：
```
Web 应用 → Python Flask / Node Express
CLI 工具 → Python click / Node commander
桌面应用 → Electron / Python tkinter
API 服务 → Python FastAPI
AI 应用 → Python + httpx + LLM API
静态网站 → Astro / Hugo / Next.js
```

## Step 2：生成骨架

创建项目结构和核心文件。只做切片范围内的功能。

## Step 3：实现核心功能

写出能跑的代码。规则：
- 能跑 > 完美
- 一个文件能搞定就不要拆成十个
- 不写测试（原型阶段）
- 不做错误处理（原型阶段）
- 留 TODO 标记后续扩展点
- **所有外部链接必须是真实可访问的 URL，或标记为 `#placeholder`**
- **不要写假的 GitHub URL、假的邮箱、假的链接**

## Step 4：验证清单（必须执行）

构建项目，确认能跑：
```bash
# 根据项目类型执行
npm run build   # Node/Astro
python -c "import <module>"  # Python
```

**Smoke test — 自动检查：**
```bash
# 检查所有内部链接是否存在
grep -r 'href="' dist/ | grep -v 'http' | grep -v '#' | grep -v 'mailto:'

# 检查所有外部 URL 是否可访问（取前 3 个）
grep -roh 'https://[^"]*' dist/ | head -3 | while read url; do
  curl -s -o /dev/null -w "%{http_code} $url\n" "$url"
done

# 检查构建产物是否存在
ls -la dist/ || ls -la build/ || ls -la output/
```

如果有 404 或构建失败，修复后再继续。

**输出验证结果：**
```
构建：✅ 成功 / ❌ 失败
内部链接：✅ 全部有效 / ❌ 发现 N 个死链
外部链接：✅ 可访问 / ⚠️ 部分不可访问（列出）
产物：✅ N 个文件生成
```

## 输出

创建项目代码文件。
写入 `brainstorm/<项目名>/prototype-report.md`（文件清单 + 验证结果 + 已知限制）。
输出：`下一步：/build <项目名>`

## 约束

- 目标：30 分钟内产出可运行原型
- 只做核心功能，其他都是 TODO
- 代码给人看，不是给机器看
- 依赖最小化，能用标准库就用标准库
- **验证清单必须执行，不能跳过**
- **不能有 404 链接**
