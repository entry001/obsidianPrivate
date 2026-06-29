# Agent 与业务后端接口规范

**面向读者**：业务后端开发人员。本文档约定由 AI Agent 侧发起的全部 HTTP 接口，请按以下 **URL、方法、请求参数、响应格式** 实现。

**基础约定**：
- 所有接口根地址为 **`BACKEND_BASE_URL`**（由对接方配置，如 `https://api.example.com`）。
- 完整 URL = `{BACKEND_BASE_URL}` + 下文中的 **路径**（路径不含 query，query 单独列出）。
- 除特别说明外，请求/响应均为 **JSON**，`Content-Type: application/json`。
- 表中 **必填** 表示 Agent 会传；**可选** 表示可能不传，后端需做兼容。

---

## 1. 项目列表 Agent（Project List Agent）

### 1.1 查询项目列表

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/projects/list` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| query_params | object | 否 | 扩展查询条件（如分页、筛选），可为空对象 `{}` |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "query_params": {}
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | array | 项目列表 |
| data[].study_number | string | 研究编号 |
| data[].project_code | string | 项目代码 |
| data[].project_name | string | 项目名称 |
| data[].status | string | 状态（如 active） |

**响应示例**
```json
{
  "data": [
    {
      "study_number": "E0164-U2303",
      "project_code": "CR3486-R7P4",
      "project_name": "示例项目名",
      "status": "active"
    }
  ]
}
```

---

### 1.2 关键词搜索项目

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/projects/search` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| search_term | string | **是** | 搜索关键词 |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "search_term": "E0164"
}
```

**响应体（200 成功）**  
与 1.1 相同，`data` 为匹配的项目列表数组。

```json
{
  "data": [
    {
      "study_number": "E0164-U2303",
      "project_code": "CR3486-R7P4",
      "project_name": "示例项目名",
      "status": "active"
    }
  ]
}
```

---

### 1.3 校验项目编号

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/projects/validate` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| study_numbers | string[] | 否* | 待校验的研究编号列表 |
| project_codes | string[] | 否* | 待校验的项目代码列表 |

\* `study_numbers` 与 `project_codes` 至少传一个，可同时传。

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "study_numbers": ["E0164-U2303"],
  "project_codes": ["P001", "CR3486-R7P4"]
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 校验结果 |
| data.valid | boolean | 是否全部有效 |
| data.invalid_study_numbers | string[] | 无效的研究编号列表 |
| data.invalid_project_codes | string[] | 无效的项目代码列表 |
| data.errors | string[] | 其他错误信息（可选） |

**响应示例（全部有效）**
```json
{
  "data": {
    "valid": true,
    "invalid_study_numbers": [],
    "invalid_project_codes": [],
    "errors": []
  }
}
```

**响应示例（部分无效）**
```json
{
  "data": {
    "valid": false,
    "invalid_study_numbers": ["E9999-X9999"],
    "invalid_project_codes": ["INVALID_CODE"],
    "errors": ["未找到对应项目"]
  }
}
```

---

### 1.4 会话与项目编号绑定

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/sessions/bind-projects` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | **是** | 会话 ID，用于关联绑定关系 |
| user_id | string | 否 | 用户 ID |
| study_numbers | string[] | 否* | 研究编号列表 |
| project_codes | string[] | 否* | 项目代码列表 |

\* `study_numbers` 与 `project_codes` 至少传一个，可同时传。

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "study_numbers": ["E0164-U2303"],
  "project_codes": ["P001", "CR3486-R7P4"]
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 绑定结果 |
| data.bound | boolean | 是否绑定成功 |
| data.session_id | string | 会话 ID |
| data.study_numbers | string[] | 已绑定的研究编号 |
| data.project_codes | string[] | 已绑定的项目代码 |

**响应示例**
```json
{
  "data": {
    "bound": true,
    "session_id": "sess_abc123",
    "study_numbers": ["E0164-U2303"],
    "project_codes": ["P001", "CR3486-R7P4"]
  }
}
```

---

## 2. 报告生成 Agent（Report Generator Agent）

本 Agent 用于生成报告，涉及业务后端 **三个 API**：1）获取提示词；2）获取项目报告数据（group、bw、tv、Individual、Individual Body Weight Change、TGI、Statistical analysis）；3）根据 AI 总结生成报告并返回报告地址。

### 2.1 获取提示词

| 项 | 值 |
|----|-----|
| **URL** | `GET {BASE_URL}/v1/prompt` |
| **方法** | GET |

**Query 参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| template_id | string | **是** | 提示词模板 ID |

**请求示例**  
`GET {BASE_URL}/v1/prompt?template_id=Prompt%20template%201`

**响应体（200 成功）**  
需包含提示词正文，以下三种字段名任一均可被 Agent 识别：`content`、`prompt`、`text`。

| 字段 | 类型 | 说明 |
|------|------|------|
| content | string | 提示词正文（与 prompt、text 三选一或同义） |
| prompt | string | 同上 |
| text | string | 同上 |

**响应示例**
```json
{
  "content": "Please analyze the results on body weight changes and tumor volumes..."
}
```

---

### 2.2 获取项目报告数据

| 项 | 值 |
|----|-----|
| **URL** | `GET {BASE_URL}/v1/sessions/{session_id}/report-data` |
| **方法** | GET |
| **路径参数** | session_id：会话 ID，替换 URL 中的 `{session_id}` |

**Query 参数（可选）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 若路径已含 session_id 可不再传 |

**请求示例**  
`GET {BASE_URL}/v1/sessions/sess_abc123/report-data`

**响应体（200 成功）**  
`data` 为对象，需包含下列字段（字段名与下表一致，Agent 按 key 取值）。业务侧可按需扩展子结构。

| 字段 | 类型 | 说明 |
|------|------|------|
| data.group | any | 分组信息（Group） |
| data.bw | any | 体重数据（Body Weight） |
| data.tv | any | 瘤体积数据（Tumor Volume） |
| data.individual_death_summary | any | Individual（Death Summary） |
| data.individual_body_weight_change | any | Individual Body Weight Change |
| data.tgi | any | Tumor growth inhibition (TGI) with data collected on the given days（如 on day 21 vs G01） |
| data.statistical_analysis | any | Statistical analysis of tumor volume with data collected on the given days |

**响应示例**
```json
{
  "data": {
    "group": {},
    "bw": {},
    "tv": {},
    "individual_death_summary": [],
    "individual_body_weight_change": [],
    "tgi": {},
    "statistical_analysis": {}
  }
}
```

---

### 2.3 根据 AI 总结生成报告并返回报告地址

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/report/generate` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| protocol | string/object | **是** | 协议内容（字符串或结构化对象） |
| template | string | **是** | 提示词模板内容 |
| ai_summary | string | **是** | AI 生成的总结正文 |
| session_id | string | 否 | 会话 ID |

