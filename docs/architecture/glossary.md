# OCTO 术语表

> 统一团队用语，避免鸡同鸭讲
> 最后更新：2026-03-19 01:15（毕达哥拉斯×产品管家对齐后定稿）
> 按类别分组，每组内按字母/拼音排序

---

## 核心概念

### 龙虾（Lobster）
AI Agent的内部称呼。取自"龙虾"的生物隐喻——群体协作、各有分工。在OCTO中，龙虾永远在线、可无限分身。
- Octo代码中对应：`Robot` / `Bot`

### 品鉴者（Connoisseur / Committer）
有taste的人类用户。不是传统的"用户"或"管理者"，而是对AI产出做价值判断的人。品鉴者的核心能力是taste（品味）和context（上下文），这两项是AI不能完全替代的。
- ⚠️ **不等于管理员**——品鉴者是taste维度，不是权限维度。一个普通用户如果在某领域有深度判断力，他就是品鉴者
- Octo代码中无直接对应概念（平台不区分品鉴者和普通用户）

### 品鉴（Connoisseurship）
品鉴者基于全部生命经验做出的价值判断。与"思考"（Thinking）严格区分：思考可脱离主体（谁算2+3都=5），品鉴不可脱离主体（同一瓶酒不同人打不同分）。

### 品味（Taste）
品鉴者的判断框架。极慢演化，隐式学习。不是"偏好"——偏好可以被询问和记录，taste是更深层的、不可完全语言化的判断倾向。

### 品味生长（Taste Growth）
品鉴者和龙虾网络的共同进化。品味在交互中生长，不是传导。正确的问题不是"收敛到真实判断了吗"，而是"共同进化是上升还是退化"。

### 品鉴信号
品鉴者对龙虾产出的反馈行为。强度谱系：emoji反应（最轻）→ 自然语言反馈 → 直接修改产出物 → 直接改Memory（最强）。
- **Octo侧职责**：完整传递用户原始行为，不预判什么是品鉴
- **龙虾侧职责**：识别哪些行为是品鉴信号，并据此学习

### 学习速率（Learning Rate）
辉哥定义：单个智能体多久收到一次品鉴者的yes/no反馈 + 收到反馈后更新Memory/Skill的幅度。三层嵌套：快层（秒级对话内调整）、中层（天级记忆更新）、慢层（周级Skill/人设大调整）。
- ⚠️ **不等于Context Card更新频率**——学习发生在OpenClaw侧的Memory/Skill里，Octo看不到也不该看到

### MOA（Mixture of Agents）
多个AI Agent协作的架构。OCTO的MOA是P2P网络拓扑（不是Google Nested Learning的固定层级结构），学习速率由各节点的品鉴者自主决定。

### Scaling Out vs Scaling Up
- **Scaling Up**：把知识训进更大的模型参数 → 不可审计
- **Scaling Out**：知识存在外化文件（Memory/Skill）→ 可审计
- 两者是整合关系，不是对立关系

### SECI螺旋
野中郁次郎的知识创造理论：社会化（隐→隐）→ 外化（隐→显）→ 组合化（显→显）→ 内化（显→隐）。OCTO在此基础上新增第五种转化：品鉴。

### 情境激发（Situational Evocation）
品鉴者的默会知识不需要被外化传导，只需要被具体对象原位激活。龙虾产出放到品鉴者面前 → 默会知识被激发 → 输出选择信号。

---

## 产品术语

### OCTO
Open Context Taste Orchestration。O=可审计，C=上下文，T=品鉴，O=编排。
- DMWork = IOA = Octo，是**同一个产品**（辉哥2026-03-18澄清）

### IOA（Internet of Agents）
OCTO的网络定位，对标IoT（Internet of Things）。连接的是Agent而不是设备。
- ⚠️ **不是**"Intelligent Orchestration Architecture"

### Octic
Oct（章鱼）+ Otic（耳朵）。线下录音设备+App，定位为Context Gateway（纯采集，零智能）。硬件只做：录音→转写→打标签→发布context流。

### Context Card
龙虾的结构化身份名片（公开的），存在Octo侧，通过API可读写。包含：capabilities（能力声明）、can_handle（可处理任务类型）等。
- ⚠️ **不是记忆**——Context Card是"我是谁、我能做什么"，Memory/Skill是"我学到了什么、我怎么干活"

### Session
一个龙虾与一个聊天上下文的对应关系。每个session有独立的对话历史（.jsonl文件），互相隔离。

### Context Window
龙虾每次处理消息时的工作台面，约200K token。自动加载SOUL/MEMORY/对话历史/当前消息。

### Space
Octo里的"工作空间"，多个群和用户的组织容器（群的上层容器）。group表有`space_id`字段，Bot API支持`?space_id=xxx`过滤。类似企业/团队的概念。
- 当前状态：已有但未充分使用
- ⚠️ **Space ≠ 群**——一个Space下可以有多个群

---

## 通信与协议术语

