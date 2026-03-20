# A组角色分配

> 实验模式：中心辐射型（Hub-and-Spoke）
> 任务：Issue #70 — Bot API 支持邀请其他 Bot 进群

## 角色

| 角色 | 成员 | 职责 |
|------|------|------|
| **PM（项目经理）** | 无云 ☁️ (wuyun3_bot) | 信息枢纽，所有沟通经其中转，决定阶段切换和优先级 |
| **Sensor/Designer** | aoli 🔧 (pmm_bot) | 需求理解、用户故事、API设计方案、权限模型 |
| **Sensor/Designer 协助** | product_bot | 协助aoli做需求文档整理，沟通同样经PM中转 |
| **Builder** | 戏精 🎭 (xijing_bot) | 技术实现方案、核心逻辑伪代码、可行性确认 |
| **Reviewer** | JOJO 🌟 (joj_bot) | 审查方案一致性、安全性、与现有架构兼容性 |
| **观察者/协调者** | 彭特小五兰 🎓 (pengte) | 实验观察、规则执行监督、断点协调 |

## 沟通规则（必须严格遵守）

```
aoli/product_bot (Sensor/Designer) ←→ 无云 (PM) ←→ 戏精 (Builder)
                                         ↕
                                      JOJO (Reviewer)
```

1. **aoli、product_bot、戏精、JOJO 之间禁止直接 @对方**
2. 所有信息必须经 @无云 中转
3. 阶段切换由无云决定和宣布
4. 违规将被观察者记录

## 分配时间

2026-03-20 15:33 CST

## 分配依据

- **无云→PM**：产品全局视野强，对DMWork产品逻辑熟悉，节奏把控能力好
- **aoli→Sensor/Designer**：对Bot API和群管理最熟，读过源码，跟进过群管理和Bot权限相关需求
- **product_bot→协助Sensor/Designer**：PRD撰写和需求收集经验
- **戏精→Builder**：工程能力最全面，熟悉Go/TS和dmwork完整项目结构，对消息流程有完整认知
- **JOJO→Reviewer**：一直在做Octo需求评审，熟悉模块架构，擅长挑毛病把质量关
