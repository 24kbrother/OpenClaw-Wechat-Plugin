# OpenClaw WeChat Plugin

> 企业微信（WeCom）channel plugin for OpenClaw，支持消息接收、AI 回复、媒体处理和 API 代理。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js Version](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org/)

## 功能特性

### 核心功能
- ✅ 接收企业微信消息（文本、图片、语音）
- ✅ 自动调用 AI 代理处理消息
- ✅ 将 AI 回复发送回企业微信用户
- ✅ 消息签名验证和 AES 加密解密
- ✅ Webhook URL 验证（企业微信回调配置）
- ✅ access_token 自动缓存和刷新（支持多账户）
- ✅ **支持 API 代理**（用于绕过 IP 白名单限制）

### 媒体功能
- ✅ 图片消息接收和 AI 识别（Vision 能力）
- ✅ 图片消息发送
- ✅ 语音消息转文字（需开启企业微信语音识别）

### 用户体验
- ✅ 命令系统（/help、/status、/clear）
- ✅ Markdown 格式自动转换
- ✅ 长消息自动分段（2048 字符限制）
- ✅ API 限流保护

### 高级功能
- ✅ 多账户支持
- ✅ 群聊支持
- ✅ Token 并发安全
- ✅ **API 代理支持**（可选配置）

## 前置要求

