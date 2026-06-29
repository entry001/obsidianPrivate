# CrownBio API 接口文档

## 基础信息

- **Base URL**: `http://localhost:8080`
- **Content-Type**: `application/json`
- **认证方式**: Bearer Token (JWT)

---

## 1. 认证接口

### 1.1 用户登录

**接口地址**: `POST /login`

**请求参数**:

```json
{
  "username": "admin",
  "password": "admin123"
}
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "token": "eyJhbGciOiJIUzUxMiJ9...",
    "user": {
      "userId": 1,
      "username": "admin",
      "nickName": "管理员",
      "email": "admin@crownbio.com"
    }
  }
}
```

---

## 2. 对话管理接口

### 2.1 创建对话

**接口地址**: `POST /conversation/create`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "title": "新的数据分析",
  "projectId": 1,
  "creator": "Alice"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| title | String | 是 | 对话标题 |
| projectId | Long | 是 | 项目ID |
| creator | String | 否 | 创建者 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "id": "conv-001",
    "title": "新的数据分析",
    "creator": "Alice",
    "createdAt": "2024-01-15T14:30:00",
    "messages": []
  }
}
```

---

### 2.2 获取对话列表

**接口地址**: `GET /conversation/list`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| userId | String | 否 | 用户ID |
| pageNum | Integer | 否 | 页码，默认1 |
| pageSize | Integer | 否 | 每页大小，默认10 |

**请求示例**:

```
GET /conversation/list?userId=user-001&pageNum=1&pageSize=10
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 25,
    "rows": [
      {
        "id": "conv-001",
        "title": "数据分析讨论",
        "createdAt": "2024-01-15T14:30:00"
      },
      {
        "id": "conv-002",
        "title": "肿瘤生长分析",
        "createdAt": "2024-01-16T09:15:00"
      }
    ],
    "pageNum": 1,
    "pageSize": 10
  }
}
```

---

### 2.3 获取对话详情

**接口地址**: `GET /conversation/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 对话ID |

**请求示例**:

```
GET /conversation/1
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "id": "conv-001",
    "title": "数据分析讨论",
    "creator": "Alice",
    "createdAt": "2024-01-15T14:30:00",
    "messages": [
      {
        "id": "msg-001",
        "sender": "user",
        "content": "请帮我分析一下这组数据",
        "timestamp": "2024-01-15T14:30:05",
        "attachments": []
      },
      {
        "id": "msg-002",
        "sender": "ai",
        "content": "好的,我来帮您分析这组数据。根据数据显示...",
        "timestamp": "2024-01-15T14:30:15",
        "attachments": []
      }
    ]
  }
}
```

---

### 2.4 发送消息

**接口地址**: `POST /conversation/message`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "conversationId": 1,
  "content": "请生成报告",
  "attachments": ["file-001.xlsx", "file-002.pdf"]
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| conversationId | Long | 是 | 对话ID |
| content | String | 是 | 消息内容 |
| attachments | List<String> | 否 | 附件列表 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "id": "msg-003",
    "sender": "ai",
    "content": "好的,我正在为您生成报告...",
    "timestamp": "2024-01-16T10:01:05",
    "attachments": []
  }
}
```

---

### 2.5 删除对话

**接口地址**: `DELETE /conversation/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 对话ID |

**请求示例**:

```
DELETE /report/conversation/1
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "删除成功",
  "data": null
}
```

---

## 3. 基础数据接口

### 3.1 获取项目列表

**接口地址**: `GET /base/projects`

**请求头**: `Authorization: Bearer {token}`

**请求示例**:

```
GET /report/base/projects
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "projectId": "1",
      "projectName": "project1"
    },
    {
      "projectId": "3",
      "projectName": "project3"
    }
  ]
}
```

---

### 3.2 获取分组列表

