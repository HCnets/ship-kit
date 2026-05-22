---
name: ship
description: 发布上线 - 打包、写 README、部署到 GitHub Pages / Vercel / Netlify。真正发出去。
argument-hint: <项目名> [部署平台]
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch]
---

# 发布上线

真正发出去，不只是 commit。输入：$ARGUMENTS

## 初始化

1. 解析项目名和部署平台。读取项目代码和已有报告
2. 检查项目当前状态

## Step 1：发布前检查（必须全部通过）

```bash
# 构建
npm run build 2>&1
# 预期：成功，无错误

# 链接检查
grep -roh 'href="[^"]*"' dist/ | sort -u | wc -l
# 预期：有输出，且无死链

# 文件检查
ls dist/index.html  # 或对应的入口文件
# 预期：文件存在

# 依赖检查
cat package.json | grep -c '"dependencies"'
# 预期：依赖已声明
```

```
□ 核心功能能跑（构建成功）
□ 无死链（所有内部链接有效）
□ README 存在且说清楚了是什么
□ 依赖已声明
□ .gitignore 存在
□ LICENSE 存在
□ 版本号已设置
□ Git 已 commit（无未提交变更）
```

缺什么补什么。

## Step 2：README 编写

如果 README 不完整，生成标准 README：
```markdown
# 项目名
> 一句话描述

## 截图（如有）
## 安装
## 快速开始
## 功能
## 技术栈
## 贡献
## License
```

## Step 3：部署

根据项目类型选择部署方式：

**静态网站（Astro/Hugo/Next.js）→ GitHub Pages：**
```bash
# 初始化 git（如未初始化）
git init
git add -A
git commit -m "Initial release v0.1.0"

# 创建 GitHub repo 并推送
gh repo create <项目名> --public --source=. --default-branch main
git push -u origin main

# 启用 GitHub Pages（通过 gh CLI）
gh api repos/{owner}/{repo}/pages -X POST -f source.branch=main -f source.path=/
```

**如果 gh CLI 不可用：**
```bash
# 提示用户手动操作
echo "1. 在 GitHub 创建仓库: https://github.com/new"
echo "2. 推送代码:"
echo "   git remote add origin https://github.com/<user>/<repo>.git"
echo "   git push -u origin main"
echo "3. Settings → Pages → Source: Deploy from branch (main)"
```

**Python 包 → PyPI：**
```bash
pip install build twine
python -m build
twine upload dist/*
```

**npm 包：**
```bash
npm publish
```

## Step 4：部署后验证

```bash
# 如果是 GitHub Pages，等待部署完成后验证
sleep 30
curl -s -o /dev/null -w "%{http_code}" https://<user>.github.io/<repo>/
# 预期：200

# 检查页面内容
curl -s https://<user>.github.io/<repo>/ | grep -o '<title>[^<]*</title>'
# 预期：标题正确
```

**等用户确认部署成功后再继续。**

## 输出

项目已部署。
写入 `brainstorm/<项目名>/ship-report.md`（部署链接 + 验证结果 + 后续计划）。
输出：`下一步：/iterate <项目名>（基于反馈迭代）`

## 约束

- 发布前必须验证构建成功、无死链
- README 要让陌生人 30 秒看懂
- 不要发布包含密钥或敏感信息的代码
- 版本号遵循 semver
- **必须真正部署，不能只 commit**
- **部署后必须验证可访问**
