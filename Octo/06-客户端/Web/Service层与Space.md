---
title: Web Service 层与 Space
type: reference
project: dmwork-web
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, web, service, space, wkapp, dmwork-web]
---

# Web Service 层与 Space

> WKApp 全局单例 · APIClient · EndpointManager · ModuleManager · Space 工作空间。

[[组件与消息渲染|← 组件与消息渲染]] | [[概述|← Web 概述]]

## 概述

`@dmwork/base` 的 Service 层是整个 Web 客户端的业务逻辑核心，以 `WKApp` 全局单例为枢纽，通过 `EndpointManager` 实现插件化扩展，通过 `ModuleManager` 管理模块生命周期。

## WKApp — 全局单例

`WKApp`（`packages/dmworkbase/src/App.tsx`）是应用的"大脑"，持有所有顶层服务引用：

```typescript
class WKApp extends ProviderListener {
    static shared = new WKApp()
    
    // 路由管理
    static route      = RouteManager.shared           // 全局路由
    static routeLeft  = new ContextRouteManager()     // 左栏路由（会话列表区）
    static routeRight = new ContextRouteManager()     // 右栏路由（主内容区）
    
    // 服务层
    static menus           = MenusManager.shared      // 菜单管理
    static apiClient       = APIClient.shared         // HTTP 客户端（axios）
    static config: WKConfig                           // App 配置（主题、系统UID等）
    static remoteConfig: WKRemoteConfig               // 远程配置（撤回时间等）
    static loginInfo: LoginInfo                       // 登录信息（token、uid）
    static endpoints: EndpointCommon                  // 常用端点快捷方式
    static conversationProvider: IConversationProvider // 消息数据源
    static messageManager: MessageManager             // 消息类型注册表
    static emojiService: EmojiService                 // Emoji 服务
    static sectionManager: SectionManager             // Section 扩展系统
    static dataSource: DataSource                     // 联系人/频道数据源
    static endpointManager: EndpointManager           // 端点管理（插件系统核心）
    static mittBus = mitt()                           // 轻量事件总线
    
    // 运行时状态
    currentSpaceId: string   = ""     // 当前选中 Space ID
    spaceChecked:   boolean  = false  // Space 检查完成
    isPC:           boolean  = false  // 是否 Electron 桌面端
    openChannel?: Channel             // 当前打开的会话
    baseContext!: WKBaseContext       // WKBase 全局弹窗上下文
}
```

### 启动流程（`WKApp.startup()`）

```
1. 加载登录信息（localStorage）
2. 检测运行环境（Electron / Tauri / Web）
3. 获取设备 ID（持久化 UUID）
4. 读取主题模式偏好（light/dark）
5. 配置 WuKongIM WebSocket 连接地址回调
6. 注册 "onLogin" 端点（登录成功后启动主流程）
7. 监听连接状态：
   - Kick / AuthFail → 自动登出
   - Disconnect → 轮换 WebSocket 地址（EndpointManager）
```

## APIClient — HTTP 客户端

```typescript
class APIClient {
    static shared = new APIClient()
    
    config = new APIClientConfig()
    // config.apiURL        → API 基础地址
    // config.tokenCallback → 获取当前 token 的回调
    
    logoutCallback?: () => void  // 401 时触发
}
```

**请求拦截器**：
```typescript
axios.interceptors.request.use(config => {
    config.headers['Authorization'] = `Bearer ${tokenCallback()}`
    return config
})
```

**响应拦截器**：
- `401` → 调用 `WKApp.shared.logout()`
- `400` → 透传 `error.response.data.msg` 给调用方
- 泛型 `wrapResult<T>` 自动将响应 `fill()` 到 `APIResp` 对象

## EndpointManager — 插件系统核心

这是整个扩展性设计的核心，一个**键值注册表 + 分类批量调用**的设计：

```typescript
class EndpointManager {
    // 注册单个端点
    register(endpoint: Endpoint): void
    setMethod(sid: string, handler, config?: { category?, sort? }): void
    
    // 单个调用
    invoke(sid: string, param?: any): any
    
    // 按分类批量调用（返回所有注册者的结果）
    invokes<T>(category: string, param?: any): T[]
    
    // 注销
    removeMethod(sid: string): void
}
```

### 预定义端点分类（EndpointCategory）

| 分类常量 | 用途 | 调用方式 |
|----------|------|----------|
| `chatToolbars` | 聊天工具栏按钮（Emoji/图片/文件/@） | `invokes`（渲染全部） |
| `channelSetting` | 频道设置 Section | `invokes`（渲染全部） |
| `messageContextMenus` | 消息右键菜单项（复制/转发/回复/撤回） | `invokes`（渲染全部） |
| `routes` | 路由注册（`/`、`/contacts`、`/login`、`/bots`） | `invokes`（注册全部路由） |
| `menus` | 左侧导航菜单（会话/通讯录/Bot） | `invokes`（渲染全部） |
| `userInfo` | 用户信息页 Section 扩展 | `invokes` |
| `conversationToolbars` | 会话列表工具栏 | `invokes` |

### 注册示例

```typescript
// 注册聊天工具栏按钮
WKApp.endpointManager.setMethod("chat.toolbars.emoji",
    () => <EmojiToolbar />,
    { category: EndpointCategory.chatToolbars, sort: 10 }
)

// 注册路由
WKApp.endpointManager.setMethod("route./contacts",
    () => <ContactsList />,
    { category: EndpointCategory.routes }
)
```