**请求示例**
```json
{
  "protocol": "CROWN BIOSCIENCE - STANDARD Study Report...",
  "template": "Please analyze...",
  "ai_summary": "## For body weight\n...",
  "session_id": "sess_abc123"
}
```

**响应体（200 成功）**  
需返回报告访问地址，以下字段名任一均可被 Agent 识别：根级 `report_url`、`report_address`、`url`，或 `data.report_url`。

| 字段 | 类型 | 说明 |
|------|------|------|
| report_url | string | 报告访问 URL（推荐） |
| report_address | string | 同上 |
| url | string | 同上 |
| data.report_url | string | 同上 |

**响应示例**
```json
{
  "report_url": "https://storage.example.com/reports/xxx.pdf"
}
```
或
```json
{
  "data": {
    "report_url": "https://storage.example.com/reports/xxx.pdf"
  }
}
```

---

## 3. 参数收集 Agent（Parameter Collector Agent）

职责：根据项目编号获取各参数的范围与默认值（由业务后端根据项目生成）；根据范围判断用户输入是否合法并反馈；仅负责将用户确认/修改后的参数**更新**到业务后端。Protocol 由用户上传，不通过本组接口拉取。

路径由 `app.config` 中 `PARAMETER_RANGES_PATH`、`PARAMETER_UPDATE_PATH` 配置，默认见下表。

### 3.1 根据项目编号获取参数范围与默认值

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/parameters/ranges` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| study_number | string | 否* | 研究编号 |
| project_code | string | 否* | 项目代码 |

\* `study_number` 与 `project_code` 至少传一个。

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "study_number": "E0164-U2303",
  "project_code": "CR3486-R7P4"
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 包含 ranges、defaults |
| data.ranges | object | 各参数的范围：key 为参数名，value 为枚举数组（如 `["en","zh"]`）或范围对象（如 `{"min":1,"max":6}`）。不含 protocol_attachment |
| data.defaults | object | 各参数默认值：key 为参数名，value 为默认值。由业务后端根据项目生成 |

**响应示例**
```json
{
  "data": {
    "ranges": {
      "offset_day": { "min": 0, "max": 365 },
      "starting_day": { "min": 0, "max": 100 },
      "prompt": ["Prompt template 1", "Prompt template 2"],
      "report_language": ["en", "zh"],
      "analyse_day": { "min": 1, "max": 99 },
      "compare_type": ["arithmetic", "geometric"],
      "type": ["TV", "FLUX"],
      "tci": { "min": 1, "max": 6 },
      "reference_group": ["Group A", "Group B"]
    },
    "defaults": {
      "offset_day": 0,
      "starting_day": 0,
      "prompt": "Prompt template 1",
      "report_language": "en",
      "compare_type": "arithmetic",
      "type": "TV",
      "tci": 5,
      "reference_group": "Group A"
    }
  }
}
```

Agent 根据 `ranges` 校验用户输入是否符合要求并给出反馈，根据 `defaults` 展示默认值。

---

### 3.2 更新项目参数信息

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/parameters/update` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID（建议传，用于关联会话与项目） |
| user_id | string | 否 | 用户 ID |
| params | object | **是** | 要更新的参数键值对（如 offset_day、prompt、report_language、type、tci 等）。Protocol 由上传流程处理，本接口可不含 protocol_attachment |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "params": {
    "offset_day": 0,
    "prompt": "Prompt template 1",
    "report_language": "en",
    "type": "TV",
    "tci": 5,
    "reference_group": "Group A"
  }
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 更新结果，建议包含 updated: true 或业务侧需要的字段 |

