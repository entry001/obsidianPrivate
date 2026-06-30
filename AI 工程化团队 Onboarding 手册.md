# AI 工程化团队 Onboarding 手册

> 面向新加入团队的同学，用于在最短时间内完成从“理解理念”到“能独立跑一条工程链路”的入门。
>
> 本手册的重点不是理论全讲，而是回答三个问题：
> 1. 我第一周该看什么？
> 2. 我第一次做需求时该怎么走？
> 3. 什么程度算真正完成 onboarding？

---

## 一、适用对象

本手册适用于以下人群：

- 新加入团队的全栈开发同学
- 从传统开发模式切换到 AI 工程化模式的老成员
- 需要承担需求结构化、实现、联调、验证与发布协作的一线研发同学

在本团队里，**“后端 / 前端 / QA / 架构”更多是任务阶段中的职责帽子，而不是固定岗位边界。**  
同一名全栈开发在一个完整任务里，可能同时要：

- 写 Product / Change / Contract Spec
- 修改 FastAPI / Vue / Electron 中的一部分实现
- 对齐 WebSocket / IPC / API 契约
- 参与验证、发布和回滚准备

因此 onboarding 的目标不是把你培养成“某一端的执行者”，而是让你形成 **Spec → Design → Implementation → Verify → Evolve** 的全链路工作习惯。

如果你以前习惯的是“拿需求 → 直接写代码 → 提 PR”，那么本手册要帮助你切换到新的工作方式：

> **先写 Spec，先审边界，先设门禁，再让 AI 和人协同完成实现、验证与沉淀。**

---

## 二、先建立正确心智模型

加入团队后，先记住下面 4 个概念：

### 1. OpenSpec
它解决“**要做什么**”。

不是写传统长文档，而是把需求、范围、契约、发布、回滚这些关键要素写成 AI 和团队都能执行的真理源。

### 2. Superpowers
它解决“**AI 应该如何工作**”。

AI 不是裸跑，而是默认带着上下文、约束、审计、验证、发布门禁和沉淀能力一起工作。

### 3. Skill
它解决“**每一步具体怎么做**”。

Skill 不是一段 Prompt，而是标准化的工程动作，例如：
- 把需求整理成 Product Spec
- 把需求展开成 BDD
- 审计接口契约
- 生成验收矩阵
- 发布前做运行验证

### 4. 五类 Spec
它们分别解决不同层面的真理源：

- **Product Spec**：做什么，范围是什么
- **Change Spec**：影响哪些仓库 / 模块 / 协议 / 表
- **Contract Spec**：接口 / 事件 / Schema 如何定义
- **Execution Spec**：怎么发布、怎么观察、怎么回滚
- **Hotfix Spec**：紧急修复时最小修复面和补账计划

如果你只记住一句话，请记住：

> **AI 工程化不是“更快写代码”，而是“更稳地定义、实现、验证和沉淀变更”。**

---

## 三、入职第一天必须看完的文档

### P0：必须读
1. [[AI 工程化方案]]
2. [[AI 工程化使用手册]]
3. [[.ai/skills/README.md|AI 工程化 Skill 索引]]
4. [[.ai/templates/README.md|Spec 模板索引]]

### P1：建议读
5. [[AI 工程化方案（管理层汇报版）]]
6. [[.ai/skills/_runtime/README.md|Skill 运行化使用指南]]
7. [[.ai/skills/_runtime/skill-capability-map.md|Skill 与现有能力映射表]]

### P2：必须过一遍的样板
8. [[.ai/specs/product/DISPATCH-2026-RETRY-VIEW-001.md|Product Spec 示例]]
9. [[.ai/specs/change/CHG-2026-RETRY-VIEW-001.md|Change Spec 示例]]
10. [[.ai/specs/contract/CONTRACT-2026-RETRY-VIEW-001.md|Contract Spec 示例]]
11. [[.ai/specs/execution/EXEC-2026-RETRY-VIEW-001.md|Execution Spec 示例]]
12. [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-acceptance-matrix.md|Acceptance Matrix 示例]]
13. [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-skill-chain-demo.md|Skill 调用链示例]]

### 第一天阅读目标
不是要求你背下来，而是达到以下 4 个结果：

- 知道 5 类 Spec 各自解决什么问题
- 知道 Skill 的 5 个阶段：Spec / Design / Implementation / Verify / Evolve
- 知道普通功能不是直接写代码，而是先过 Spec 和审计
- 知道热修复可以收缩流程，但不能跳过边界、验证和回滚

---

## 四、目录地图：你以后大部分时间会在哪些地方工作

### 1. 规则与模板入口
- [[.ai/templates/README.md|Spec 模板索引]]
- [[.ai/skills/README.md|Skill 索引]]