**接口地址**: `GET /base/groups/{projectId}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| projectId | Long | 是 | 项目ID |

**请求示例**:

```
GET /base/groups/1
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "groupId": 1,
      "groupName": "group1"
    },
    {
      "groupId": 2,
      "groupName": "group2"
    }
  ]
}
```

---

### 3.3 获取分组详情

**接口地址**: `POST /base/group-detail`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "type": "BW",
  "groupIds": [1, 2]
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| type | String | 是 | 类型（如BW-体重，TV-肿瘤体积） |
| groupIds | List<Long> | 否 | 分组ID列表 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "tableData": [
      {
        "sessionNote": "2025/01/15",
        "dayNo": "1",
        "animalId": "7758",
        "tempId": "7758",
        "name": "bodyWeight",
        "value": "18.9"
      },
      {
        "sessionNote": "2025/01/15",
        "dayNo": "3",
        "animalId": "7758",
        "tempId": "7758",
        "name": "bodyWeight",
        "value": "18.9"
      }
    ]
  }
}
```

---

## 4. 数据分析接口

### 4.1 获取分组摘要

**接口地址**: `POST /analysis/group-summary`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "type": "BW",
  "groupIds": [1, 2]
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| type | String | 是 | 类型（如BW-体重，TV-肿瘤体积） |
| groupIds | List<Long> | 否 | 分组ID列表 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "tableData": [
      {
        "groupId": "group-001",
        "groupName": "对照组",
        "dayNo": "1",
        "mean": "25.3",
        "sem": "0.8",
        "stdev": "1.2",
        "meanChange": "-2.8"
      },
      {
        "groupId": "group-001",
        "groupName": "对照组",
        "dayNo": "3",
        "mean": "25.3",
        "sem": "0.8",
        "stdev": "3.2",
        "meanChange": "-2.8"
      }
    ],
    "picUrl": "abcd"
  }
}
```

**备注**: picUrl字段内容待定，不确定是否是URL格式

---

### 4.2 获取个体摘要

**接口地址**: `POST /analysis/individual-summary`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "groupIds": [1, 2]
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| groupIds | List<Long> | 否 | 分组ID列表 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "tableData": [
      {
        "groupName": "对照组",
        "animalId": "A0001",
        "fateCode": "Alive",
        "fateDays": "Death",
        "fateDate": "20",
        "dead": "No"
      },
      {
        "groupName": "对照组",
        "animalId": "A0002",
        "fateCode": "Alive",
        "fateDays": "Death",
        "fateDate": "20",
        "dead": "No"
      }
    ],
    "picUrl": "abcd"
  }
}
```

**备注**: picUrl字段内容待定，不确定是否是URL格式

---

### 4.3 执行HuPharm数据分析

**接口地址**: `POST /analysis/analyze`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "groupIds": [1, 2],
  "referenceGroup": 1,
  "type": "TV",
  "compareType": "Vehicle",
  "analysisDay": "8",
  "tgl": "tv-c",
  "meanType": "arithmetic"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| groupIds | List<Long> | 是 | 组ID列表 |
| referenceGroup | Long | 是 | 参考组ID |
| type | String | 是 | 类型 |
| compareType | String | 是 | 比较类型 |
| analysisDay | String | 是 | 分析天数 |
| tgl | String | 是 | TGL参数 |
| meanType | String | 是 | 均值类型 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "tumorGrowthInhibition": {
      "title": "on day 8 vs G01",
      "data": [
        {
          "testTGI": "G01-G02",
          "pValues": "0.23%",
          "signifiLvl": "0.997ns"
        },
        {
          "testTGI": "G01-G02",
          "pValues": "0.23%",
          "signifiLvl": "0.997ns"
        }
      ]
    },
    "tcTumorVolume": {
      "heads": ["8", "12", "15", "19", "22", "26"],
      "dataKey": ["group1", "group2", "group3", "group4", "group5", "group16"],
      "dataValues": [
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2],
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2],
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2],
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2],
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2],
        [0.9977, 0.1, 0.2, 0.9977, 0.1, 0.2]
      ]
    }
  }
}
```

**备注**: heads数组中的天数是动态的，支持不固定的天数列表

---

## 5. 提示词模板接口

### 5.1 获取提示词模板列表

**接口地址**: `GET /report/prompt-template/lists`

**请求头**: `Authorization: Bearer {token}`

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "id": 1,
      "content": "prop1"
    },
    {
      "id": 2,
      "content": "prop2"
    }
  ]
}
```

