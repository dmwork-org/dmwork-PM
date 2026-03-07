# DMWork PM — 产品需求池

> DMWork IM 产品需求收集、评估、流转的中心仓库

## 这是什么？

这个仓库是 DMWork 产品侧的需求管理中心。所有用户反馈和 Feature 需求在这里收集和评估，评审通过后才会流转到研发仓库成为开发 Issue。

**核心原则：需求池和研发池分离，研发仓库的 Issues 只有确认要做的。**

## 流程

```
用户反馈 → aoli 分析判断
  │
  ├─ 明确 Bug（报错/崩溃/UI异常）→ 直接提到对应研发仓库
  ├─ 拿不准的 → 提到本仓库
  └─ 需求/Feature → 提到本仓库
                      │
                      ▼
              aoli 定时 Review（每天 2:00/14:00/17:00/21:00）
              在 Issue 下出详细需求方案（按改动大小分级）
                      │
                      ▼
              aoli 通知 JOJO 评审
              JOJO 在 Issue 评论区评审，完善需求细节
                      │
                      ▼
              JOJO 写"评审通过"
              aoli 整理推送清单 → DMWork IM 私聊佳佳
                      │
              ┌───────┴───────┐
              ▼               ▼
          佳佳 ✅           佳佳 ❌
          aoli 提到         aoli 关闭 Issue
          研发仓库 Issue    标注原因
          + 关闭 PM Issue
```

## Issue 标签流转

| 标签 | 说明 | 谁操作 |
|------|------|--------|
| `needs-analysis` | 刚提交，待 aoli 出方案 | 自动 |
| `in-review` | 方案已出，JOJO 评审中 | aoli |
| `ready` | JOJO 评审通过，待佳佳确认 | aoli |
| `approved` | 佳佳确认，待提到研发 | aoli |
| `rejected` | 不做，标注原因 | aoli |
| `transferred` | 已流转到研发仓库 | aoli |

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
| **佳佳（yejia）** | 最终审批，确认是否进入研发 |
| **aoli** | 需求收集、评估分析、出方案、推送清单、提研发 Issue、关闭 PM Issue |
| **JOJO** | 需求评审（在 Issue 评论区写"评审通过"） |
| **嘉伟** | 技术约束确认、研发实现 |

## 研发仓库提交规范

提到研发仓库时，遵循各仓库的 Issue Template 和 [WORKFLOW.md](https://github.com/dmwork-org/dmworkim/blob/main/docs/WORKFLOW.md) 规范：

- **分支**：`feat/` `fix/` `refactor/` `docs/` `ci/` 前缀
- **Commit**：Conventional Commits（`feat:` `fix:` `refactor:` 等）
- **PR**：标题遵循 Conventional Commits，关联 Issue（`Closes #xx`），一个 PR 只做一件事，Squash Merge
- **提交前**：确认编译通过、检查有无重复 PR

## 关联仓库

| 仓库 | 说明 |
|------|------|
| [dmwork-org/dmworkim](https://github.com/dmwork-org/dmworkim) | 后端（Go） |
| [dmwork-org/dmwork-web](https://github.com/dmwork-org/dmwork-web) | 前端（Web） |
| [dmwork-org/dmwork-adapters](https://github.com/dmwork-org/dmwork-adapters) | OpenClaw 适配器 |

---

*Maintained by aoli 🤖 & JOJO 🐾*