### 2. 日常写作位置
- `.ai/specs/product/`
- `.ai/specs/change/`
- `.ai/specs/contract/`
- `.ai/specs/execution/`
- `.ai/runbooks/`

### 3. 规则和动作资产位置
- `.ai/skills/spec/`
- `.ai/skills/design/`
- `.ai/skills/implementation/`
- `.ai/skills/verify/`
- `.ai/skills/evolve/`

### 4. 你真正要理解的是这条链

```text
模板 → Spec → Skill → 代码 / 测试 / 审计 / 发布说明 → 复盘沉淀
```

不要把 `.ai/` 目录理解为“文档堆”。

它本质上是：
- **模板库**
- **工程动作库**
- **样板库**
- **知识回流库**

---

## 五、第一周 onboarding 计划

## Day 1：理解全貌

### 目标
理解为什么团队不再采用“需求 → 直接编码”的旧方式。

### 要做的事
- 通读 [[AI 工程化方案]]
- 通读 [[AI 工程化使用手册]]
- 浏览 [[.ai/skills/README.md|Skill 索引]] 和 [[.ai/templates/README.md|模板索引]]
- 读完“任务重试次数展示”的整套样板

### 当天通过标准
你可以口头回答：
- Product / Change / Contract / Execution / Hotfix 分别是什么
- 普通功能和热修复流程有什么不同
- 为什么跨仓库改动必须先做 Change Spec 和 Contract 审计

---

## Day 2：学会从模板起手

### 目标
能从模板写出一版最小 Spec，而不是直接让 AI 编码。

### 要做的事
- 用 [[.ai/templates/product-spec-template.md|product-spec-template]] 仿写一个小需求
- 用 [[.ai/templates/change-spec-template.md|change-spec-template]] 补齐影响面
- 如果涉及接口，再写 [[.ai/templates/contract-spec-template.md|contract-spec-template]]

### 推荐练习题
任选一个小需求：
- 新增列表展示字段
- 新增一个监控筛选项
- 新增一个任务详情中的只读信息

### 当天通过标准
你写的 Spec 至少包含：
- In Scope / Out of Scope
- 影响仓库
- Blast Radius
- 兼容性说明
- 回滚思路

---

## Day 3：学会走设计审计

### 目标
理解“还没编码就先发现问题”是这套体系最重要的节省成本方式。

### 要做的事
- 学习以下 Skill：
  - [[.ai/skills/design/contract-auditor.md|contract-auditor]]
  - [[.ai/skills/design/state-machine-checker.md|state-machine-checker]]
  - [[.ai/skills/design/architecture-impact-brief.md|architecture-impact-brief]]
  - [[.ai/skills/design/migration-planner.md|migration-planner]]
- 拿 Day 2 的练习题做一次简化设计审计

### 当天通过标准
你能指出：
- 是否有跨仓库影响
- 是否有接口契约变化
- 是否有状态机 / 副作用 / 兼容窗口风险

---

## Day 4：学会把 Spec 变成实现

### 目标
理解实现不是“自由发挥”，而是对 Spec 和 Contract 的落地。

### 要做的事
- 学习以下 Skill：
  - [[.ai/skills/implementation/tdd-implementer.md|tdd-implementer]]
  - [[.ai/skills/implementation/api-contract-coder.md|api-contract-coder]]
  - [[.ai/skills/implementation/ui-prototyper.md|ui-prototyper]]
  - [[.ai/skills/implementation/event-wiring-builder.md|event-wiring-builder]]
- 做一个最小实现练习：
  - 一个只读字段展示
  - 一个轻量接口字段扩展
  - 一个前端类型同步

### 当天通过标准
你能说清楚：
- 代码是由哪份 Spec 驱动的
- 测试是由哪些 BDD 场景覆盖的
- 这次实现刻意没改什么

---

## Day 5：学会验证与交付

### 目标
理解“能跑”和“能发”是两回事。

### 要做的事
- 学习以下 Skill：
  - [[.ai/skills/spec/acceptance-matrix-builder.md|acceptance-matrix-builder]]
  - [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]
  - [[.ai/skills/verify/security-boundary-reviewer.md|security-boundary-reviewer]]
  - [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]
  - [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]
  - [[.ai/skills/evolve/changelog-and-runbook.md|changelog-and-runbook]]
- 为 Day 4 的练习补齐：
  - 验收矩阵
  - 最小发布说明
  - 最小回滚说明

### 当天通过标准
你能产出：
- 一份验收矩阵
- 一份最小发布说明
- 一份最小回滚步骤

---

## Week 2：接一个真实低风险任务

### 目标
在 mentor 或 buddy 陪同下，独立跑完一条真实链路，并练习作为全栈开发如何同时照看需求、接口、页面、事件、验证和发布。

