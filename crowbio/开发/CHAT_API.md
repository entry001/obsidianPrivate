# Chat API 接口文档

基于 `app/routes/chat.py` 对外暴露的接口说明。所有接口前缀：**`/api/v1/chat`**。

**Base URL 示例**：`http://localhost:8000/api/v1/chat`

---

## 目录

1. [流式聊天 (SSE)](#1-流式聊天-sse)
2. [同步聊天](#2-同步聊天)
3. [获取会话历史](#3-获取会话历史)
4. [写入会话消息](#4-写入会话消息)
5. [测试连接](#5-测试连接)

---

## 1. 流式聊天 (SSE)

**POST** `/sse`

基于 Server-Sent Events 的流式多 Agent 对话。Orchestrator 进行意图识别与路由，按识别结果调度子 Agent，通过 SSE 实时返回内容。

### 请求体 (JSON)

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| message | string | 是 | 用户消息内容，长度 1–5000 |
| session_id | string | 否 | 会话 ID，不传则使用 `"default"` |
| user_id | string | 否 | 用户 ID（Bedrock 用于 actor_id） |

**示例**

```json
{
  "message": "帮我查一下项目 A 的报表",
  "session_id": "session_123456",
  "user_id": "user_001"
}
```

### 响应

- **Content-Type**：`text/event-stream`
- **Headers**：`Cache-Control: no-cache`，`Connection: keep-alive`，`X-Accel-Buffering: no`

### SSE 事件类型

| type | 说明 |
|------|------|
| content | 内容片段，`data` 为当前片段文本，可能带 `agent_type` |
| done | 流结束，`data`: `{"status": "completed"}` |
| state | 参数/UI 刷新通知（parameter_collector / file_recognizer 后），前端可刷新 UI；会话/参数详情由业务后端提供 |
| error | 错误，`data`: `{"message": "错误信息"}` |

### 错误

- **500**：处理异常，响应体为 `{"detail": "错误信息"}`

---

## 2. 同步聊天

**POST** `/sync`

非流式聊天，等待完整回复后一次性返回。

### 请求体 (JSON)

与 [流式聊天](#1-流式聊天-sse) 相同。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| message | string | 是 | 用户消息内容，1–5000 字符 |
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |

### 响应体 (JSON)

| 字段 | 类型 | 说明 |
|------|------|------|
| content | string | Agent 完整回复内容 |
| agent_type | string | 使用的 Agent 类型 |
| session_id | string | 会话 ID |
| routing_info | object | 路由信息（agent_type、confidence、reasoning 等） |
| refresh_ui | boolean | 为 true 时表示参数或会话有更新，前端可刷新 UI（详情由业务后端提供） |
| params_updated | boolean | 为 true 时表示参数/会话状态已更新 |

**示例**

```json
{
  "content": "已根据您的条件生成报表...",
  "agent_type": "report_generator",
  "session_id": "session_123456",
  "routing_info": {
    "agent_type": "report_generator",
    "confidence": 0.8,
    "reasoning": "通过Orchestrator处理"
  },
  "refresh_ui": false,
  "params_updated": false
}
```

### 错误

- **500**：处理异常

---

## 3. 获取会话历史

**GET** `/sessions/{session_id}/history`

根据会话 ID 获取历史对话。短期记忆来自 Bedrock Agent Core 或当前会话状态。

### 路径参数

| 参数 | 类型 | 说明 |
|------|------|------|
| session_id | string | 会话 ID |

### 查询参数

| 参数 | 类型 | 必填 | 默认 | 说明 |
|------|------|------|------|------|
| user_id | string | 否 | - | 用户 ID（Bedrock actor_id） |
| max_results | int | 否 | 100 | 最多返回条数 |
| raw | boolean | 否 | true | true：返回 Bedrock 原始 events；false：返回解析后的 conversation_history |

### 响应体 (JSON)

**raw=true（默认）**

| 字段 | 类型 | 说明 |
|------|------|------|
| session_id | string | 会话 ID |
| events | array | Bedrock 原始 event 列表（含 eventId、eventTimestamp、payload 等） |
| count | number | 条数 |

**raw=false**

| 字段 | 类型 | 说明 |
|------|------|------|
| session_id | string | 会话 ID |
| conversation_history | array | 解析后的对话列表，每项 `{"role", "content", "timestamp"}` |
| count | number | 条数 |

---

## 4. 写入会话消息

**POST** `/sessions/{session_id}/messages`

向指定会话显式写入一条消息（用户/助手/系统），写入短期记忆（Bedrock create_event 或内存）。

### 路径参数

| 参数 | 类型 | 说明 |
|------|------|------|
| session_id | string | 会话 ID |

### 请求体 (JSON)

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| role | string | 是 | `user` / `assistant` / `system` |
| content | string | 是 | 消息内容，最长 50000 字符 |
| user_id | string | 否 | 用户 ID（Bedrock actor_id） |

**示例**

```json
{
  "role": "user",
  "content": "请帮我查询项目列表",
  "user_id": "user_001"
}
```

### 响应体 (JSON)

| 字段 | 类型 | 说明 |
|------|------|------|
| session_id | string | 会话 ID |
| role | string | 写入的角色 |
| status | string | 固定为 `"stored"` |

---

## 5. 测试连接

**POST** `/test`

用于验证服务是否正常运行。

### 响应体 (JSON)

| 字段 | 类型 | 说明 |
|------|------|------|
| status | string | 固定为 `"ok"` |
| message | string | 固定为 `"服务运行正常"` |
| version | string | 应用版本号 |

---

## 接口汇总

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/v1/chat/sse` | 流式聊天 (SSE) |
| POST | `/api/v1/chat/sync` | 同步聊天 |
| GET | `/api/v1/chat/sessions/{session_id}/history` | 获取会话历史 |
| POST | `/api/v1/chat/sessions/{session_id}/messages` | 写入会话消息 |
| POST | `/api/v1/chat/test` | 测试连接 |

---

*文档由 `app/routes/chat.py` 导出，如有变更请以实际代码为准。*
