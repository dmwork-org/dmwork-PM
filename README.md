# DMWork PM — 产品需求池

> DMWork IM 产品需求收集、评估、流转的中心仓库

## 这是什么？

这个仓库是 DMWork 产品侧的需求管理中心，所有用户反馈、Feature 需求在这里收集和评估，评审通过后才会流转到研发仓库（dmworkim / dmwork-web / dmwork-adapters）成为开发 Issue。

**核心原则：需求池和研发池分离，研发仓库的 Issues 只有确认要做的。**

## 流程

```
用户反馈
  │
  ├─ 明确 Bug（报错/崩溃/UI异常）→ 直接提到对应研发仓库
  ├─ 拿不准的 → 提到本仓库
  └─ 需求/Feature → 提到本仓库
                      │
                      ▼
              aoli 每日 Review
              在 Issue 下出详细需求方案
              （按改动大小分级：简版/标准/完整 PRD）
                      │
                      ▼
              JOJO 在 Issue 评论区评审
              完善需求细节，达成共识
                      │
                      ▼
              在 DMWork 找佳佳确认
                      │
              ┌───────┴───────┐
              ▼               ▼
          佳佳 ✅           佳佳 ❌
          aoli 提到         关闭 Issue
          研发仓库          标注原因
```

## Issue 分类

| 标签 | 说明 |
|------|------|
| `bug` | 可能是 Bug，待确认 |
| `feature` | 新功能需求 |
| `enhancement` | 已有功能改进 |
| `needs-analysis` | 待 aoli 出方案 |
| `in-review` | 方案已出，JOJO 评审中 |
| `ready` | 评审通过，待佳佳确认 |
| `approved` | 佳佳确认，待提到研发仓库 |
| `rejected` | 不做，标注原因 |
| `transferred` | 已流转到研发仓库 |

## PRD 分级标准

| 改动大小 | 标准 | PRD 要求 |
|----------|------|----------|
| **小** | <1天，单模块 | 简版：标题 + 描述 + 交互说明 |
| **中** | 1-3天，跨模块 | 标准版：用户故事 + 流程图(Mermaid) + 接口设计 |
| **大** | >3天，涉及架构 | 完整版：全套 PRD + 线框图 + 技术方案 |

## 出图标准

- 默认 ASCII 图
- 流程逻辑用 Mermaid
- 需要 HTML 原型时先找佳佳确认

## 角色分工

| 角色 | 职责 |
|------|------|
| **佳佳（yejia）** | 最终审批，确认是否进研发 |
| **aoli** | 需求收集、评估分析、出方案、提研发 Issue |
| **JOJO** | 需求评审、产品侧分析补充 |
| **嘉伟** | 技术约束确认、研发实现 |

## 关联仓库

| 仓库 | 说明 |
|------|------|
| [dmwork-org/dmworkim](https://github.com/dmwork-org/dmworkim) | 后端（Go） |
| [dmwork-org/dmwork-web](https://github.com/dmwork-org/dmwork-web) | 前端（Web） |
| [dmwork-org/dmwork-adapters](https://github.com/dmwork-org/dmwork-adapters) | OpenClaw 适配器 |

---

*Maintained by aoli 🤖 & JOJO 🐾*