### 推荐任务类型
- 只读字段展示
- 查询接口增强
- 小型监控页面改动
- 不涉及 schema 变更的轻量跨仓库功能

### 推荐流程
`spec-intake → change-scope-mapper → bdd-expander → acceptance-matrix-builder → contract-auditor → tdd-implementer / api-contract-coder / ui-prototyper → spec-test-sync → release-gate-checker → runtime-verifier → changelog-and-runbook`

### 通过标准
你可以独立交付：
- 1 份 Product Spec
- 1 份 Change Spec
- 1 份最小 Contract / Execution Spec
- 1 套最小验收 / 验证 / 发布说明

---

## 六、新人最重要的行为准则

### 1. 先问“边界是什么”，不要先问“代码怎么写”
任何需求进来先确认：
- 改哪些
- 不改哪些
- 会不会动接口
- 会不会动事件
- 会不会影响其他仓库

### 2. 不让 AI 从模糊需求直跳代码
如果输入不清楚，AI 输出再快也会把错误放大。

### 3. 不把“测试通过”当成“业务正确”
测试只是证明部分行为可验证，不能代替对 Spec 的核对。

### 4. 不把热修复当特权
热修复只是收缩流程，不是免检通行证。

### 5. 任何事故都应该留下资产
至少留下一项：
- 新规则
- 新 Skill
- 新测试
- 新 Runbook
- 新上下文补充

---

## 七、全栈开发的 onboarding 重点

在这套工程化模式里，全栈开发不是“前后端都会一点”，而是要能在一个任务内切换多种职责视角：

- **需求视角**：把业务目标写成 Product Spec 与 BDD
- **架构视角**：看影响边界、契约、状态机、兼容性
- **实现视角**：完成后端、前端、事件链路或桌面端中的必要改动
- **验证视角**：看验收矩阵、运行验证、安全边界、发布门禁
- **沉淀视角**：把经验回流成规则、上下文、Skill、Runbook

### 1. 全栈开发第一优先学会什么

建议优先掌握下面这组“全链路基础 Skill”：

- [[.ai/skills/spec/spec-intake.md|spec-intake]]
- [[.ai/skills/spec/change-scope-mapper.md|change-scope-mapper]]
- [[.ai/skills/spec/bdd-expander.md|bdd-expander]]
- [[.ai/skills/spec/acceptance-matrix-builder.md|acceptance-matrix-builder]]
- [[.ai/skills/design/contract-auditor.md|contract-auditor]]
- [[.ai/skills/design/architecture-impact-brief.md|architecture-impact-brief]]
- [[.ai/skills/implementation/tdd-implementer.md|tdd-implementer]]
- [[.ai/skills/implementation/api-contract-coder.md|api-contract-coder]]
- [[.ai/skills/implementation/ui-prototyper.md|ui-prototyper]]
- [[.ai/skills/implementation/event-wiring-builder.md|event-wiring-builder]]
- [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]
- [[.ai/skills/verify/security-boundary-reviewer.md|security-boundary-reviewer]]
- [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]
- [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]
- [[.ai/skills/evolve/changelog-and-runbook.md|changelog-and-runbook]]
- [[.ai/skills/evolve/diff-to-context.md|diff-to-context]]

### 2. 全栈开发最容易踩的坑

#### 需求侧
- 还没写清 Scope 就开始改代码
- 只写“做什么”，不写“不做什么”
- 不写 Blast Radius，导致联调时才发现影响了别的仓库

#### 设计侧
- 接口加了字段，但没同步 Contract Spec
- 事件改了 payload，但没检查 WebSocket / IPC 消费端
- 跨仓库改动不写发布顺序和兼容窗口

#### 实现侧
- 只改后端，不补前端 `types` / `api` 层
- 只改 UI 展示，不确认后端返回与事件推送是否一致
- 只顾一个端的代码，通过后却忽略整体链路

#### 验证侧
- 只看单测，不看真实运行结果
- 认为“接口通了”就代表“页面、事件、权限都对了”
- 忘记写最小发布说明和回滚动作

#### 沉淀侧
- 合并后不更新上下文
- 发生问题后只修一次，不提炼规则与 Runbook

### 3. 全栈开发的最小能力标准

完成 onboarding 后，一名合格的全栈开发应具备：

- 能把一个需求从自然语言整理成 Product / Change / Contract Spec
- 能判断这是后端改动、前端改动，还是跨仓库联动改动
- 能同时考虑 API、Vue 页面、WebSocket / IPC、权限和发布影响
- 能给出最小验证方案，而不是只给出实现代码
- 能在任务结束后提出至少一条沉淀建议

### 4. 全栈开发的一句话工作法

> **先把需求和边界写清楚，再把后端、前端、事件和发布当成一条链看待，而不是拆成互相甩锅的几个端。**

---

