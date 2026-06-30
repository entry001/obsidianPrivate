# Product Spec: 新增任务重试次数展示

```yaml
spec_id: DISPATCH-2026-RETRY-VIEW-001
change_type: feature
repos: [主调度服务, 主调度Web, 看门狗Web]
blast_radius: [api, websocket, db]
compatibility: backward-compatible
release_guard: feature_flag
rollback: required
owner: 开发负责人
status: draft
```

## 1. 背景与业务目标
- 背景：当前任务详情与监控视图无法直观看到任务已重试次数，导致开发、运维和值班在分析失败任务时需要查日志或数据库。
- 业务目标：在任务查询接口与相关页面中展示 `retry_count`，用于快速判断任务是否已进入重试阶段。
- 用户价值：降低排障成本，提升值班判断效率。
- 成功指标：任务详情页、任务列表/告警视图均可展示当前重试次数；异常任务排查时间缩短。

## 2. In Scope
- 主调度服务任务查询接口返回 `retry_count`
- 主调度 Web 任务详情 / 列表展示 `retry_count`
- 看门狗 Web 告警或任务监控视图展示 `retry_count`
- 如有实时状态推送，事件 payload 补充 `retry_count`

## 3. Out of Scope
- 不修改任务重试策略本身
- 不新增重试配置页面
- 不改变调度子系统写入重试次数的现有逻辑
- 不做历史数据修复脚本（默认历史数据按现状展示）

## 4. 角色与权限
| 角色 | 可执行动作 | 限制 |
|------|------------|------|
| admin / 运维 | 查看任务重试次数 | 无 |
| 普通用户 | 查看自己可见任务的重试次数 | 仍受现有任务查看权限控制 |
| 看门狗值班 | 在监控与告警视图查看重试次数 | 只读 |

## 5. 核心业务流程
1. 调度子系统在任务失败重试时维护 `retry_count`
2. 主调度服务查询任务详情 / 列表时返回 `retry_count`
3. 主调度 Web、看门狗 Web 在页面中展示该字段
4. 如状态变化触发 WebSocket 推送，则 payload 一并包含 `retry_count`

## 6. 关键实体
| 实体 | 含义 | 关键字段 |
|------|------|----------|
| task | 调度任务 | id, status, retry_count |
| task_status_changed event | 状态变化推送事件 | event, data.task_id, data.status, data.retry_count, timestamp |

## 7. 风险与未知项
- 需确认当前任务查询接口是否已返回完整任务对象，新增字段是否会影响前端类型解析。
- 需确认看门狗 Web 当前是通过 REST 还是 WebSocket 获取任务详情。
- 需确认历史旧任务 `retry_count` 是否都存在且默认为 0。

## 8. 依赖与前置条件
- PostgreSQL 中 `task.retry_count` 字段已存在且默认值正确
- 主调度服务有统一任务查询 API / schema
- 前端已存在任务详情和监控展示位

## 9. 验收标准
- [ ] 任务详情接口返回 `retry_count`
- [ ] 主调度 Web 任务列表 / 详情正确展示该字段
- [ ] 看门狗 Web 监控或告警视图正确展示该字段
- [ ] 实时状态推送链路中字段一致
- [ ] 未改变现有重试逻辑与权限边界
