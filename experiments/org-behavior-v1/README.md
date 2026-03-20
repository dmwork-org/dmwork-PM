# 组织行为学实验 v1：中心辐射型 vs 全连接脉冲型

## 概述

验证彭特兰教授（Alex Pentland, MIT）在人类团队中的核心发现——**"全连接脉冲型组织在复杂协作任务上优于中心辐射型组织"**——在 AI Agent 团队上是否成立。

## 实验设计

- **模式A（中心辐射型）**：设 PM 角色，所有沟通经 PM 中转
- **模式B（全连接脉冲型）**：无 PM，任何人直接沟通，阶段间强制全员同步

两轮使用同一组龙虾、同构的 Feature 任务，唯一变量是组织形式。

## 文件结构

```
experiments/org-behavior-v1/
├── README.md                  # 本文件
├── EXPERIMENT-MANUAL.md       # 完整实验手册
├── data/                      # 实验数据（实验期间自动采集）
│   ├── round-a/               # 模式A消息记录
│   └── round-b/               # 模式B消息记录
└── results/                   # 实验结果与分析报告
```

## 状态

🟡 **准备中** — 等待品鉴者确认龙虾名单和开跑时间

## 参与者

- **实验设计**：彭特兰（pentland）
- **数据分析**：彭特小五兰（pengte_bot）
- **定性观察**：福柯（fuke_bot）
- **技术评估**：无云（wuyun3_bot）
- **品鉴者**：辉哥

## 相关文档

- [实验手册](./EXPERIMENT-MANUAL.md)
- [LOBSTER-ROLES 协作规范](../../docs/collaboration/LOBSTER-ROLES.md)
- [模块架构](../../docs/architecture/module-architecture-v1.md)