## 八、新人的第一条真实任务怎么做

建议直接跟着这个样板走：

- [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-skill-chain-demo.md|任务重试次数展示示例链路]]

### 你要做的不是“照抄”，而是练习这 4 个动作：
1. 从模板起手，而不是空白起手
2. 先写清影响面，再开始实现
3. 先把验收标准写成矩阵，再开始提测
4. 合并后思考：哪些知识要沉淀回 `.ai/`

### Mentor / Buddy 最好重点看你这 4 点
- Scope 有没有收敛
- Blast Radius 有没有写清
- Contract 有没有对齐
- 发布 / 回滚有没有写成可执行动作

---

## 九、onboarding 通过标准（Graduation Criteria）

当新人达到下面标准时，视为完成 onboarding：

### L1：理解型
- [ ] 能解释 5 类 Spec 的作用
- [ ] 能解释 Skill 五阶段
- [ ] 能读懂现有样板任务

### L2：跟练型
- [ ] 能在 mentor 陪同下完成一个小任务的 Product / Change / Contract Spec
- [ ] 能完成最小验收矩阵
- [ ] 能完成最小发布说明

### L3：独立型
- [ ] 能独立完成一个低风险任务的 Spec → Audit → Implement → Verify → Runbook 全链路
- [ ] 能识别出是否需要跨仓库设计审计
- [ ] 能在合并后提出至少一条沉淀建议（rule / context / skill / runbook）

团队实际可将 **L3** 作为 onboarding 结束标准。

---

## 十、推荐 mentor 带教方式

不要用“我讲你听”式 onboarding，建议采用 **1 讲解 + 1 跟练 + 1 独立任务** 的方式。

### Step 1：讲解
mentor 带新人走一遍现有示例任务链路。

### Step 2：跟练
新人基于模板写一个小需求，mentor 只纠正：
- 范围
- 契约
- 兼容性
- 回滚

### Step 3：独立
新人拿一个低风险真实任务，完整跑一遍链路，mentor 做终审。

最忌讳的 onboarding 方式是：

> 只给新人一堆代码仓库和几句口头说明，然后让他自己摸索。

在 AI 工程化模式下，这样会导致他直接回到旧习惯：想到哪改到哪。

---

## 十一、最常见的 onboarding 失败信号

如果新人出现以下情况，说明 onboarding 还没过关：

1. 一接到需求就先想改哪个文件，而不是先想影响边界
2. Product Spec 里没有 In Scope / Out of Scope
3. Change Spec 里没有 Blast Radius
4. 接口变更但没写 Contract Spec
5. 只会让 AI 生成代码，不会让 AI 生成验收矩阵、审计报告和 Runbook
6. 不知道热修复和普通功能流程的差异
7. 认为“测试过了就可以上线”
8. 合并后从不更新规则、上下文或 Skill

出现 2 条以上，建议重新进行带练。

---

## 十二、给新人的一句话工作法

如果你一时记不住全部流程，先按下面这句话工作：

> **先用模板写清楚，再用 Skill 审清楚，再让 AI 实现出来，最后用验证和 Runbook 把交付闭环补完整。**

---

## 十三、附：新人最小检查表

### 第一天结束前
- [ ] 看完方案总纲
- [ ] 看完使用手册
- [ ] 看完 Skill 索引
- [ ] 看完模板索引
- [ ] 看完一个完整示例任务

### 第一周结束前
- [ ] 独立写过 1 份 Product Spec
- [ ] 独立写过 1 份 Change Spec
- [ ] 跑过 1 次设计审计
- [ ] 补过 1 份验收矩阵
- [ ] 补过 1 份最小发布说明 / 回滚说明

### Onboarding 结束前
- [ ] 独立完成 1 个低风险真实任务全链路
- [ ] 与 mentor 完成复盘
- [ ] 提出至少 1 条可沉淀资产建议

---

## 十四、下一步推荐阅读

完成 onboarding 后，建议进入下面两类进阶阅读：

### 1. 全栈执行清单
如果后续补齐，建议优先阅读“角色分工与执行清单”的全栈版本或“全栈任务执行清单”。

### 2. 高风险场景样板
建议继续学习：
- 数据库迁移样板
- 热修复样板
- 跨仓库接口变更样板

这些样板会比当前的“任务重试次数展示”更接近真实复杂场景。

---

## 十五、总结

团队 onboarding 的目标，不是让新人背会多少 Skill 名称，而是让他尽快形成新的工作习惯：

- 需求先结构化
- 影响先可视化
- 契约先审计
- 实现先受约束
- 发布先可验证
- 经验最后要沉淀

当新人真正形成这套习惯后，他就不再只是“会用 AI 写代码的人”，而是：

> **能够在团队工程纪律下稳定驱动 AI 交付的工程成员。**
