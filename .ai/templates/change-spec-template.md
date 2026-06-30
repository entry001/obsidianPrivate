---
template: change-spec
version: 0.1
owner: 架构组 / 开发负责人
---

# Change Spec: {{change_name}}

```yaml
change_id: {{change_id}}
spec_id: {{spec_id}}
risk_level: medium
cross_repo: false
compatibility: backward-compatible
release_guard: feature_flag
rollback: required
status: draft
```

## 1. 变更摘要
- 目标：
- 原因：
- 变更类型：feature / refactor / migration / hotfix

## 2. 影响仓库与模块
| Repo | Module | Change Type | Owner |
|------|--------|-------------|-------|
| | | | |

## 3. Blast Radius
- api:
- db:
- websocket:
- ipc:
- redis:
- queue:

## 4. 依赖顺序
1. 
2. 
3. 

## 5. 兼容性声明
- backward-compatible / breaking
- 兼容窗口：
- 老版本行为：
- 新版本行为：

## 6. 回滚策略
- 代码回滚：
- 数据回滚：
- 开关回滚：

## 7. 风险点
- 

## 8. 审批与协作人
- 架构：
- 开发：
- 测试：
- 值班 / 发布：
