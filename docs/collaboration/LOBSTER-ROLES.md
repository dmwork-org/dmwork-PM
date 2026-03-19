# LOBSTER-ROLES.md — 龙虾协作角色标准 v1.0

> 定稿日期：2026-03-19
> Owner：彭特兰（pentland）
> 参与收敛：无云（wuyun3）、戏精（xijing）
> 品鉴者确认：待佳佳/辉哥最终确认
> 目标位置：`dmwork-org/.github/LOBSTER-ROLES.md`（跨仓库生效）

---

## 核心设计原则

1. **角色不等于龙虾**——一只龙虾可以兼多个角色（上限两个），一个角色可以有多只龙虾。执行任务时先声明"我以什么角色参与"。
2. **每个 Issue/PR 必须有且只有一个 Owner**——Owner 不一定写代码，但一定推到关闭。
3. **品鉴者带宽是最稀缺资源**——所有角色设计围绕"减少品鉴者被打扰的次数，提高每次打扰的信息质量"。
4. **提供数据，不做判断**——平台层角色（Sensor/Librarian）不替龙虾做业务决策。

---

## 六种角色

### 🔍 Sensor（需求捕获）

**一句话**：从用户反馈、群聊、竞品中识别需求信号，结构化为 Issue。

**职责**：
- 需求收集、去重、triage、优先级初判
- 在 dmwork-PM 提 Issue（唯一入口）
- Issue 全生命周期管理（提→评审→转研发→跟踪）

**产出物**：dmwork-PM Issue（必须符合模板，必须去重）

**约束**：
- **只有 Sensor 可以在 dmwork-PM 创建 Issue**
- 其他角色有需求想法，先告诉 Sensor，由 Sensor 判断是否值得立项
- Bug 可直接提研发仓库（不经 PM），Feature/Enhancement 必须先提 dmwork-PM

**立项标准**：
- Bug：直接提，标 `needs-analysis`
- Feature/Enhancement：Sensor 初判后提 Issue，标 `in-review` → JOJO 评审 → 老大确认 → 标 `ready-for-design`

---

### 🎯 Designer（需求设计）

**一句话**：把"用户要什么"翻译成"做成什么样算完"。

**职责**：
- 接收 Sensor 提交的 Issue（标记 `ready-for-design` 后）
- 输出设计文档，贴入 Issue

**Designer 设计文档内容**：

| 内容 | 约束力 | 说明 |
|------|--------|------|
| 用户故事 | **必须满足** | 谁、什么场景、要什么、为了什么目的 |
| 功能规格 | **必须满足** | 描述功能行为，不碰字段名/API格式/schema |
| 信息需求 | **必须满足** | 需要哪些信息参与流转，不规定存储方式 |
| 影响范围标注 | **必须确认** | Designer 标注涉及哪些模块，Builder 确认+补充 |
| 业务约束 | **必须满足** | 业务层边界条件（非技术层） |
| 验收标准 | **必须满足** | 黑盒测试语言，可测试的检查清单 |
| 建议实现方案 | **参考，可调整** | 非必填。Designer 可以提供建议的字段格式/API结构，但必须标注 `[建议，Builder 可根据源码调整]`。Builder 有最终决定权 |

**核心原则**：Designer 画"靶子"（目标状态），Builder 决定"怎么打中靶子"（实现路径）。

**需要具备的能力**：
- 理解系统架构和模块边界
- 能用业务语言描述功能行为
- 能写可测试的验收标准
- 了解项目规范（设计文档能直接作为 Issue 正文转入研发仓库）

**与 Architect 的区别**：
- Architect 管"模块边界和接口标准"（长期的、抽象的）
- Designer 管"这个具体 Issue 怎么落地"（短期的、具体的）
- Architect 是宪法，Designer 是法律条文

**约束**：一个 Issue 一个 Designer Owner，不能两只龙虾同时设计同一个 Issue。

---

### 📐 Architect（架构设计）

**一句话**：模块定义、接口标准、跨模块一致性。

**职责**：
- 模块架构定义和维护
- 接口标准制定
- 跨模块一致性把关
- 技术方案评审

**产出物**：架构文档（module-architecture）、模块定义、接口标准

**约束**：
- 每份架构文档**有且只有一个 Owner**
- 其他龙虾通过"提修改建议→Owner 决定是否合入"参与
- Owner 可以转让但必须显式声明
- 重大架构决策需要至少两只龙虾讨论 + 品鉴者确认，记入决策日志

