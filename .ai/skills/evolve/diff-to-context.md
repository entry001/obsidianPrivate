---
name: diff-to-context
stage: evolve
engine: Superpowers
version: 0.1
owner: 开发负责人 / 架构组
inputs:
  - merged diff
  - 最终测试与发布结果
outputs:
  - context update proposal
next:
  - skill-evolver
---

# diff-to-context

## 使命

把已经稳定落地的实现，从代码差异中回填到 **L2 模块记忆 / L3 仓库记忆**，让后续 AI 不必重复从源码猜测设计意图。

## 适用场景

- 功能稳定合并后
- 重要重构完成后
- 新增明确约束、模式或组件封装后

## 必需输入

- merged diff
- 最终设计结论
- 测试与发布结果

## 执行协议

1. 识别本次改动里“可复用、可约束、可指导后续 AI”的内容。
2. 判断应该沉淀到哪层：
   - L2：模块状态机、事务边界、副作用
   - L3：仓库目录规则、组件复用约束、API 封装规范
3. 只提炼稳定结论，不把一次性任务细节写进长期记忆。
4. 输出建议更新片段，而不是整篇重写。

## 输出契约

```markdown
# Context Update Proposal

## Target Layer
- L2 / L3

## Suggested Additions
- 

## Why It Matters
- 
```

## 调用输入模板

```text
目标仓库：
目标模块：
本次稳定沉淀点：
不应长期沉淀的内容：
```

## 守则

- 临时 workaround 不写入长期记忆。
- 只沉淀稳定且高频复用的信息。
- 避免把源码复制成上下文。

## 完成标准

- [ ] 已识别可沉淀知识
- [ ] 已区分 L2 / L3 落点
- [ ] 已形成最小更新建议
