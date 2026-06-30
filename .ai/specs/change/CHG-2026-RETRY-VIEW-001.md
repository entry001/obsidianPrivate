# Change Spec: 新增任务重试次数展示

```yaml
change_id: CHG-2026-RETRY-VIEW-001
spec_id: DISPATCH-2026-RETRY-VIEW-001
risk_level: medium
cross_repo: true
compatibility: backward-compatible
release_guard: feature_flag
rollback: required
status: draft
```

## 1. 变更摘要
- 目标：让任务查询与监控展示可见 `retry_count`
- 原因：降低排障与值班定位成本
- 变更类型：feature

## 2. 影响仓库与模块
| Repo | Module | Change Type | Owner |
|------|--------|-------------|-------|
| 主调度服务 | src/api/tasks / src/services/tasks | modify | 后端组 |
| 主调度Web | views/tasks / api/tasks / types | modify | 前端组 |
| 看门狗Web | views/monitor / api or websocket handlers / types | modify | 前端组 |

## 3. Blast Radius
- api: 任务详情 / 列表查询接口响应新增 `retry_count`
- db: 无 schema 变更，仅读取既有字段
- websocket: `task_status_changed` 等事件 payload 可能新增 `retry_count`
- ipc: 无
- redis: 无
- queue: 无

## 4. 依赖顺序
1. 先确认后端 schema / response 已兼容新增字段
2. 再更新主调度 Web 与看门狗 Web 类型与展示
3. 最后联调实时事件显示一致性

## 5. 兼容性声明
- backward-compatible
- 兼容窗口：前端应兼容字段缺失的过渡期
- 老版本行为：不展示 `retry_count`
- 新版本行为：展示 `retry_count`，缺失时按 0 或空态兜底

## 6. 回滚策略
- 代码回滚：前后端回退到不展示字段的版本
- 数据回滚：无需回滚，因无 schema 变更
- 开关回滚：如使用 feature flag，可直接关闭展示

## 7. 风险点
- 前端若对响应类型严格校验，需确认新增字段不会触发解析异常
- WebSocket payload 若前后端未同步，可能导致实时视图字段缺失
- 不同页面对 `retry_count` 的空值展示需统一

## 8. 审批与协作人
- 架构：后端负责人
- 开发：前后端负责人
- 测试：QA / 值班
- 值班 / 发布：发布负责人