---

### 5.2 获取提示词模板内容

**接口地址**: `GET /report/prompt-template/content/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": "123122312321"
}
```

---

## 6. 报告生成接口

### 6.1 获取表单初始化配置

**接口地址**: `GET /report/config`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 用于页面加载时，获取所有下拉框的初始备选项

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "projects": ["E3651-U2405(HH-7)", "P2024-X01"],
    "compareTypes": ["all_pairwise", "vs_control"],
    "types": ["TV", "BW", "RTV"],
    "tgiMethods": ["1-TV T/TV_C", "1-ΔT/ΔC"],
    "meanTypes": ["arithmetic", "geometric"],
    "prompts": [
      {
        "id": 1,
        "label": "Prompt template 1, good TGI, no BWL, 12 grp."
      }
    ],
    "languages": ["English", "Chinese"]
  }
}
```

---

### 6.2 上传实验协议文件

**接口地址**: `POST /report/upload-protocol`

**请求头**:

```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**接口说明**: 点击 "Upload Protocol" 按钮时调用

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| file | File | 是 | 协议文件 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "上传成功",
  "data": {
    "fileId": "file_8829312",
    "fileName": "protocol_v1.pdf"
  }
}
```

---

### 6.3 生成报告

**接口地址**: `POST /report/generate`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 点击 "GENERATE REPORT" 按钮时提交全量表单数据

**请求参数**:

```json
{
  "project": "E3651-U2405(HH-7)",
  "referenceGroup": "Group 01",
  "offsetDay": 0,
  "startDate": "2025/09/21",
  "analyseDay": 8,
  "compareType": "all_pairwise",
  "type": "TV",
  "tgiMethod": "1-TV T/TV_C",
  "meanType": "arithmetic",
  "promptId": 1,
  "language": "English",
  "protocolFileId": "file_8829312"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| project | String | 是 | 项目编号 |
| referenceGroup | String | 是 | 对照组名称 |
| offsetDay | Integer | 是 | 偏移天数 |
| startDate | String | 是 | 随机化开始日期 (YYYY/MM/DD) |
| analyseDay | Integer | 是 | 分析天数 |
| compareType | String | 是 | 比较类型 (如 all_pairwise) |
| type | String | 是 | 数据类型 (如 TV: Tumor Volume) |
| tgiMethod | String | 是 | TGI计算方法 |
| meanType | String | 是 | 均值类型 (arithmetic/geometric) |
| promptId | Integer | 是 | 提示词模板ID |
| language | String | 是 | 语言 (English/Chinese) |
| protocolFileId | String | 是 | 已上传的文件唯一标识 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "报告生成任务已启动",
  "data": {
    "taskId": "task_abc123",
    "status": "processing"
  }
}
```

---

## 7. Word文档管理接口

### 7.1 获取文档章节列表

**接口地址**: `GET /word/chapter/tree/{documentId}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "documentId": 1,
    "documentName": "实验报告.docx",
    "version": 1,
    "totalChapters": 15,
    "chapters": [
      {
        "id": "ch_001",
        "title": "Introduction",
        "level": 1,
        "orderIndex": 0,
        "contentType": "HEADING",
        "hasChildren": true,
        "styleInfo": {
          "fontName": "Arial",
          "fontSize": 16,
          "bold": true
        },
        "children": [
          {
            "id": "ch_001_001",
            "title": "Background",
            "level": 2,
            "orderIndex": 0,
            "contentType": "HEADING",
            "hasChildren": false,
            "styleInfo": {
              "fontName": "Arial",
              "fontSize": 14,
              "bold": true
            },
            "children": []
          }
        ]
      },
      {
        "id": "ch_002",
        "title": "Methods",
        "level": 1,
        "orderIndex": 1,
        "contentType": "HEADING",
        "hasChildren": false,
        "styleInfo": {
          "fontName": "Arial",
          "fontSize": 16,
          "bold": true
        },
        "children": []
      }
    ]
  }
}
```

**响应字段说明**:

| 字段名                    | 类型      | 说明                       |     |
| ---------------------- | ------- | ------------------------ | --- |
| documentId             | Long    | 文档ID                     |     |
| documentName           | String  | 文档名称                     |     |
| version                | Integer | 文档版本号                    |     |
| totalChapters          | Integer | 总章节数                     |     |
| chapters               | Array   | 顶层章节列表                   |     |
| chapters[].id          | String  | 章节唯一标识                   |     |
| chapters[].title       | String  | 章节标题                     |     |
| chapters[].level       | Integer | 章节层级（1-9）                |     |
| chapters[].orderIndex  | Integer | 同级排序序号                   |     |
| chapters[].contentType | String  | 内容类型（HEADING/PARAGRAPH等） |     |
| chapters[].hasChildren | Boolean | 是否有子章节                   |     |
| chapters[].children    | Array   | 子章节列表（递归结构）              |     |

---

### 7.2 查询章节详细内容

**接口地址**: `GET /word/chapter/content`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 章节ID |

**请求示例**:

```
GET /word/chapter/content?documentId=1&chapterId=ch_001
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "chapterId": "ch_001",
    "title": "Introduction",
    "level": 1,
    "styleInfo": {
      "fontName": "Arial",
      "fontSize": 16,
      "bold": true,
      "italic": false,
      "underline": false
    },
    "contents": [
      {
        "type": "TEXT",
        "content": "This study investigates the efficacy of compound XYZ in tumor growth inhibition.",
        "metadata": {}
      },
      {
        "type": "TABLE",
        "html": "<table><tr><th>Group</th><th>Dose</th></tr><tr><td>Control</td><td>0 mg/kg</td></tr></table>",
        "metadata": {
          "rows": 2,
          "columns": 2
        }
      },
      {
        "type": "IMAGE",
        "url": "http://localhost:8080/files/images/chart_001.png",
        "thumbnailUrl": "http://localhost:8080/files/images/chart_001_thumb.png",
        "width": 800,
        "height": 600,
        "metadata": {
          "caption": "Figure 1: Tumor volume over time"
        }
      }
    ]
  }
}
```

**响应字段说明**:

| 字段名 | 类型 | 说明 |
|--------|------|------|
| chapterId | String | 章节ID |
| title | String | 章节标题 |
| level | Integer | 章节层级 |
| styleInfo | Object | 样式信息 |
| contents | Array | 内容项列表 |
| contents[].type | String | 内容类型（TEXT/TABLE/IMAGE） |
| contents[].content | String | 文本内容（仅TEXT类型） |
| contents[].html | String | 表格HTML（仅TABLE类型） |
| contents[].url | String | 图片URL（仅IMAGE类型） |
| contents[].thumbnailUrl | String | 缩略图URL（仅IMAGE类型） |
| contents[].width | Integer | 图片宽度（仅IMAGE类型） |
| contents[].height | Integer | 图片高度（仅IMAGE类型） |
| contents[].metadata | Object | 其他元数据 |

---

### 7.3 删除章节

**接口地址**: `DELETE /word/chapter/delete`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 删除指定章节及其所有子章节

**请求参数**:

```json
{
  "documentId": 1,
  "chapterId": "ch_001_001"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 要删除的章节ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": null
}
```

**注意事项**:
- 删除章节会同时删除其所有子章节
- 删除操作不可恢复，请谨慎操作
- 删除后会自动调整同级章节的排序序号

---

### 7.4 调整章节顺序

**接口地址**: `PUT /word/chapter/move`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 调整章节在同级中的顺序位置

**请求参数**:

```json
{
  "documentId": 1,
  "chapterId": "ch_002",
  "targetOrderIndex": 0
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 要移动的章节ID |
| targetOrderIndex | Integer | 是 | 目标位置的排序序号（从0开始） |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": null
}
```

**使用场景**:
- 将第3个章节移到第1个位置：`targetOrderIndex: 0`
- 将第1个章节移到第3个位置：`targetOrderIndex: 2`
- 只能在同级章节之间移动，不能跨层级移动

---

### 7.5 修改章节层级

**接口地址**: `PUT /word/chapter/level`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 修改章节的层级（如将一级标题改为二级标题）

**请求参数**:

```json
{
  "documentId": 1,
  "chapterId": "ch_001_001",
  "newLevel": 3
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 章节ID |
| newLevel | Integer | 是 | 新的层级（1-6，对应Heading1-6） |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": null
}
```

**注意事项**:
- 层级范围为1-6，对应Word中的Heading1到Heading6
- 修改层级会自动调整样式（字体大小、加粗等）
- 修改父章节层级可能影响子章节的层级关系

---

### 7.6 修改章节标题

**接口地址**: `PUT /word/chapter/title`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 修改章节的标题文本

**请求参数**:

```json
{
  "documentId": 1,
  "chapterId": "ch_001",
  "newTitle": "Introduction and Background"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 章节ID |
| newTitle | String | 是 | 新的标题文本 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": null
}
```

**注意事项**:
- 标题不能为空
- 修改标题不会影响章节的层级和样式
- 标题修改会立即生效

---

### 7.7 修改章节内容

**接口地址**: `PUT /word/chapter/content`

**请求头**: `Authorization: Bearer {token}`

**接口说明**: 替换章节的正文内容（不包含子章节）

**请求参数**:

```json
{
  "documentId": 1,
  "chapterId": "ch_001",
  "newContent": "This is the updated content for the introduction section. It provides a comprehensive overview of the study objectives and methodology.",
  "contentFormat": "TEXT"
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| documentId | Long | 是 | 文档ID |
| chapterId | String | 是 | 章节ID |
| newContent | String | 是 | 新的内容（纯文本或HTML） |
| contentFormat | String | 否 | 内容格式（TEXT/HTML），默认TEXT |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": null
}
```

**注意事项**:
- 内容格式支持TEXT（纯文本）和HTML（富文本）
- 使用HTML格式时，需确保HTML标签正确闭合
- 修改内容只影响当前章节，不影响子章节
- 原有的表格和图片会被替换

**HTML格式示例**:

```json
{
  "documentId": 1,
  "chapterId": "ch_001",
  "newContent": "<p>This is a <strong>bold</strong> text.</p><p>This is an <em>italic</em> text.</p>",
  "contentFormat": "HTML"
}
```

---

## 8. 系统管理接口

### 6.1 获取模型配置

**接口地址**: `GET /report/system/model-config`

**请求头**: `Authorization: Bearer {token}`

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "id": 1,
    "modelName": "GPT-4"
  }
}
```

