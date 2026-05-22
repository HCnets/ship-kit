---
name: iterate
description: 迭代优化 - 主动扫描问题（死链、性能、可访问性），结合用户反馈确定改进方向。
argument-hint: <项目名>
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch]
---

# 迭代优化

不等反馈，主动找问题。输入：$ARGUMENTS

## 初始化

1. 解析项目名。读取项目代码和所有已有报告
2. 检查是否有用户反馈、GitHub issues

## Step 1：主动扫描（不依赖用户反馈）

**死链检查：**
```bash
# 检查所有内部链接
grep -roh 'href="[^"]*"' dist/ 2>/dev/null | sort -u | while read link; do
  url=$(echo "$link" | sed 's/href="//;s/"//')
  [[ "$url" == http* ]] && continue
  [[ "$url" == "#"* ]] && continue
  [[ "$url" == mailto:* ]] && continue
  if [[ ! -f "dist$url" ]] && [[ ! -f "dist${url}index.html" ]]; then
    echo "❌ 死链: $url"
  fi
done
```

**外部链接检查：**
```bash
grep -roh 'https://[^"]*' dist/ 2>/dev/null | sort -u | head -10 | while read url; do
  code=$(curl -s -o /dev/null -w "%{http_code}" --max-time 5 "$url" 2>/dev/null)
  if [[ "$code" != "200" ]]; then
    echo "⚠️ $code $url"
  fi
done
```

**HTML 质量检查：**
```bash
# 图片缺少 alt
echo "图片无 alt: $(grep -r '<img' dist/ 2>/dev/null | grep -vc 'alt=')"

# 缺少 meta description
echo "无 description: $(grep -rL 'meta name="description"' dist/*.html 2>/dev/null | wc -l)"

# 缺少 lang 属性
echo "无 lang: $(grep -rL 'lang=' dist/*.html 2>/dev/null | wc -l)"

# 页面大小
echo "最大页面: $(du -sh dist/*.html 2>/dev/null | sort -rh | head -1)"
```

**构建产物统计：**
```bash
echo "文件数: $(find dist/ -type f 2>/dev/null | wc -l)"
echo "总大小: $(du -sh dist/ 2>/dev/null | cut -f1)"
```

## Step 2：用户反馈收集

如果有用户反馈（直接输入、GitHub issues、评论），整理：
```
反馈 | 类型(bug/体验/功能) | 优先级(P0/P1/P2)
```

## Step 3：机会排序

结合扫描结果和用户反馈：
```
改进点 | 来源(扫描/反馈) | 用户价值(1-5) | 实现成本 | 优先级
```

优先修 bug 和体验问题，再加新功能。

## Step 4：下一轮计划

```
本轮目标：[一句话]
要做的功能：1-2 个
验收标准：[怎么算做完]
```

**等用户确认迭代方向后再继续。**

## 输出

写入 `brainstorm/<项目名>/iterate-report.md`（扫描结果 + 反馈分析 + 改进计划）。
输出：`下一步：/build <项目名>（开始下一轮开发）`

## 约束

- 一轮只做 1-2 个改进
- **必须执行主动扫描，不能只等反馈**
- 优先修 bug 和体验问题，再加新功能
- 如果用户很少，优先解决获客问题
- 扫描结果要写入报告，不能只在终端输出
