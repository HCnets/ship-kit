---
name: quick-validate
description: 快速验证 - 用真实数据验证方向。搜 issues 看用户骂什么，搜竞品看市场空白，定义 MVP 切片。
argument-hint: <产品方向描述> [项目名]
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch]
---

# 快速验证

用数据说话，不用感觉。输入：$ARGUMENTS

## 初始化

1. 解析方向描述和项目名
2. 创建 `brainstorm/<项目名>/`
3. 检查 `gh` CLI，不可用则用 `Bash curl` 调 GitHub API

## Step 1：痛点验证（5 分钟）

**搜仓库看热度：**
```bash
curl -s "https://api.github.com/search/repositories?q=<关键词>&sort=stars&order=desc&per_page=10"
```

**搜 issues 看真实痛点（关键步骤）：**
```bash
curl -s "https://api.github.com/search/issues?q=<关键词>+is:issue+sort:comments-desc&per_page=10"
```

从 issues 中提取：
- 用户在抱怨什么（高频词）
- 需求有多强烈（评论数、emoji 反应）
- 现有方案哪里不行

**输出痛点评分（必须有证据）：**
```
频率(1-10)：[分] — 证据：[具体数据/issue/趋势]
强度(1-10)：[分] — 证据：[用户原话/情绪强度]
付费意愿(1-10)：[分] — 证据：[竞品定价/市场数据]
竞品覆盖(1-10)：[分] — 证据：[竞品数量和质量]
综合：[加权平均] | 结论：[值得做/需验证/不建议]
```

如果综合 < 5，明确说"换一个方向"，不要硬做。

## Step 2：功能切片（3 分钟）

拆 5-8 个最小功能，选 MVP 切片：
```
切片名 | 一句话 | 输入 | 输出 | 工时 | 验收标准
```

验收标准必须是可验证的：能点击、能显示、能通过命令检查。

## Step 3：竞品快扫（3 分钟）

找 3-5 个竞品，每个记：
```
名称 | Stars/用户量 | 做得好的 | 做得烂的 | 没做的
```

差异化一句话：我们和他们最大的不同是 ___

**等用户确认方向和切片后再继续。**

## 输出

写入 `brainstorm/<项目名>/validate-report.md`。
输出：`下一步：/rapid-prototype <项目名>`

## 约束

- 整个过程不超过 15 分钟
- 数据来自 GitHub API，不编造
- 痛点评分必须有具体证据支撑
- 如果方向不靠谱，要有勇气说"换一个"
- issues 搜索是必须步骤，不能跳过