---

### 6.2 更新模型配置

**接口地址**: `PUT /report/system/model-config`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "id": 1
}
```

**响应示例**:

```json
{
  "code": 200,
  "msg": "更新成功",
  "data": null
}
```

---

### 6.3 获取提示词模板列表（分页）

**接口地址**: `GET /report/prompt-template/list`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 否 | 模板名称 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 10,
    "rows": [
      {
        "id": 1,
        "name": "数据分析模板",
        "content": "请分析以下数据...",
        "createTime": "2024-01-01T00:00:00"
      }
    ]
  }
}
```

---

### 6.4 创建提示词模板

**接口地址**: `POST /report/prompt-template`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

```json
{
  "name": "新模板",
  "content": "请分析数据..."
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 模板名称 |
| content | String | 是 | 模板内容 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "创建成功",
  "data": {
    "name": "新模板",
    "content": "请分析{{dataType}}数据..."
  }
}
```

---

### 6.5 更新提示词模板

**接口地址**: `PUT /report/prompt-template/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板ID |

**请求参数**:

```json
{
  "name": "更新后的模板",
  "content": "请详细分析{{dataType}}数据..."
}
```

**参数说明**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 是 | 模板名称 |
| content | String | 是 | 模板内容 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "更新成功",
  "data": null
}
```

