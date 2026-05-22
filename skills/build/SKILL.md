---
name: build
description: 产品开发 - 完善原型，加功能，打磨体验。每轮自动验证，不能引入 bug。
argument-hint: <项目名> [要加的功能描述]
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch]
---

# 产品开发

把原型变成产品。输入：$ARGUMENTS

## 初始化

1. 解析项目名和要加的功能。读取项目代码和已有报告
2. 分析当前代码状态：有什么、缺什么、哪里需要改

## Step 1：当前状态评估

```
已实现：[功能列表]
待实现：[功能列表]
技术债务：[问题列表]
代码质量：[好/中/差]
```

## Step 2：功能规划

根据用户输入或报告中的"下一步"，选择本轮要做的 1-2 个功能：
```
功能名 | 价值(1-5) | 难度(1-5) | 工时
```

**等用户确认功能选择后再继续。**

## Step 3：实现

逐个功能实现。每个功能完成后立即验证。

## Step 4：自动验证（每个功能完成后必须执行）

**构建检查：**
```bash
npm run build 2>&1  # 或对应的构建命令
```

**链接检查：**
```bash
# 检查构建产物中的链接
grep -roh 'href="[^"]*"' dist/ | sort -u | while read link; do
  url=$(echo "$link" | sed 's/href="//;s/"//')
  # 跳过外部链接和锚点
  if [[ "$url" == http* ]] || [[ "$url" == "#"* ]] || [[ "$url" == mailto:* ]]; then
    continue
  fi
  # 检查内部链接对应的文件是否存在
  if [[ ! -f "dist$url" ]] && [[ ! -f "dist${url}index.html" ]]; then
    echo "❌ 死链: $url"
  fi
done
echo "✅ 链接检查完成"
```

**基础可访问性：**
```bash
# 检查图片是否有 alt 属性
grep -r '<img' dist/ | grep -v 'alt=' | head -5

# 检查是否有 lang 属性
grep -l 'lang=' dist/*.html
```

**如果有任何检查失败，修复后再继续。**

## Step 5：Git checkpoint

验证通过后，自动 commit：
```bash
git add -A
git commit -m "feat: <功能名称>"
```

## Step 6：体验打磨

核心功能做完后，花 30 分钟打磨：
- 错误提示要友好
- 输出格式要好看
- 配置要简单
- 启动要快

## 输出

更新项目代码。
写入 `brainstorm/<项目名>/build-report.md`（本轮做了什么 + 验证结果 + 下一步建议）。
输出：`下一步：/build <项目名>（继续加功能）或 /ship <项目名>（准备发布）`

## 约束

- 每轮只做 1-2 个功能，不贪多
- **每个功能完成后必须执行自动验证**
- **不能引入死链或 404**
- 验证通过后自动 git commit
- 如果发现架构需要重构，先问用户
- 保持代码可读性