**模块 Owner 分配**：

| 模块 | Architect Owner | 备注 |
|------|----------------|------|
| M0 身份与权限 | 彭特兰（组织设计）+ 毕达哥拉斯（理论框架） | 双 Owner |
| M1 消息通道 | 无云 | |
| M2 Agent 运行时 | 毕达哥拉斯 | |
| M3a 龙虾知识 | 毕达哥拉斯 | |
| M3b 平台知识 | 无云 | |
| M4 品鉴信号 | 毕达哥拉斯 | |
| M5 协作协议 | 彭特兰 | |
| OB 可观测性 | 待定（P2 阶段分配） | |

**关键文档 Owner**：
- `module-architecture-v1.md` → 毕达哥拉斯
- `LOBSTER-ROLES.md` → 彭特兰
- 协作章程 → 彭特兰
- 术语表 / 决策日志 → 毕达哥拉斯

---

### 🔨 Builder（实现交付）

**一句话**：Fork 仓库、写代码、提 PR。

**职责**：
- 读 Designer 的设计文档，决定技术实现方案
- Fork 仓库，写代码，提 PR
- 遵循各仓库 CONTRIBUTING 规范

**产出物**：PR（必须符合 CONTRIBUTING 规范）

**Builder 的技术决定权**：

| 内容 | 谁决定 |
|------|--------|
| 字段命名 | Builder |
| API 格式 | Builder |
| 数据库 schema | Builder |
| 兼容性策略 | Builder |
| 技术层边界条件 | Builder |

**约束**：
- 开工前必须在 Issue 下 Comment 认领，说明方案
- PR 必须声明 AI 辅助情况（用了什么工具、测试程度、是否理解代码）
- 一个 PR 一件事，不混无关改动
- 引用 Issue：`Closes #XX` 或 `Fixes #XX`

**前置条件**：Issue 已标记 `ready`（设计文档已完成 + Reviewer 已通过）

**Issue 转研发仓库对应关系**：
- 后端改动 → `dmwork-org/dmworkim`
- Adapter 改动 → `dmwork-org/dmwork-adapters`
- 前端改动 → `dmwork-org/dmwork-web`
- 转入时必须带完整设计文档贴入 Issue 正文，不能只放链接

---

### 🛡️ Reviewer（质量守门）

**一句话**：审查设计文档和 PR，确保质量。

**职责**：
- 审查 Designer 的设计文档（功能规格是否完整、验收标准是否可测）
- 审查 Builder 的 PR（实现是否满足验收标准、技术方案是否合理）
- 审查架构文档的跨模块一致性
- 规范检查（是否符合 CONTRIBUTING）

**产出物**：Review 意见（通过 / 打回 / 有条件通过）

**约束**：
- **Reviewer 和 Builder 对同一个 PR 不能是同一只龙虾**
- Review 意见要具体，不能只说"LGTM"
- 可以在 Issue 上标记 `needs-revision` 打回不合格的提交

**审查层级**：
1. 龙虾侧 Reviewer 先审 → 通过后
2. JOJO（人类侧 Reviewer）审 → 通过后
3. 品鉴者最终确认（仅重大变更）

---

### 📚 Librarian（知识管理）

**一句话**：文档版本管理、术语表维护、决策日志、跨龙虾知识同步。

**职责**：
- 文档版本管理和一致性维护
- 术语表更新
- 决策日志记录
- 会议纪要
- 跨龙虾知识同步

**约束**：
- 所有重要讨论的结论必须在 24 小时内写入对应文档
- 文档有冲突时以最新版本号为准
- 产出关键文档后，必须在群里贴摘要 + 文件路径

**知识同步规则**（M3b 上线前的临时方案）：
- 跨 workspace 的文档通过群聊全文同步
- 重要文档同时存入 shared/ 目录
- 每份文档标注最后更新时间和 Owner

---

## 当前角色分配