---

### 6.6 删除提示词模板

**接口地址**: `DELETE /report/prompt-template/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "删除成功",
  "data": null
}
```

---

### 6.7 上传报告模板

**接口地址**: `POST /report/system/report-template`

**请求头**:

```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| file | File | 是 | 模板文件 |
| name | String | 是 | 模板名称 |
| description | String | 否 | 模板描述 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "上传成功",
  "data": {
    "id": 1,
    "name": "标准报告模板",
    "fileName": "template.docx",
    "filePath": "/templates/template.docx",
    "description": "标准数据分析报告模板",
    "uploadTime": "2024-01-20T10:00:00"
  }
}
```

---

### 6.8 获取报告模板列表

**接口地址**: `GET /report/system/report-template/list`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | String | 否 | 模板名称 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 5,
    "rows": [
      {
        "id": 1,
        "name": "标准报告模板",
        "fileName": "template.docx",
        "filePath": "/templates/template.docx",
        "description": "标准数据分析报告模板",
        "createTime": "2024-01-20T10:00:00"
      }
    ]
  }
}
```

---

### 6.9 删除报告模板

**接口地址**: `DELETE /report/system/report-template/{id}`

**请求头**: `Authorization: Bearer {token}`

**路径参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 模板ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "删除成功",
  "data": null
}
```