- [OpenClaw](https://openclaw.ai) 已安装并配置
- 企业微信管理员权限
- 公网可访问的服务器（用于接收回调）
- Node.js 18+

## 快速开始

### 1. 安装依赖

```bash
cd OpenClaw-Wechat-Plugin-main
npm install
```

### 2. 配置 OpenClaw

在 `~/.openclaw/openclaw.json` 中添加插件路径和配置：

```json
{
  "plugins": {
    "enabled": true,
    "load": {
      "paths": [
        "/path/to/OpenClaw-Wechat-Plugin-main"
      ]
    }
  },
  "env": {
    "vars": {
      "WECOM_CORP_ID": "你的企业ID",
      "WECOM_CORP_SECRET": "你的应用Secret",
      "WECOM_AGENT_ID": "你的应用AgentId",
      "WECOM_CALLBACK_TOKEN": "你设置的Token",
      "WECOM_CALLBACK_AES_KEY": "你生成的EncodingAESKey",
      "WECOM_WEBHOOK_PATH": "/wecom/callback"
    }
  }
}
```

### 3. 配置企业微信

1. 登录 [企业微信管理后台](https://work.weixin.qq.com/wework_admin/frame)
2. 进入 **应用管理** → **自建** → **创建应用**
3. 记录 **AgentId** 和 **Secret**
4. 在管理后台首页记录 **企业ID (CorpId)**
5. 进入应用 → **接收消息** → **设置API接收**
6. 填写回调 URL、Token 和 EncodingAESKey

### 4. 启动 OpenClaw

```bash
# 重启 OpenClaw
pkill -f openclaw
openclaw
```

### 5. 验证配置

```bash
# 检查 webhook
curl http://localhost:18789/wecom/callback
# 应返回 "wecom webhook ok"

# 查看日志
tail -f /tmp/openclaw/openclaw-*.log | grep wecom
```

## API 代理配置（可选）

如果你的服务器 IP 不在企业微信白名单中，可以使用 API 代理。

### 使用代理服务

在 `~/.openclaw/openclaw.json` 中添加：

```json
{
  "env": {
    "vars": {
      "WECOM_API_PROXY": "http://你的代理服务器IP:3120",
      "WECOM_CORP_ID": "...",
      "WECOM_CORP_SECRET": "...",
      // ...其他配置
    }
  }
}
```

### 部署代理服务

请参考 [OpenClaw-Wechat-Proxy](https://github.com/yourusername/OpenClaw-Wechat-Proxy) 项目。

## 多账户配置

支持配置多个企业微信账户：

```json
{
  "env": {
    "vars": {
      "WECOM_CORP_ID": "默认账户企业ID",
      "WECOM_CORP_SECRET": "默认账户Secret",
      "WECOM_AGENT_ID": "默认账户AgentId",
      "WECOM_CALLBACK_TOKEN": "默认账户Token",
      "WECOM_CALLBACK_AES_KEY": "默认账户AESKey",

      "WECOM_SALES_CORP_ID": "销售账户企业ID",
      "WECOM_SALES_CORP_SECRET": "销售账户Secret",
      "WECOM_SALES_AGENT_ID": "销售账户AgentId",
      "WECOM_SALES_CALLBACK_TOKEN": "销售账户Token",
      "WECOM_SALES_CALLBACK_AES_KEY": "销售账户AESKey"
    }
  }
}
```

## 使用

配置完成后，企业微信用户可以直接向应用发送消息：

1. 在企业微信中找到你创建的应用
2. 发送文字、图片或语音消息
3. AI 会自动回复

### 命令系统

| 命令 | 说明 |
|------|------|
| `/help` | 显示帮助信息 |
| `/status` | 查看系统状态 |
| `/clear` | 清除会话历史 |

### 支持的消息类型

| 类型 | 接收 | 发送 | 说明 |
|------|------|------|------|
| 文本 | ✅ | ✅ | 完全支持，自动分段 |
| 图片 | ✅ | ✅ | 支持 Vision 识别 |
| 语音 | ✅ | ❌ | 需开启企业微信语音识别 |
| 视频 | ❌ | ❌ | 暂不支持 |
| 文件 | ❌ | ❌ | 暂不支持 |

## 环境变量说明

| 变量名 | 必填 | 说明 |
|--------|------|------|
| `WECOM_CORP_ID` | 是 | 企业微信企业ID |
| `WECOM_CORP_SECRET` | 是 | 自建应用的 Secret |
| `WECOM_AGENT_ID` | 是 | 自建应用的 AgentId |
| `WECOM_CALLBACK_TOKEN` | 是 | 回调配置的 Token |
| `WECOM_CALLBACK_AES_KEY` | 是 | 回调配置的 EncodingAESKey |
| `WECOM_WEBHOOK_PATH` | 否 | Webhook 路径，默认 `/wecom/callback` |
| `WECOM_API_PROXY` | 否 | API 代理地址 |

## 故障排查

### 回调验证失败

```bash
# 检查 URL 是否可公网访问
curl https://你的域名/wecom/callback
# 应返回 "wecom webhook ok"

# 查看日志
tail -f /tmp/openclaw/openclaw-*.log | grep wecom
```

### 消息没有回复

1. 检查日志中是否有 `wecom inbound` 记录
2. 确认 AI 模型配置正确
3. 检查是否有错误日志

### access_token 获取失败

1. 确认 `WECOM_CORP_ID` 和 `WECOM_CORP_SECRET` 正确
2. 检查应用的可见范围是否包含测试用户
3. 确认服务器能访问 `qyapi.weixin.qq.com`

### IP 白名单错误 (60020)

如果看到错误：
```
{"errcode":60020,"errmsg":"not allow to access from your ip"}
```

请在企业微信管理后台添加你的服务器 IP 到白名单。

如果无法固定 IP，请使用 API 代理服务：
1. 部署 [OpenClaw-Wechat-Proxy](https://github.com/yourusername/OpenClaw-Wechat-Proxy)
2. 配置 `WECOM_API_PROXY` 环境变量

## 技术实现

- **消息加解密**：使用 AES-256-CBC 算法，遵循企业微信加密规范
- **签名验证**：SHA1 签名验证，防止消息伪造
- **异步处理**：消息接收后立即返回 200，异步调用 AI 处理
- **Token 缓存**：access_token 按账户隔离缓存，过期前 1 分钟刷新
- **并发安全**：Promise 锁防止重复刷新 token
- **API 限流**：RateLimiter 控制并发和频率
- **代理支持**：可选的 API 代理，用于绕过 IP 白名单限制

## 相关项目

- [OpenClaw-Wechat-Proxy](https://github.com/yourusername/OpenClaw-Wechat-Proxy) - API 代理服务

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request！
