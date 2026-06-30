---
name: migration-planner
stage: design
engine: OpenSpec + Superpowers
version: 0.1
owner: 架构组 / DBA / 后端负责人
inputs:
  - Change Spec
  - Schema 变更说明
outputs:
  - .ai/specs/contract/<ticket>-migration.md
  - rollback plan
next:
  - release-gate-checker
  - runtime-verifier
---

# migration-planner

## 使命

把数据库变更从“改表动作”升级为 **带兼容窗口、发布顺序、回滚策略** 的工程计划，确保 PostgreSQL 迁移不会破坏共享数据层。

## 适用场景

- 新增表 / 字段 / 索引 / 枚举
- 调整 NOT NULL / DEFAULT / 外键 / 唯一约束
- 需要 Alembic / Flyway 迁移脚本的场景
- 跨仓库依赖数据库兼容窗口的变更

## 必需输入

- Change Spec
- 当前 Schema 与目标 Schema
- 表归属规则
- 上下游服务依赖关系

## 执行协议

1. 判断是否为**主人仓库**发起的变更；非主人仓库不得直接改表。
2. 明确变更类型：向后兼容新增、破坏性删除、数据回填、枚举扩展、索引优化。
3. 为每种变更写发布顺序：
   - 先加兼容结构
   - 再发应用
   - 最后清理旧结构
4. 必须给出 rollback 策略：结构回退、数据回补、开关回退、只读降级等。
5. 标注风险项：大表锁、长事务、索引创建时机、历史数据回填、默认值冲击。
6. 给出验证清单：迁移前检查、迁移后验证、性能观察指标。

## 输出契约

```markdown
# Migration Plan

## 1. Change Summary
- table:
- fields:
- indexes:

## 2. Compatibility Strategy
- expand / migrate / contract

## 3. Release Order
1.
2.
3.

## 4. Rollback
- 

## 5. Verification Checklist
- 
```

## 调用输入模板

```text
目标表：
归属仓库：
变更内容：
是否需要回填：
是否影响历史数据：
上线窗口：
```

## 守则

- 没有 rollback 的迁移不能进入发布。
- 破坏性变更必须采用 expand → migrate → contract 模式。
- 任何 ALTER TABLE 都要说明锁与性能影响。
- 共享数据层变更必须显式说明对上下游仓库的兼容窗口。

## 完成标准

- [ ] 已定义迁移类型与发布顺序
- [ ] 已写 rollback 方案
- [ ] 已列出风险项与验证清单
- [ ] 已明确兼容窗口
