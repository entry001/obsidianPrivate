---
template: execution-spec
version: 0.1
owner: 发布负责人 / 值班
---

# Execution Spec: {{execution_name}}

```yaml
execution_id: {{execution_id}}
spec_id: {{spec_id}}
change_type: feature
release_mode: canary
rollback: required
status: draft
```

## 1. 发布目标
- 

## 2. 发布前检查
- [ ] Lint / TypeCheck 通过
- [ ] 测试通过
- [ ] 安全审计通过
- [ ] 契约审计通过
- [ ] 回滚方案已确认

## 3. 发布步骤
1. 
2. 
3. 

## 4. 灰度 / 开关策略
- feature flag:
- canary 范围：
- 放量条件：

## 5. 监控与告警
- 关键指标：
- 告警阈值：
- 观察时长：

## 6. 回滚 Runbook
- 触发条件：
- 回滚步骤：
- 验证步骤：

## 7. 发布后验证
- [ ] 核心路径通过
- [ ] 异常路径验证
- [ ] 数据一致性检查