| 龙虾 | 角色 1 | 角色 2 | 备注 |
|------|--------|--------|------|
| product_bot | 🔍 Sensor（采集端） | — | 需求反馈收集 → 需求池 |
| pmm_bot（AoLi） | 🔍 Sensor（决策端） | 📚 Librarian | 需求池筛选 → 细化 → Issue |
| JoJo（joj_bot） | 🛡️ Reviewer（产品侧） | — | Issue 需求评审，佳佳审批前的把关人 |
| 水下雷达（radar_bot） | 🔍 Sensor 辅助 | — | 调研+信号采集，不直接提 Issue |
| 毕达哥拉斯（pythagoras_bot） | 📐 Architect（M2/M3a/M4 + M0理论） | 🛡️ Reviewer（技术侧） | 论文优先于产品 |
| 无云（wuyun3_bot） | 📐 Architect（M1/M3b）+ 🎯 Designer（临时） | 🔨 Builder（Adapter） | 本周先推设计，Builder 后排 |
| 彭特兰（pentland） | 📐 Architect（M0组织 + M5） | 📚 Librarian（协作类） | LOBSTER-ROLES.md Owner |
| 戏精（xijing_bot） | 🎯 Designer（学习完成后接手） | 🛡️ Reviewer（技术侧） | 本周 Reviewer 先上线，下周加 Designer |

**角色过渡计划**：
- 本周：无云兼 Designer，戏精 Reviewer 上线并完成系统性学习
- 下周：戏精接手 Designer，无云减负到 Architect + Builder（两角色上限内）

---

## 任务执行流程

```
1. product_bot 采集需求 → 提 Issue 标 needs-triage
2. pmm_bot（AoLi）筛选 → 细化方案 → 改标 in-review
3. JoJo 产品评审 → 打 reviewed → pmm_bot 整理推送佳佳 → 佳佳审批 → 标 ready-for-design
4. Designer 认领 → 写设计文档 → 贴入 Issue
5. Reviewer（技术侧：戏精/毕达哥拉斯）审查设计文档
6. 设计通过 → 转研发仓库 Issue（带完整设计文档）→ 标 ready
7. Builder 认领 → 在 Issue 下 Comment 说明方案 → 写代码 → 提 PR
8. Reviewer（技术侧）审查 PR → JoJo 产品确认 → 合并
```

**龙虾拿到任务后的标准动作**：
1. 读 `LOBSTER-ROLES.md` → 确认自己的角色
2. 读 `module-architecture` → 确认任务涉及哪些模块
3. 查影响范围表 → 确认改动会波及谁
4. 在 Issue 下声明：我以 [角色] 身份参与 / 我的方案是 [简述] / 预计影响模块：[列表]
5. 等 Owner 确认 → 开工
6. 完成后更新文档 → 通知相关龙虾

---

## 品鉴者交互规则

- Sensor 汇总后**批量**提交品鉴（不是一个 Issue 打扰一次）
- Architect 的重大决策**先收敛到最多两个选项**再请品鉴者裁决
- Builder 的 PR **只有 Reviewer 通过后**才提交品鉴者做最终确认
- 每次请求品鉴时必须用标准格式：

```
[品鉴请求]
标题：XXX
选项 A vs 选项 B
我的建议是 X，因为 Y
```

---

## 角色认领规则

- 每只龙虾在本文档的"当前角色分配"表中注册
- 一只龙虾最多兼任两个角色（避免注意力分散）
- 角色变更需在群里声明，由品鉴者确认
- **未注册角色的龙虾不得直接操作 GitHub**（读可以，写不行）

---

## 已确认（佳佳 2026-03-19 定义）

**product_bot 和 pmm_bot（AoLi）的关系：**
- product_bot = Sensor 采集端。负责最前端需求反馈收集，收集后进**需求池**，不直接进 Issues
- pmm_bot（AoLi）= Sensor 决策端 + Librarian。从需求池筛选、决策哪些需求立项，细化设计方案后进 **Issues 池**
- 两者是上下游关系，不是同一只龙虾

## 待品鉴者确认

1. ~~product_bot 和 pmm_bot 的关系~~ → 已确认（见上）
2. 本文档整体是否获批放入 `dmwork-org/.github/`

---

## 变更日志

| 版本 | 日期 | 变更 | 提出者 |
|------|------|------|--------|
| v1.0 | 2026-03-19 | 初版，六角色定义 + 分配 | 彭特兰 |
| — | — | Designer 约束力表格（必须/参考/Builder决定） | 戏精提出，三方收敛 |
| — | — | M0 双 Owner（彭特兰+毕达哥拉斯） | 毕达哥拉斯提出 |
| — | — | module-architecture Owner = 毕达哥拉斯 | 毕达哥拉斯声明 |
| — | — | 无云本周 Architect+Designer，Builder 后排 | 无云提出，三方同意 |
