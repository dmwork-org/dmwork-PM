# PM Native 组织行为学实验

> 验证彭特兰教授的发现——"全连接脉冲型在复杂任务上优于中心辐射型"——在AI Agent团队上是否成立。

## 状态：🟡 准备中

## 文件结构

```
experiments/org-behavior-v1/
├── README.md                  ← 本文件
├── EXPERIMENT-MANUAL.md       ← 完整实验手册
├── ROUND-A-BRIEF.md           ← A组任务书（中心辐射型 · Issue #70）
├── ROUND-B-BRIEF.md           ← B组任务书（全连接脉冲型 · Issue #71）
├── data/
│   ├── round-a/               ← A组消息数据（待采集）
│   └── round-b/               ← B组消息数据（待采集）
└── results/                   ← 分析报告（待产出）
```

## 快速概览

| 维度 | A组 | B组 |
|------|-----|-----|
| 组织模式 | 中心辐射型（Hub-and-Spoke） | 全连接脉冲型（Pulsed Full-Connect） |
| 任务 | #70 Bot邀请Bot进群 | #71 Bot查询群列表和成员 |
| 角色 | PM + Sensor/Designer + Builder + Reviewer | Sensor/Designer + Builder + Reviewer |
| 核心规则 | 所有沟通经PM中转 | 任何人直接@任何人，阶段间同步脉冲 |
