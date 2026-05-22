# Product Forge v5.0

从点子到产品，5 步搞定。数据驱动，自动验证，真正部署。

## 命令

| 命令 | 用途 | v5 改进 |
|------|------|---------|
| `/quick-validate` | 快速验证 | 搜 issues 看真实痛点，不只看 stars |
| `/rapid-prototype` | 快速原型 | 自动 smoke test，不能有 404 |
| `/build` | 产品开发 | 每个功能自动验证（死链/可访问性），自动 git commit |
| `/ship` | 发布部署 | 真正部署（GitHub Pages），部署后验证可访问 |
| `/iterate` | 迭代优化 | 主动扫描（死链/性能/HTML质量），不只等反馈 |

## 用法

```
/quick-validate "给未来的自己写信的应用" time-mailbox
/rapid-prototype time-mailbox
/build time-mailbox
/ship time-mailbox
/iterate time-mailbox
```

## v4 → v5 核心变化

| 问题 | v4 | v5 |
|------|----|----|
| 验证靠拍脑袋 | stars 看热度 | 搜 issues 看用户骂什么 |
| 原型有 404 | 不检查 | smoke test 自动检查 |
| build 引入 bug | 不验证 | 每个功能自动检查死链/可访问性 |
| ship 只 commit | git commit | 真部署 + 部署后验证 |
| iterate 等反馈 | 被动 | 主动扫描死链/性能/HTML质量 |

## 流程

```
/quick-validate → /rapid-prototype → /build → /ship → /iterate
      ↑                                                    |
      └────────────────────────────────────────────────────┘
```