---

### 6.10 获取对话日志

**接口地址**: `GET /report/system/conversation-logs`

**请求头**: `Authorization: Bearer {token}`

**请求参数**:

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| conversationId | Long | 否 | 对话ID |
| startDate | String | 否 | 开始日期 |
| endDate | String | 否 | 结束日期 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 50,
    "rows": [
      {
        "id": 1,
        "conversationId": 1,
        "userId": "user-001",
        "action": "发送消息",
        "content": "请分析数据",
        "timestamp": "2024-01-15T14:30:00"
      }
    ]
  }
}
```

---

### 6.11 获取常见问题列表

**接口地址**: `GET /report/system/frequent-questions`

**请求头**: `Authorization: Bearer {token}`

**响应示例**:

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "id": 1,
      "question": "如何导出报告？",
      "answer": "点击右上角的导出按钮...",
      "category": "报告",
      "order": 1
    },
    {
      "id": 2,
      "question": "如何分析肿瘤生长数据？",
      "answer": "选择对照组和测试组...",
      "category": "分析",
      "order": 2
    }
  ]
}
```

---

## 7. 通用响应格式

### 7.1 成功响应

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {}
}
```

---

### 7.2 失败响应

```json
{
  "code": 500,
  "msg": "操作失败：具体错误信息",
  "data": null
}
```

---

### 7.3 参数校验失败

```json
{
  "code": 400,
  "msg": "参数校验失败：字段名不能为空",
  "data": null
}
```

---

### 7.4 未授权

```json
{
  "code": 401,
  "msg": "未授权，请先登录",
  "data": null
}
```

---

### 7.5 无权限

```json
{
  "code": 403,
  "msg": "无权限访问该资源",
  "data": null
}
```

---

### 7.6 资源不存在

```json
{
  "code": 404,
  "msg": "资源不存在",
  "data": null
}
```

---

## 8. 状态码说明

| 状态码 | 说明 |
|--------|------|
| 200 | 操作成功 |
| 400 | 参数错误 |
| 401 | 未授权 |
| 403 | 无权限 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

---