**响应示例**
```json
{
  "data": {
    "updated": true,
    "session_id": "sess_abc123"
  }
}
```

---

## 4. 语义转换 Agent（Semantic Converter Agent）

本 Agent 流程：先从对话中提取章节信息，根据章节向业务后端请求对应文本内容；业务后端需支持根据章节替换文本内容。  
路径由 `app.config` 中 `SEMANTIC_EXTRACT_SECTIONS_PATH`、`SEMANTIC_GET_TEXT_BY_SECTIONS_PATH`、`SEMANTIC_REPLACE_TEXT_BY_SECTIONS_PATH` 配置。

### 4.1 从对话中提取章节信息

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/semantic/sections/extract` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| message | string | **是** | 用户对话/消息内容 |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "message": "请把第三章和第五章的内容给我看一下"
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 包含提取到的章节信息 |
| data.sections | array | 章节列表，每项可含 id、name 等（结构由业务约定） |
| data.section_ids | string[] | 或直接返回章节 ID 数组（与 sections 二选一即可） |

**响应示例**
```json
{
  "data": {
    "sections": [
      { "id": "ch3", "name": "第三章" },
      { "id": "ch5", "name": "第五章" }
    ]
  }
}
```
或
```json
{
  "data": {
    "section_ids": ["ch3", "ch5"]
  }
}
```

---

### 4.2 根据章节获取对应文本内容

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/semantic/sections/text` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| section_ids | string[] | **是** | 章节 ID 列表 |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "section_ids": ["ch3", "ch5"]
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | key 为 section_id，value 为该章节文本内容 |

**响应示例**
```json
{
  "data": {
    "ch3": "第三章的完整文本内容...",
    "ch5": "第五章的完整文本内容..."
  }
}
```

---

### 4.3 根据章节替换文本内容

| 项 | 值 |
|----|-----|
| **URL** | `POST {BASE_URL}/v1/semantic/sections/replace` |
| **方法** | POST |
| **Content-Type** | application/json |

**请求体（Body）**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| session_id | string | 否 | 会话 ID |
| user_id | string | 否 | 用户 ID |
| updates | array | **是** | 按章节替换的列表，每项含 section_id、content |
| updates[].section_id | string | **是** | 章节 ID |
| updates[].content | string | **是** | 该章节的新文本内容（替换原内容） |

**请求示例**
```json
{
  "session_id": "sess_abc123",
  "user_id": "user_001",
  "updates": [
    { "section_id": "ch3", "content": "第三章修订后的完整文本..." },
    { "section_id": "ch5", "content": "第五章修订后的完整文本..." }
  ]
}
```

**响应体（200 成功）**

| 字段 | 类型 | 说明 |
|------|------|------|
| data | object | 替换结果，建议含 updated: true |

**响应示例**
```json
{
  "data": {
    "updated": true
  }
}
```

---

## 5. 配置与 URL 对照表（供对接方配置 Agent 侧 BASE_URL 与路径时参考）

| Agent | 配置键（app.config） | 默认路径 | 方法 |
|-------|----------------------|----------|------|
| 全局 | BACKEND_BASE_URL | — | 根地址 |
| 报告生成 | REPORT_DATA_PATH | /v1/sessions/{session_id}/report-data | GET |
| 报告生成 | REPORT_PROMPT_PATH | /v1/prompt | GET |
| 报告生成 | REPORT_GENERATE_PATH | /v1/report/generate | POST |
| 项目列表 | PROJECT_LIST_LIST_PATH | /v1/projects/list | POST |
| 项目列表 | PROJECT_LIST_SEARCH_PATH | /v1/projects/search | POST |
| 项目列表 | PROJECT_LIST_VALIDATE_PATH | /v1/projects/validate | POST |
| 项目列表 | PROJECT_LIST_BIND_PATH | /v1/sessions/bind-projects | POST |
| 参数收集 | PARAMETER_RANGES_PATH | /v1/parameters/ranges | POST |
| 参数收集 | PARAMETER_UPDATE_PATH | /v1/parameters/update | POST |
| 语义转换 | SEMANTIC_EXTRACT_SECTIONS_PATH | /v1/semantic/sections/extract | POST |
| 语义转换 | SEMANTIC_GET_TEXT_BY_SECTIONS_PATH | /v1/semantic/sections/text | POST |
| 语义转换 | SEMANTIC_REPLACE_TEXT_BY_SECTIONS_PATH | /v1/semantic/sections/replace | POST |

业务后端只需保证 **实际提供的 URL、方法、请求/响应格式** 与上文一致；Agent 侧通过 `BACKEND_BASE_URL` 及上述路径拼出完整 URL 发起调用。
