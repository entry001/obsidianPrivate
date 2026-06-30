# Acceptance Matrix

## 场景：新增任务重试次数展示

| Dimension | Scenario | Method | Priority | Owner | Evidence | Gate |
|-----------|----------|--------|----------|-------|----------|------|
| 功能 | 任务详情接口返回 retry_count | api test | Must Pass | 后端开发 | 测试报告 | PR |
| 功能 | 任务列表接口返回 retry_count | api test | Must Pass | 后端开发 | 测试报告 | PR |
| UI | 主调度 Web 列表展示 retry_count | manual / UI test | Must Pass | 前端开发 | 截图 / 测试记录 | 预发 |
| UI | 主调度 Web 详情展示 retry_count | manual / UI test | Must Pass | 前端开发 | 截图 / 测试记录 | 预发 |
| 监控 | 看门狗 Web 展示 retry_count | manual / runtime verify | Must Pass | 前端开发 / 值班 | 截图 / 验证报告 | 发布前 |
| 契约 | WebSocket payload 字段一致 | contract audit + runtime verify | Must Pass | 架构 / 开发 | 审计报告 / 日志 | PR / 发布前 |
| 兼容 | 前端在字段缺失时不崩溃 | manual / UI test | Must Pass | 前端开发 | 截图 / 记录 | 预发 |
| 安全 | 查询权限未放宽 | review | Must Pass | 审计组 | 审计报告 | PR |
| 回归 | 不影响现有任务状态展示 | regression | Should Pass | QA | 回归记录 | 预发 |

## 豁免项
- 无

## 补充说明
- 如果看门狗 Web 当前不消费该字段，可先验证 REST 展示，再追加实时事件链路补齐。
