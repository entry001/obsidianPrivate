# Execution Spec: 任务重试次数展示发布计划

```yaml
execution_id: EXEC-2026-RETRY-VIEW-001
spec_id: DISPATCH-2026-RETRY-VIEW-001
change_type: feature
release_mode: canary
rollback: required
status: draft
```

## 1. 发布目标
- 在不改变现有任务调度逻辑的前提下，为查询与监控链路增加 `retry_count` 展示能力。

## 2. 发布前检查
- [ ] 后端查询接口已返回 `retry_count`
- [ ] 主调度 Web 类型与展示已同步
- [ ] 看门狗 Web 类型与展示已同步
- [ ] WebSocket payload 如涉及，已联调通过
- [ ] 空值/缺失字段兼容策略已验证
- [ ] 回滚方式已确认

## 3. 发布步骤
1. 发布主调度服务，确保 API / 事件 payload 兼容新增字段
2. 发布主调度 Web 展示版本
3. 发布看门狗 Web 展示版本
4. 验证列表、详情、实时状态视图显示一致

## 4. 灰度 / 开关策略
- feature flag: 如前端支持，可按页面开关控制展示
- canary 范围：先测试环境，再小范围生产页面验证
- 放量条件：无字段解析异常，无显示错位，无事件丢字段

## 5. 监控与告警
- 关键指标：接口错误率、前端页面异常率、WebSocket 消费错误日志
- 告警阈值：新增错误率不可明显高于基线
- 观察时长：发布后观察 30-60 分钟

## 6. 回滚 Runbook
- 触发条件：前端解析异常、页面渲染异常、实时视图异常
- 回滚步骤：先回滚前端展示，再回滚后端 payload 扩展（如必要）
- 验证步骤：确认页面恢复正常，不再引用该字段

## 7. 发布后验证
- [ ] 任务详情页显示 `retry_count`
- [ ] 任务列表显示 `retry_count`
- [ ] 看门狗视图显示 `retry_count`
- [ ] WebSocket 实时更新一致
