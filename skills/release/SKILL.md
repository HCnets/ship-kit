---
name: release
description: 版本发布 - 更新 CHANGELOG、bump 版本号、打 tag、push。规范化的版本管理。
argument-hint: [major|minor|patch] [发布说明]
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

# 版本发布

规范化发版。输入：$ARGUMENTS

## 初始化

1. 解析版本类型（major/minor/patch，默认 minor）和发布说明
2. 读取当前版本号（从 `plugin.json` 或 `package.json`）
3. 检查 git 状态

## Step 1：状态检查

```bash
# 检查是否有未提交变更
git status --porcelain

# 检查当前版本
cat .claude-plugin/plugin.json | grep version

# 检查已有 tags
git tag -l --sort=-v:refname | head -5

# 检查 CHANGELOG 是否有未发布内容
head -20 CHANGELOG.md
```

如果有未提交变更，先 commit 再继续。

## Step 2：确定版本号

当前版本 → 新版本：
```
5.0.0 + patch → 5.0.1（修复）
5.0.0 + minor → 5.1.0（新功能，默认）
5.0.0 + major → 6.0.0（不兼容变更）
```

**等用户确认版本号后再继续。**

## Step 3：更新 CHANGELOG

在 CHANGELOG.md 顶部添加新版本条目（只增不删，旧版本保留）：

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added
- 新增内容

### Changed
- 变更内容

### Fixed
- 修复内容
```

从上一次发布以来的 git log 中提取变更：
```bash
git log $(git describe --tags --abbrev=0 2>/dev/null || echo "")..HEAD --oneline
```

根据 commit message 自动分类：
- `feat:` → Added
- `fix:` → Fixed
- `docs:` → (记录但不单独分类)
- `refactor:` → Changed

## Step 4：更新版本号

更新所有包含版本号的文件：
```bash
# plugin.json
sed -i 's/"version": ".*"/"version": "X.Y.Z"/' .claude-plugin/plugin.json

# package.json（如存在）
[ -f package.json ] && sed -i 's/"version": ".*"/"version": "X.Y.Z"/' package.json
```

## Step 5：提交并打 Tag

```bash
# Commit 版本变更
git add CHANGELOG.md .claude-plugin/plugin.json package.json 2>/dev/null
git commit -m "release: vX.Y.Z"

# 打 tag
git tag -a vX.Y.Z -m "vX.Y.Z - 发布说明"

# Push
git push origin main
git push origin vX.Y.Z
```

## Step 6：验证

```bash
# 确认 tag 存在
git tag -l "vX.Y.Z"

# 确认 CHANGELOG 包含新版本
grep "## \[X.Y.Z\]" CHANGELOG.md

# 确认版本号正确
grep '"version"' .claude-plugin/plugin.json
```

## 输出

```
✅ 发布完成：vX.Y.Z
📦 Tag: vX.Y.Z
📝 CHANGELOG 已更新
🔗 https://github.com/<user>/<repo>/releases/tag/vX.Y.Z
```

## 约束

- 发布前必须所有变更已 commit
- CHANGELOG 只增不删，旧版本条目永久保留
- 版本号遵循 semver
- 每次发布必须有 git tag
- 发布后必须验证 tag 和版本号正确
- 发布说明从 git log 自动提取，用户可修改
