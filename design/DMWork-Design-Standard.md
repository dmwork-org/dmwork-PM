# DMWork v4 设计标准

> 由花花（三妹 🌸）维护，所有新页面/组件设计请参照此标准。
> 完整交互展示页：`design-system.html`

---

## 品牌色

- **渐变**：`#7C5CFC → #00D4AA` (135deg)，紫→青
- **主色**：`#7C5CFC`
- **用途**：Logo、AI 标签、发送按钮、active 指示条、AI 角标

---

## 背景层级

### 亮色（默认）

| Token | 值 | 用途 |
|-------|-----|------|
| `--bg-deep` | `#F0F1F5` | Nav Rail、页面底层 |
| `--bg-base` | `#F7F8FA` | Sidebar |
| `--bg-surface` | `#FFFFFF` | Chat 主区域 |
| `--bg-elevated` | `#F0F1F5` | 气泡、输入框 |
| `--bg-hover` | `#EAEBF0` | Hover 态 |
| `--bg-active` | `#E2E3EA` | Active/选中态 |
| AI 面板 | `#FAFAFF` | 微紫白 |

### 暗色

| Token | 值 | 用途 |
|-------|-----|------|
| `--bg-deep` | `#111318` | Nav Rail、页面最深层 |
| `--bg-base` | `#171921` | Sidebar、AI 面板 |
| `--bg-surface` | `#1E212B` | Chat 主区域 |
| `--bg-elevated` | `#262A36` | 气泡、输入框、卡片 |
| `--bg-hover` | `#2E3240` | Hover 态 |
| `--bg-active` | `#363B4A` | Active/选中态 |

---

## 文字颜色

| 用途 | 暗色 | 亮色 |
|------|------|------|
| Primary | `#E4E6ED` | `#1A1C24` |
| Secondary | `#9CA1B3` | `#5C6070` |
| Tertiary（仅时间戳等装饰信息） | `#7A7F96` | `#9498A8` |
| Accent | `#B0A4FF` | `#6B4FD8` |

---

## 语义色

| 语义 | 暗色 | 亮色 |
|------|------|------|
| Green（在线/成功） | `#00D4AA` | `#00B894` |
| Amber（警告） | `#FFAD33` | `#E6930A` |
| Red（错误/未读） | `#FF5C72` | `#E5405E` |
| Blue（信息） | `#5C9AFF` | `#4A85E6` |

---

## 字体

- **Sans**：Inter + PingFang SC / Noto Sans SC
- **Mono**：JetBrains Mono
- **字阶**：H1(28) → H2(22) → H3(16) → H4(14) → Body(14) → Caption(12) → Tiny(10) → Code(13) → Mono-sm(11)

---

## 间距 & 圆角

- **间距**（4px grid）：sp-1=4, sp-2=8, sp-3=12, sp-4=16, sp-5=20, sp-6=24, sp-8=32, sp-10=40, sp-12=48
- **圆角**：r-xs=4, r-sm=6, r-md=10, r-lg=14, r-xl=20, r-full=9999

---

## 头像身份规则（核心！）

| 类型 | 形状 | 说明 |
|------|------|------|
| **人类** | 圆形 `border-radius: 50%` | — |
| **AI** | 圆角方形 `r-sm=6px` | 渐变色背景 + 右下角 14px 渐变角标（闪电图标） |
| **群组** | 大圆角方形 `r-md=10px` | — |

---

## 消息视觉语言（核心！）

### 人类消息
- 气泡样式：`inline-block`, `bg-elevated`, 圆角 `2px/r-lg/r-lg/r-lg`

### AI 消息
- 面板样式：`block`, `max-width: 680px`, `ai-panel-bg`, `r-lg`
- 顶部 2px 品牌渐变线
- 头部：Agent 名 + AI 标签(渐变) + 模型标签(mono)
- 内容区：`line-height: 1.75`，更宽松
- 底栏：token/耗时 + 操作按钮（hover 显现）

### AI 思考
- 步骤轨道：done/active/pending 三态，连接线，脉冲动画

---

## 输入框

- **普通模式**：`bg-elevated`, `r-xl` 圆角
- **AI 模式**（@AI 后）：渐变边框 + Agent 上下文条 + placeholder 变化

---

## 布局

- **四栏**：Nav Rail(60px) + Sidebar(280px) + Chat(flex) + Task Rail(320px, 可收起)
- **Chat Head**：毛玻璃 `backdrop-filter: blur(20px)`

---

## 图标规范

- **风格**：全部 SVG stroke icons，不混用 emoji
- **参数**：`stroke-width: 1.6~1.8`, `stroke-linecap/linejoin: round`, `fill: none`
- **尺寸**：Nav 按钮 20px / Tab 小按钮 14~16px / 结果列表 16~18px
- **颜色**：跟随 `currentColor`
- **图标源**：<https://www.iconfont.cn/>，保持风格一致

---

## 动效

- **ease**：`cubic-bezier(0.16, 1, 0.3, 1)`
- **duration**：200ms（常规）/ 150ms（快速）/ 350ms（慢速）

---

## 参考页面

| 页面 | 文件 |
|------|------|
| 设计系统展示（22章） | `design-system.html` |
| 聊天页（核心） | `chat.html` |
| 登录/注册页 | `login.html` |
| 会话列表 | `conversation-list.html` |
| 搜索 | `search.html` |

---

*最后更新：2026-03-10*