## ModuleManager — 模块生命周期

```typescript
class ModuleManager {
    static shared = new ModuleManager()
    
    register(module: IModule): void {
        this.moduleMap.set(module.id(), module)
        module.init()  // 立即初始化
    }
}

interface IModule {
    id():   string   // 唯一模块 ID
    init(): void     // 初始化（注册端点、路由、消息类型等）
}
```

### 已注册的模块

| 模块 | ID | 职责 |
|------|-----|------|
| `BaseModule` | `"base"` | 注册所有消息类型、CMD 监听、菜单、工具栏、频道设置 |
| `LoginModule` | `"login"` | 注册 `/login` 路由 |
| `ContactsModule` | `"contacts"` | 注册通讯录路由和端点 |
| `DataSourceModule` | `"datasource"` | 绑定数据源实现到 `WKApp` |

## RouteManager — 路由系统

基于 `EndpointManager` 的轻量路由，左右两栏各有独立 `ContextRouteManager`：

```typescript
class ContextRouteManager {
    push(path: string): void      // 导航到新路由（入栈）
    pop(): void                   // 返回上一页
    popToRoot(): void             // 返回根路由
    get(path: string): JSX.Element // 获取路由对应组件
}
```

类似移动端的 `UINavigationController`，支持多层嵌套导航。

## Space 功能

### Space 工作空间

Space 是 DMWork 的**多租户隔离单元**，类似 Slack Workspace / Discord Server：

```
用户加入 Space
    │
    ▼
SpaceGate（鉴权门卫）
    │── 无已选 Space → SpaceList（选择或加入 Space）
    └── 有已选 Space → MainPage（主界面）
```

### Space 相关组件

| 组件 | 功能 |
|------|------|
| `SpaceGate` | Space 入口守卫，鉴权后决定跳转 |
| `SpaceCreate` | 创建新 Space（名称、描述、Logo、预设群组） |
| `SpaceList` | Space 列表，支持加入和切换 |
| `SpaceMembers` | 成员管理（邀请/移除/角色设置） |
| `SpaceSettings` | Space 设置（改名/描述/邀请链接/离开/解散） |

### Space 感知数据同步

```typescript
// mitt 事件总线监听 Space 切换
WKApp.mittBus.on('space-changed', (spaceId: string) => {
    WKApp.shared.currentSpaceId = spaceId
    
    // 重新加载：联系人列表
    contactsListManager.reload()
    
    // Space 模式下联系人来源
    if (spaceId) {
        // 从 /v1/space/{id}/members 获取成员作为联系人
    } else {
        // 传统模式：从 friend/sync 获取好友
    }
})
```

### Space API 映射（CORRECTED 路径）

> ⚠️ **CORRECTED**：路径是 `/v1/space/`（无 s），非 `/v1/spaces/`

| 操作 | 前端调用 | API 端点 |
|------|----------|----------|
| 获取 Space 列表 | `WKApp.apiClient.get('/v1/space/my')` | `GET /v1/space/my` |
| 创建 Space | `WKApp.apiClient.post('/v1/space/create', data)` | `POST /v1/space/create` |
| 获取成员 | `WKApp.apiClient.get('/v1/space/{id}/members')` | `GET /v1/space/:id/members` |

## SectionManager — 设置页扩展

用于频道设置、用户信息页等动态 Section 场景：

```typescript
class SectionManager {
    // 注册：category 决定在哪个页面使用
    register(category: string, sectionID: string, 
             sectionFnc: (ctx) => Section, sort?: number): void
    
    // 获取该分类下所有 Section（按 sort 排序）
    sections(category: string, context: any): Section[]
}

// Section 结构
class Section {
    title?:    string
    rows?:     Row[]
    subtitle?: string
}

class Row {
    cell:       ElementType  // 渲染组件
    properties?: any         // props
    sort:        number      // 排序权重
}
```

## MessageManager — 消息类型注册表

```typescript
class MessageManager {
    // 按 ContentType 注册渲染 Cell
    registerCell(contentType: number, handler: () => ElementType): void
    
    // 注册更灵活的工厂（支持条件逻辑）
    registerMessageFactor(factor: (ct: number) => ElementType | undefined): void
    
    // 查找渲染器（优先 Cell，其次 Factor）
    getCell(contentType: number): ElementType
}
```

## DataSource 层

### 接口与实现分离

```
@dmwork/base（接口定义）
    IChannelDataSource   → 群组/订阅者/二维码操作
    ICommonDataSource    → 好友/收藏/贴图等通用操作
    IConversationProvider→ 消息同步/撤回/编辑/未读/删除

@dmwork/datasource（具体实现）
    ChannelDataSource    implements IChannelDataSource
    CommonDataSource     implements ICommonDataSource
    ConversationProvider implements IConversationProvider
```

所有 API 调用通过 `WKApp.apiClient` 发起，由 `DataSourceModule` 在 `init()` 时将实现绑定到 `WKApp.dataSource`。

## 相关页面

- [[概述|06-客户端/Web/概述]]
- [[组件与消息渲染|06-客户端/Web/组件与消息渲染]]
- [[04-服务端/模块/space|04-服务端/模块/space]]
- [[架构概述|02-架构/架构概述]]

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初始版本；CORRECTED: Space API 路径为 /v1/space/（无 s） |
