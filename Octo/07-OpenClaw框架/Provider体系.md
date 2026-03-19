---
title: Provider 体系
type: reference
project: openclaw
version: "0.1.0"
created: 2026-03-19
updated: 2026-03-19
authors: [戏精]
status: draft
tags: [octo, openclaw, provider, model, ai]
---

# Provider 体系

[[概述|← 返回概述]]

Provider 系统负责将 AI 模型请求路由到各个模型提供商。

## 模型引用格式

```
provider/model

示例：
  anthropic/claude-opus-4-6
  openai/gpt-5.4
  google/gemini-3.1-pro-preview
  zai/glm-5
  openrouter/anthropic/claude-opus-4-6
```

## 内置 Provider（无需额外插件，只需 Auth）

| Provider | 标识符 | 认证方式 | 代表模型 |
|----------|--------|---------|---------|
| OpenAI | `openai` | API Key | `openai/gpt-5.4` |
| Anthropic | `anthropic` | API Key / setup-token | `anthropic/claude-opus-4-6` |
| OpenAI Codex (OAuth) | `openai-codex` | ChatGPT OAuth | `openai-codex/gpt-5.4` |
| OpenCode Zen | `opencode` | `OPENCODE_API_KEY` | `opencode/claude-opus-4-6` |
| OpenCode Go | `opencode-go` | `OPENCODE_ZEN_API_KEY` | `opencode-go/kimi-k2.5` |
| Google Gemini | `google` | `GEMINI_API_KEY` | `google/gemini-3.1-pro-preview` |
| Google Vertex | `google-vertex` | gcloud ADC | — |
| Z.AI (GLM) | `zai` | `ZAI_API_KEY` | `zai/glm-5` |
| OpenRouter | `openrouter` | `OPENROUTER_API_KEY` | `openrouter/anthropic/...` |
| Kilo Gateway | `kilocode` | `KILOCODE_API_KEY` | `kilocode/anthropic/...` |
| xAI | `xai` | `XAI_API_KEY` | — |
| Mistral | `mistral` | `MISTRAL_API_KEY` | `mistral/mistral-large-latest` |
| Groq | `groq` | `GROQ_API_KEY` | — |
| Cerebras | `cerebras` | `CEREBRAS_API_KEY` | — |
| GitHub Copilot | `github-copilot` | GitHub Token | — |
| Hugging Face | `huggingface` | `HF_TOKEN` | `huggingface/deepseek-ai/...` |
| Vercel AI Gateway | `vercel-ai-gateway` | `AI_GATEWAY_API_KEY` | `vercel-ai-gateway/anthropic/...` |

## 通过插件启用的 Provider

| Provider | 插件 Extension | 认证方式 |
|----------|--------------|---------|
| Ollama（本地） | `ollama` | 无（本地服务） |
| vLLM（本地/自托管） | `vllm` | 可选 API Key |
| SGLang（本地/自托管） | `sglang` | 可选 API Key |
| Google Gemini CLI | `google-gemini-cli-auth` | OAuth 设备码流程 |
| Minimax Portal | `minimax-portal-auth` | OAuth |
| Qwen Portal | `qwen-portal-auth` | OAuth 设备码流程 |
| GitHub Copilot Proxy | `copilot-proxy` | OAuth |

## 自定义 Provider（OpenAI/Anthropic 兼容）

通过 `models.providers` 配置任何兼容端点：

```json5
{
  models: {
    providers: {
      lmstudio: {
        baseUrl: "http://localhost:1234/v1",
        apiKey: "LMSTUDIO_KEY",
        api: "openai-completions",  // 或 "anthropic-messages"
        models: [{ id: "minimax-m2.5", name: "MiniMax M2.5" }]
      },
      kimi: {
        baseUrl: "https://api.moonshot.cn/v1",
        apiKey: "MOONSHOT_KEY",
        api: "openai-completions",
        models: [{ id: "moonshot-v1-128k", name: "Kimi 128K" }]
      }
    }
  }
}
```

已知支持的自定义 Provider：
- Moonshot (Kimi)
- MiniMax
- Volcano Engine（火山引擎）
- BytePlus
- Synthetic
- LM Studio
- LiteLLM

## API Key 轮换机制

当有多个 Key 时，遇到 429（速率限制）自动切换：

```
优先级：
  1. OPENCLAW_LIVE_<PROVIDER>_KEY
  2. <PROVIDER>_API_KEYS（逗号分隔多个 Key）
  3. <PROVIDER>_API_KEY
  4. <PROVIDER>_API_KEY_1, _2, _3...

规则：
  - 收到 429 → 切换到下一个 Key
  - 其他错误 → 立即失败（不切换）
```

## Provider 优先级与 Failover

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-opus-4-6",
        fallbacks: [
          "google/gemini-3.1-pro-preview",
          "openai/gpt-5.4"
        ]
      }
    }
  }
}
```

## 模型管理命令

```bash
# 列出所有可用模型
openclaw models list

# 设置主模型
openclaw models set openai/gpt-5.4

# 登录 Anthropic
openclaw models auth login --provider anthropic

# 配置 Gemini API Key
openclaw onboard --auth-choice gemini-api-key
```

## 相关文档

- [[Plugin系统]] — Provider 插件加载机制
- [[Session管理]] — 模型在 session 级别的覆盖

---

## CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.0 | 2026-03-19 | 初稿 |
