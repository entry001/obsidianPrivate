# Contract Spec: 任务重试次数展示

```yaml
contract_id: CONTRACT-2026-RETRY-VIEW-001
spec_id: DISPATCH-2026-RETRY-VIEW-001
contract_type: api
compatibility: backward-compatible
version: v1
status: draft
```

## 1. 契约类型
- API / WebSocket

## 2. 接口 / 事件定义

### API: GET /api/v1/tasks/{task_id}

#### 响应体（节选）
```json
{
  "task_id": "string",
  "status": "PENDING|RUNNING|SUCCESS|FAILED",
  "retry_count": 0
}
```

### API: GET /api/v1/tasks

#### 列表项（节选）
```json
{
  "task_id": "string",
  "status": "string",
  "retry_count": 0
}
```

### WebSocket: task_status_changed

#### Payload
```json
{
  "event": "task_status_changed",
  "data": {
    "task_id": "string",
    "status": "RUNNING|SUCCESS|FAILED",
    "retry_count": 1
  },
  "timestamp": "2026-06-30T23:20:00Z"
}
```

## 3. 错误码 / 异常约定
| Code | Meaning | Trigger |
|------|---------|---------|
| TASK_NOT_FOUND | 任务不存在 | 查询不存在任务 |
| FORBIDDEN | 无权限查看任务 | 权限不足 |

## 4. 兼容性声明
- 可新增字段：`retry_count`
- 不可破坏字段：`task_id`, `status`
- 废弃计划：无

## 5. 安全与鉴权
- 继承现有任务查询鉴权逻辑
- 不新增写操作
- `retry_count` 不属于敏感字段，但仍受任务可见性权限控制

## 6. 触发时机 / 生命周期
- 查询任务详情 / 列表时同步返回
- 任务状态变化推送时携带当前最新 `retry_count`

## 7. 消费者 / 调用方
| Side | Consumer | Notes |
|------|----------|-------|
| 主调度Web | 任务列表 / 详情页 | 缺失字段需兼容 |
| 看门狗Web | 监控 / 告警视图 | WebSocket / REST 读取一致 |