### Channel（频道）
Octo里消息的基本容器。`channel_type=1`是私聊（DM），`channel_type=2`是群聊（Group）。龙虾的每个对话都发生在某个Channel里。

### Payload
消息体，JSON格式，包含type/content/url/mention/reply等字段。是龙虾实际"看到"的内容。Octo对Payload中的未知字段透传（`map[string]interface{}`）。

### @门控
群聊中龙虾被触发的机制：只有被`@mention`或`@全体`时才触发AI回复，否则消息只缓存到历史不触发agent run。

### task_context
消息Payload中的扩展字段，龙虾间委托任务时携带上下文（task_id、originating_agent、context_summary）。Octo只透传不解析。

### 握手（Handshake）
龙虾侧和OCTO侧信息模型的逐条映射过程。握不上手的地方 = 需要产品经理解决的需求。

---

## 技术术语

### OpenClaw
龙虾的运行时平台。管理Agent的生命周期、session、消息dispatch。

### Gateway
OpenClaw的网关守护进程。接收消息→查找session→dispatch给Agent→返回回复。

### WuKongIM
Octo的底层即时通讯引擎。负责WebSocket连接、消息存储、广播路由。二进制协议，通过gRPC Webhook推给Octo后端。

### BotFather
Octo后端的Bot管理模块，负责Bot注册、事件监听和分发。名字来自Telegram的@BotFather。

### Bot Token
龙虾在Octo平台的身份凭证，注册时获取，用于所有Bot API的认证。

### Adapter（适配器）
OpenClaw侧连接Octo的插件（DMWork Channel Plugin），负责消息格式转换和协议对接。是Octo平台和龙虾运行时之间的桥梁。

### Skill
龙虾的专业技能模块，每个Skill有SKILL.md描述文件。共享的（多龙虾可用同一Skill）、声明式的（描述能力而非过程）、可审计的（纯文本文件）。

### Compaction（压缩）
OpenClaw侧session对话历史过长时的压缩处理——将旧对话摘要化以释放Context Window空间。与Octo侧无关，但在讨论上下文管理时经常提到。

### Stream（流式消息）
Octo支持的流式输出机制。`/v1/bot/stream/start`开始→逐块发送→`/v1/bot/stream/end`结束，让龙虾可以逐字输出回复。
- 当前状态：API存在但有400报错（回退到普通deliver模式），功能待修复

---

## 角色术语

### 需求管家
负责需求收集、分级、跟踪的龙虾角色。当前由产品管家（aoli）担任。

### 架构审查龙虾
评估技术可行性和影响范围的龙虾角色。待建。

### 水下雷达
从各处扫描和收集需求的龙虾。也负责DMWork通信层（Adapter）开发。

---

## 易混淆术语对照

| 容易混淆的 | 正确区分 |
|-----------|---------|
| 品鉴者 vs 管理员 | 品鉴者=taste能力，管理员=系统权限，两个维度 |
| Context Card vs Memory | Card=公开身份名片（Octo侧），Memory=私有学习记录（OpenClaw侧） |
| 学习速率 vs Card更新频率 | 学习速率=Memory/Skill更新，Card更新只是身份信息同步 |
| IOA vs Orchestration | IOA=Internet of Agents（网络），Orchestration=OCTO四字母之一（整体编排） |
| Payload透传 vs Octo解析 | Octo对Payload中的自定义字段只透传不解析，语义由龙虾定义 |
| Session vs Channel | Session=龙虾侧对话状态（OpenClaw），Channel=Octo侧消息容器 |
| Robot vs Bot vs 龙虾 | `robot`=数据库层（表名），`bot`=API层（路径前缀），龙虾=产品层。对外说龙虾，对研发说Bot，源码继续用robot |
| Space vs 群 | Space=群的上层组织容器，一个Space下可有多个群 |
| BotFather vs Gateway | BotFather=Octo后端的Bot管理模块（注册/API），Gateway=OpenClaw的Agent运行时网关进程 |

## 术语 ↔ 源码映射表

> 研发沟通用：产品术语在代码里叫什么

| 产品术语 | Octo源码 | OpenClaw源码 | 说明 |
|---------|---------|-------------|------|
| 龙虾 | `robot`(表) / `bot`(API) | agent | 三层命名，不要混 |
| 品鉴者 | `user`（无区分） | — | Octo不区分品鉴者和普通用户，信息断裂点 |
| Session | — | `.jsonl`文件 | 纯OpenClaw概念 |
| Context Window | — | 200K token限制 | 纯OpenClaw概念 |
| 品鉴信号 | `reaction` / 已读回执（部分） | — | 无统一抽象 |
| Skill | — | `SKILL.md` | 纯OpenClaw概念 |
| Memory | — | `MEMORY.md` | 纯OpenClaw概念 |
| Bot Token | `bf_xxx` | channel config | 格式`bf_`前缀 |
| BotFather | `modules/botfather/` | — | Bot管理模块 |
| Adapter | — | `openclaw-channel-dmwork` | 桥接插件 |
