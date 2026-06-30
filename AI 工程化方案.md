# AI 工程化方案 — 调度系统专版

> 基于 OMC 工程化思想，结合我们自身的技术架构（主调度服务 + 主调度Web + 看门狗Web + 看门狗Electron + 调度子系统 + PostgreSQL），设计一套完整的 AI 辅助工程化体系。

---

## 一、系统架构全景

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          调度系统全景架构                                    │
├──────────────┬──────────────┬──────────────┬──────────────┬────────────────┤
│  主调度服务   │  主调度 Web   │  看门狗 Web   │ 看门狗Electron│  调度子系统     │
│  (Backend)   │  (Frontend)  │  (Frontend)  │  (Desktop)   │  (Sub-system)  │
│              │              │              │              │                │
│  Python/Go   │  Vue/React   │  Vue/React   │  Electron    │  Python/Go     │
│  REST API    │  SPA Portal  │  Monitor UI  │  Local Agent │  Worker/Task   │
│  任务编排     │  任务管理     │  健康监控     │  本地守护     │  任务执行       │
├──────────────┴──────────────┴──────────────┴──────────────┴────────────────┤
│                         PostgreSQL (共享数据层)                              │
│                    任务表 / 状态表 / 配置表 / 日志表 / 用户表                   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 二、记忆与约束分层逻辑

核心原则：**AI 的行为质量 = 记忆深度 × 约束强度**。记忆让它"知道"，约束让它"守规矩"。

### 2.1 四层记忆金字塔

```
┌────────────────────────────────────────────────────────────────────┐
│  L4 · 全局记忆 (Global Memory)                                     │
│  ── 跨仓库、跨模块的永恒真理                                        │
│  ── 技术栈声明、数据库Schema、系统边界定义                            │
│  ── 存放位置：中央规则仓 / CLAUDE.md (根级)                          │
├────────────────────────────────────────────────────────────────────┤
│  L3 · 仓库记忆 (Repo Memory)                                       │
│  ── 单仓库内的架构约定与代码风格                                     │
│  ── API路由规范、组件命名约定、状态管理模式                            │
│  ── 存放位置：各仓库根目录 CLAUDE.md                                 │
├────────────────────────────────────────────────────────────────────┤
│  L2 · 模块记忆 (Module Memory)                                     │
│  ── 模块/服务内部的业务逻辑与数据流                                   │
│  ── 状态机定义、事务边界、副作用声明                                  │
│  ── 存放位置：模块目录下的 .ai/context.md                            │
├────────────────────────────────────────────────────────────────────┤
│  L1 · 任务记忆 (Task Memory)                                       │
│  ── 当前任务的 Spec、BDD场景、临时约束                                │
│  ── 存放位置：任务分支的 .ai/task-spec.md                            │
└────────────────────────────────────────────────────────────────────┘
```

#### 各层记忆的具体内容定义

| 层级 | 记忆内容 | 示例 | 更新频率 | 负责人 |
|------|----------|------|----------|--------|
| **L4 全局** | 技术栈锁定 | "后端统一 Python 3.11 + FastAPI，前端统一 Vue 3 + TypeScript" | 极低（架构变更时） | 架构组 |
| **L4 全局** | 数据库 Schema 全景 | PostgreSQL 表结构 ER 图、字段枚举值、外键关系 | 低（版本迭代时） | 架构组 |
| **L4 全局** | 跨仓库通信协议 | 主调度服务 → 调度子系统的 RPC/HTTP 协议格式 | 低 | 架构组 |
| **L4 全局** | 安全红线 | "禁止明文存储密码、禁止硬编码数据库连接、禁止未鉴权接口暴露" | 极低 | 架构组 |
| **L3 仓库** | 代码风格 | ESLint/Prettier 配置、Python Black/Ruff 配置 | 中 | 工具组 |
| **L3 仓库** | 目录结构约定 | `src/api/` `src/models/` `src/services/` 分层规则 | 低 | 架构组 |
| **L3 仓库** | 组件库使用规范 | "禁止直接使用 axios，统一使用 @/utils/httpRequest" | 中 | 工具组 |
| **L3 仓库** | 数据库访问规范 | "统一使用 SQLAlchemy ORM，禁止裸 SQL，事务必须显式声明" | 低 | 架构组 |
| **L2 模块** | 状态机定义 | 任务状态流转矩阵：PENDING → RUNNING → SUCCESS/FAILED | 中 | 业务设计组 |
| **L2 模块** | 事务边界 | "任务创建 + 资源锁定必须在同一事务中" | 中 | 业务设计组 |
| **L2 模块** | 副作用声明 | "任务状态变为 RUNNING 时，必须推送 WebSocket 通知" | 中 | 业务设计组 |
| **L1 任务** | BDD 场景 | Given-When-Then 用例（正向/逆向/异常三路径） | 高（每个任务） | 业务设计组 |
| **L1 任务** | 临时约束 | "本次修改仅涉及看门狗 Web 的告警展示模块" | 高 | 开发者 |

### 2.2 三层约束体系

```
┌────────────────────────────────────────────────────────────────────┐
│  C3 · 硬约束 (Hard Constraints) — 不可违反，违反即阻断              │
│  ── CI/CD 自动化卡点：Lint失败→拒、覆盖率<85%→拒、安全扫描→拒       │
│  ── 数据库约束：外键检查、枚举值检查、NOT NULL 检查                  │
│  ── 接口契约：OpenAPI Schema 严格校验                               │
├────────────────────────────────────────────────────────────────────┤
│  C2 · 软约束 (Soft Constraints) — 建议遵守，偏离需说明理由          │
│  ── 代码风格偏好（命名规范、注释密度）                                │
│  ── 性能建议（查询耗时<200ms、批量操作分页）                          │
│  ── 设计模式偏好（优先使用现有组件而非新建）                           │
├────────────────────────────────────────────────────────────────────┤
│  C1 · 情境约束 (Context Constraints) — 当前任务特有，任务结束释放    │
│  ── "本次只改看门狗 Electron，不动主调度服务"                        │
│  ── "此功能为实验性，允许简化错误处理"                                │
│  ── "紧急修复，允许跳过非核心测试"                                   │
└────────────────────────────────────────────────────────────────────┘
```

---

## 三、AI 工作流定义

### 3.1 核心工作流：Spec → Code 全链路

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Spec     │───>│  设计     │───>│  编码     │───>│  校验     │───>│  合入     │
│  编写     │    │  审计     │    │  生成     │    │  防线     │    │  主干     │
│ (人类)    │    │ (AI+人类) │    │ (AI)     │    │ (AI+CI)  │    │ (人类)    │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
     │               │               │               │               │
     │  L1任务记忆    │  L2模块记忆    │  L3仓库记忆    │  C3硬约束      │  C3硬约束
     │  BDD场景       │  状态机        │  代码风格      │  Lint/Test     │  PR Review
     │  契约定义       │  事务边界      │  组件规范      │  Coverage      │  人工终审
```

### 3.2 五阶段详细流程

#### 阶段 1：Spec 编写（人类主导）

**输入**：产品需求文档
**输出**：结构化 Spec（契约层 + 行为层 + 规则层）

Spec 必须包含以下三部分，缺一不可：

```markdown
# Spec: [功能名称]

## 1. 契约层 (Contract)
- API: POST /api/v1/dispatch/tasks/create
- Request: { taskType: Enum[ANALYSIS, REPORT, EXPORT], priority: Int[1-5], params: JSON }
- Response: 200 { taskId: String, status: Enum[PENDING] } / 400 { code: String, message: String }
- 数据库变更: 新增字段 `task.retry_count` (Int, Default=0, NOT NULL)

## 2. 行为层 (BDD Scenarios)

### 正向路径
- Given: 用户提交 ANALYSIS 类型任务，参数合法
- When: 调用 create 接口
- Then: 返回 taskId，数据库 task 表新增一条 PENDING 状态记录
- Then: 【副作用】推送 WebSocket 事件 `task_created` 到看门狗 Web

### 逆向路径
- Given: 用户提交不支持的 taskType 枚举值
- When: 调用 create 接口
- Then: 返回 400，错误码 INVALID_TASK_TYPE

### 异常路径
- Given: PostgreSQL 连接超时
- When: 调用 create 接口
- Then: 返回 503，触发看门狗告警，任务不写入数据库（事务回滚）

## 3. 规则层 (Constraints)
- 性能：任务创建接口响应时间 ≤ 100ms
- 并发：同一 taskType 同时运行上限为 10
- 安全：必须鉴权，非 admin 角色只能创建 priority ≤ 3 的任务
```

#### 阶段 2：设计审计（AI + 人类）

**目的**：在编码前发现 Spec 的逻辑漏洞

| 审计维度 | 审计内容 | 执行者 |
|----------|----------|--------|
| **跨仓库一致性** | Spec 是否违反了主调度服务与调度子系统之间的通信协议？ | AI (读取 L4 全局记忆) |
| **数据库兼容性** | 新增字段/表是否与现有 Schema 冲突？外键关系是否完整？ | AI (读取 L4 Schema 记忆) |
| **状态机完整性** | 状态流转是否有"死胡同"？是否有未定义的非法跳转？ | AI + 人类 |
| **副作用遗漏** | 是否遗漏了 WebSocket 推送、日志记录、缓存更新等副作用？ | AI |
| **边界值覆盖** | 等价类划分是否完整？并发/超时/异常是否覆盖？ | 人类终审 |

#### 阶段 3：编码生成（AI 主导）

**核心规则**：AI 编码时必须同时加载四层记忆 + 三层约束

```
AI 编码时的记忆加载顺序：
  1. L4 全局记忆 → 确定技术栈、数据库结构、跨仓库协议
  2. L3 仓库记忆 → 确定代码风格、目录结构、组件库规范
  3. L2 模块记忆 → 确定状态机、事务边界、副作用
  4. L1 任务记忆 → 确定本次 BDD 场景、临时约束
  5. C3 硬约束   → 编码过程中实时自检（类型安全、空值检查）
  6. C2 软约束   → 编码风格偏好
  7. C1 情境约束 → 本次任务特有规则
```

**编码产出要求**：

| 产出 | 要求 | 存放位置 |
|------|------|----------|
| 功能代码 | 完整实现 Spec 中所有 BDD 场景 | 各仓库对应模块 |
| 单元测试 | 覆盖正向 + 逆向 + 异常三条路径 | `tests/` 目录 |
| 接口测试 | 至少覆盖所有 API 端点的 Happy Path | `tests/api/` 目录 |
| 变更说明 | 自动生成 CHANGELOG 条目 | `CHANGELOG.md` |

#### 阶段 4：校验防线（AI + CI 自动化）

**三道关卡，全部自动化，零人工干预**：

```
┌─────────────────────────────────────────────────────────────────┐
│  第一道关：静态代码检查                                          │
│  ── ESLint / Prettier / Black / Ruff                            │
│  ── SonarQube 圈复杂度 ≤ 15、重复率 ≤ 3%                         │
│  ── TypeCheck (TypeScript strict / Python mypy)                 │
│  ── ❌ 不通过 → 自动关闭 PR，附修复建议                           │
├─────────────────────────────────────────────────────────────────┤
│  第二道关：自动化测试                                            │
│  ── 单元测试覆盖率 ≥ 85%                                        │
│  ── BDD 接口测试 100% 通过                                       │
│  ── E2E 测试关键路径无阻断                                       │
│  ── ❌ 不通过 → 自动关闭 PR，附失败报告                           │
├─────────────────────────────────────────────────────────────────┤
│  第三道关：AI 审计合规                                           │
│  ── 检查是否引入未登记的第三方依赖                                │
│  ── 检查是否有未捕获的全局异常                                    │
│  ── 检查是否有硬编码的数据库连接/密钥                              │
│  ── 检查跨仓库接口是否与全局协议一致                               │
│  ── ❌ 不通过 → 生成审计报告，标记问题行号                         │
└─────────────────────────────────────────────────────────────────┤
```

#### 阶段 5：合入主干（人类终审）

**人类只做三件事**：

1. **业务逻辑终审**：代码是否忠实实现了 Spec 的业务意图？
2. **系统边界审核**：改动是否溢出了本次任务的范围？是否影响了其他仓库？
3. **合并决策**：通过 → 合入主干；不通过 → 打回，附具体原因。

---

## 四、各仓库专属记忆配置

### 4.1 主调度服务 (Backend)

```markdown
# CLAUDE.md — 主调度服务

## 技术栈
- Python 3.11 + FastAPI
- SQLAlchemy ORM + PostgreSQL
- Redis (缓存 + 分布式锁)
- WebSocket (实时推送)

## 目录结构约定
src/
  api/          # REST API 路由（按资源分组）
  models/       # SQLAlchemy 数据模型
  services/     # 业务逻辑层（事务边界在此声明）
  tasks/        # 异步任务定义
  utils/        # 工具函数（禁止在此写业务逻辑）

## 禁令
- 禁止裸 SQL，统一使用 SQLAlchemy ORM
- 禁止直接使用 requests/httpx，统一使用 src/utils/http_client.py
- 禁止在 API 层写业务逻辑，必须下沉到 services/
- 禁止跨事务操作不声明事务边界
- 禁止硬编码数据库连接字符串

## 数据库约定
- 所有时间字段统一使用 UTC，类型为 DateTime(timezone=True)
- 所有枚举字段使用 PostgreSQL ENUM 类型，禁止用 String 存枚举
- 外键必须声明 ondelete 约束
- 表名统一小写 + 下划线：task_execution_log
```

### 4.2 主调度 Web (Frontend)

```markdown
# CLAUDE.md — 主调度 Web

## 技术栈
- Vue 3 + TypeScript + Vite
- Pinia (状态管理)
- Element Plus (UI组件库)
- Axios (统一封装为 @/utils/httpRequest)

## 目录结构约定
src/
  views/        # 页面组件（按业务模块分组）
  components/   # 通用组件（禁止在 views/ 写通用组件）
  stores/       # Pinia Store（按业务域分组）
  api/          # API 调用层（一个资源一个文件）
  utils/        # 工具函数
  types/        # TypeScript 类型定义

## 禁令
- 禁止直接使用 axios，统一使用 @/utils/httpRequest
- 禁止在组件内直接调用 API，必须通过 api/ 层
- 禁止使用 localStorage 存敏感数据
- 禁止在 Pinia Store 写 UI 逻辑
- 禁止新增未在 Element Plus 中的基础 UI 组件（优先使用现有组件库）
```

### 4.3 看门狗 Web (Monitor Frontend)

```markdown
# CLAUDE.md — 看门狗 Web

## 技术栈
- Vue 3 + TypeScript + Vite
- Pinia (状态管理)
- WebSocket (实时监控数据推送)
- ECharts / Chart.js (监控图表)

## 特殊约定
- 监控数据必须通过 WebSocket 实时更新，禁止轮询
- 告警阈值展示必须与后端配置一致，禁止前端硬编码阈值
- 告警列表必须支持实时追加（WebSocket push），禁止全量刷新
- 图表渲染必须在数据到达后 50ms 内完成

## 禁令
- 禁止使用 setInterval 轮询后端数据
- 禁止在前端计算告警阈值（阈值由后端定义）
- 禁止在监控页面写业务调度逻辑
```

### 4.4 看门狗 Electron (Desktop Agent)

```markdown
# CLAUDE.md — 看门狗 Electron

## 技术栈
- Electron 28+ + Node.js
- Vue 3 (渲染进程 UI)
- IPC (主进程 ↔ 渲染进程通信)
- node-notifier (系统通知)

## 特殊约定
- 主进程只负责系统级操作（文件访问、系统通知、进程守护）
- 渲染进程只负责 UI 展示，所有系统操作必须通过 IPC 委托主进程
- 本地配置文件存放在 electron.app.getPath('userData') 下
- 进程守护逻辑：检测调度子系统进程存活，死亡时自动重启 + 告警推送

## 禁令
- 禁止在渲染进程直接访问文件系统
- 禁止在渲染进程直接调用 child_process
- 禁止硬编码本地路径，统一使用 electron.app.getPath()
- 禁止绕过 IPC 直接在渲染进程执行系统级操作
```

### 4.5 调度子系统 (Task Worker)

```markdown
# CLAUDE.md — 调度子系统

## 技术栈
- Python 3.11 + FastAPI (轻量服务)
- Celery / 自研任务队列
- PostgreSQL (只读任务表 + 写入执行日志)
- Redis (任务状态缓存)

## 特殊约定
- 调度子系统只执行任务，不编排任务（编排由主调度服务负责）
- 任务执行日志必须实时写入 PostgreSQL，同时推送状态到 Redis
- 任务失败必须重试（最多 retry_count 次），超过后标记为 FAILED
- 子系统与主调度服务通信必须通过定义好的 HTTP/RPC 协议，禁止直连数据库写任务表

## 禁令
- 禁止修改 task 表的核心字段（status 由主调度服务控制）
- 禁止绕过协议直接操作主调度服务的数据库
- 禁止在子系统内实现任务编排逻辑
- 禁止任务执行超时不处理（必须有超时检测 + 自动标记 FAILED）
```

---

## 五、跨仓库协作约束

### 5.1 数据库共享约束

PostgreSQL 是所有仓库的共享数据层，必须遵守以下铁律：

```markdown
# 数据库铁律 (Database Iron Rules)

## 表归属原则
- 每张表有明确的"主人仓库"
- 主调度服务：task, task_config, user, schedule 等核心表（读写）
- 调度子系统：task_execution_log, task_metric 等执行表（读写）
- 看门狗：alert_config, alert_history 等告警表（读写）
- 其他仓库：只读，通过 API 获取数据

## 写入约束
- 禁止非主人仓库直接写入他人表（必须通过 API）
- 禁止跨仓库在同一事务中操作多张表
- 禁止 ALTER TABLE 不经过全局 Schema 审批

## 迁移约束
- 所有数据库迁移必须使用 Migration 文件（Alembic / Flyway）
- 迁移文件必须包含 rollback 脚本
- 迁移必须先在测试环境验证，再在生产环境执行
```

### 5.2 跨仓库通信协议

```markdown
# 跨仓库通信协议

## 主调度服务 ↔ 调度子系统
- 协议：HTTP REST API（内网通信）
- 认证：Service Token（非用户 Token）
- 格式：JSON，字段命名统一 snake_case
- 超时：5s 连接 + 30s 读取

## 主调度服务 ↔ 看门狗 Web/Electron
- 协议：WebSocket（实时推送）+ REST API（查询）
- 推送事件：task_created, task_status_changed, alert_triggered
- 消息格式：{ event: String, data: JSON, timestamp: ISO8601 }

## 禁令
- 禁止跨仓库直连数据库（除表主人）
- 禁止跨仓库使用文件共享通信
- 禁止跨仓库硬编码对方服务的 IP/端口
```

---

## 六、Spec 与编码可采用的具体流程方案

### 6.1 方案 A：Spec-Driven TDD（推荐 — 适合核心业务逻辑）

```
人类写 Spec (契约+BDD+规则)
        │
        ▼
AI 读取 Spec → 生成测试代码 (先写测试)
        │
        ▼
AI 读取测试 → 生成功能代码 (让测试通过)
        │
        ▼
AI 自检：覆盖率 ≥ 85%？所有 BDD 场景通过？
        │
        ▼
提交 PR → CI 三道关卡 → 人类终审 → 合入
```

**适用场景**：
- 主调度服务的任务编排逻辑
- 调度子系统的任务执行与重试逻辑
- 涉及数据库事务的操作
- 涉及状态流转的核心业务

### 6.2 方案 B：Spec-Driven Prototyping（适合 UI / 前端开发）

```
人类写 Spec (契约+UI交互描述+规则)
        │
        ▼
AI 读取 Spec + 全局记忆 → 生成 UI 原型代码
        │
        ▼
人类审查 UI 效果 → 反馈调整
        │
        ▼
AI 补充单元测试 + E2E 测试
        │
        ▼
提交 PR → CI 三道关卡 → 人类终审 → 合入
```

**适用场景**：
- 主调度 Web 的页面开发
- 看门狗 Web 的监控仪表盘
- 看门狗 Electron 的桌面 UI

### 6.3 方案 C：Spec-Driven Hotfix（适合紧急修复）

```
人类写精简 Spec (仅契约+核心BDD场景)
        │
        ▼
AI 读取 Spec + C1情境约束("紧急修复，允许简化") → 生成修复代码
        │
        ▼
AI 生成核心路径测试（允许覆盖率放宽至 60%）
        │
        ▼
提交 PR → CI 第一+第三道关卡（跳过覆盖率门槛）→ 人类终审 → 合入
        │
        ▼
后续补全：补写完整测试，覆盖率恢复至 85%
```

**适用场景**：
- 生产环境紧急 Bug 修复
- 看门狗告警误报修复
- 调度子系统任务卡死紧急处理

### 6.4 方案选择矩阵

| 任务类型 | 推荐方案 | Spec 颗粒度 | 测试要求 | 审计强度 |
|----------|----------|-------------|----------|----------|
| 核心业务逻辑（任务编排/状态流转） | **方案 A** | 契约+BDD+规则 全量 | ≥85% 覆盖率 | 三道关卡全开 |
| 前端页面开发 | **方案 B** | 契约+UI交互 | ≥70% 覆盖率 | 第一+第三道关卡 |
| 数据库迁移 | **方案 A** | Schema 变更+回滚脚本 | Migration 测试 | 三道关卡全开 |
| 跨仓库接口变更 | **方案 A** | 协议变更+兼容性声明 | ≥85% 覆盖率 | 三道关卡全开 |
| 紧急修复 | **方案 C** | 精简契约+核心场景 | ≥60% 覆盖率 | 第一+第三道关卡 |
| 实验性功能 | **方案 B** | 契约+交互描述 | ≥50% 覆盖率 | 第一道关卡 |

---

## 七、落地实施路线图

### 第 1 周：基础设施搭建

| 任务 | 负责人 | 产出 |
|------|--------|------|
| 建立中央规则仓（Monorepo Rules） | 架构组 | `.ai/rules/` 全局规则库 |
| 编写各仓库 CLAUDE.md | 架构组 + 工具组 | 5 个仓库的 L3 记忆文件 |
| 配置 CI 基础卡点（Lint + TypeCheck） | 工具组 | GitHub Actions / GitLab CI 配置 |
| 定义 PostgreSQL Schema 全景文档 | 架构组 | L4 数据库记忆文件 |

### 第 2-3 周：试点跑通

| 任务 | 负责人 | 产出 |
|------|--------|------|
| 选 1 个模块用方案 A 跑通完整流程 | 业务设计组 + 开发 | 验证 Spec → TDD 全链路 |
| 选 1 个前端页面用方案 B 跑通 | 业务设计组 + 开发 | 验证 Spec → Prototyping 流程 |
| 配置 CI 第二道关卡（测试覆盖率） | 工具组 | Coverage 门槛配置 |
| 配置 CI 第三道关卡（AI 审计） | 工具组 | CodeRabbit / 自研审计脚本 |
| 编写 L2 模块记忆（试点模块） | 业务设计组 | 状态机 + 事务边界文档 |

### 第 4 周起：全面推广

| 任务 | 负责人 | 产出 |
|------|--------|------|
| 全仓库推广 Spec-Driven 开发 | 全团队 | 所有新需求走 Spec 流程 |
| 补全所有模块的 L2 记忆 | 业务设计组 | 每个模块的 context.md |
| 建立跨仓库协作审批流程 | 架构组 | 跨仓库 PR 审批规则 |
| 沉淀内部组件库（Domain LEGOs） | 工具组 | 统一 HTTP 客户端、统一通知组件等 |
| 每月回顾：更新 L4/L3 记忆 | 架构组 + 工具组 | 规则库版本迭代 |

---

## 八、关键文件结构

```
调度系统项目根/
├── .ai/                          # AI 工程化配置根目录
│   ├── rules/                    # L4 全局记忆（中央规则仓）
│   │   ├── tech-stack.md         # 技术栈锁定声明
│   │   ├── db-schema.md          # PostgreSQL 全景 Schema
│   │   ├── cross-repo-protocol.md # 跨仓库通信协议
│   │   ├── security-redlines.md  # 安全红线
│   │   └── db-iron-rules.md      # 数据库铁律
│   ├── skills/                   # AI Skills 定义（可被 OMC/Cline/Claude 读取）
│   │   ├── spec-writer.md        # Spec 编写 Skill
│   │   ├── tdd-coder.md          # TDD 编码 Skill
│   │   ├── design-auditor.md     # 设计审计 Skill
│   │   └── db-migrator.md        # 数据库迁移 Skill
│   └── templates/                # Spec 模板库
│   │   ├── spec-template-core.md # 核心业务 Spec 模板
│   │   ├── spec-template-ui.md   # 前端 UI Spec 模板
│   │   └── spec-template-hotfix.md # 紧急修复 Spec 模板
│
├── 主调度服务/
│   ├── CLAUDE.md                 # L3 仓库记忆
│   ├── .ai/
│   │   ├── context.md            # L2 模块记忆（主调度核心模块）
│   │   └── task-specs/           # L1 任务记忆（按分支存放）
│   ├── src/
│   ├── tests/
│   └── ...
│
├── 主调度Web/
│   ├── CLAUDE.md                 # L3 仓库记忆
│   ├── .ai/
│   │   ├── context.md
│   │   └── task-specs/
│   └── ...
│
├── 看门狗Web/
│   ├── CLAUDE.md                 # L3 仓库记忆
│   ├── .ai/
│   │   ├── context.md
│   │   └── task-specs/
│   └── ...
│
├── 看门狗Electron/
│   ├── CLAUDE.md                 # L3 仓库记忆
│   ├── .ai/
│   │   ├── context.md
│   │   └── task-specs/
│   └── ...
│
├── 调度子系统/
│   ├── CLAUDE.md                 # L3 仓库记忆
│   ├── .ai/
│   │   ├── context.md
│   │   └── task-specs/
│   └── ...
│
└── ci/                           # CI/CD 配置
    ├── lint-pipeline.yml         # 第一道关卡
    ├── test-pipeline.yml         # 第二道关卡
    ├── audit-pipeline.yml        # 第三道关卡
    └── merge-gate.yml            # 合入关卡
```

---

## 九、总结：核心设计哲学

| 原则 | 释义 |
|------|------|
| **Spec 是圣旨** | Spec 不是文档，是 AI 的输入。写 Spec = 做静态审计。 |
| **记忆分层加载** | AI 不是"裸跑"，每次编码都从 L4→L3→L2→L1 逐层加载上下文。 |
| **约束分级阻断** | 硬约束（C3）自动化阻断，软约束（C2）偏离需说明，情境约束（C1）临时生效。 |
| **人类终审权** | AI 做所有机械工作（编码/测试/审计），人类只做三件事：写 Spec、审边界、做终审。 |
| **跨仓库铁律** | 数据库有主人、通信走协议、改动需审批。禁止"野蛮"跨仓库操作。 |
| **渐进式落地** | 第 1 周搭基础设施 → 第 2-3 周试点 → 第 4 周全面推广。不搞一夜转型。 |

---

## 十、OpenSpec × Superpowers 双引擎增强层

当前方案已经解决了“记忆如何分层、约束如何阻断、代码如何过流水线”的问题，但还缺一个更清晰的双引擎定义：

- **OpenSpec**：把“要做什么、做到什么程度、影响哪些边界”定义成机器可执行的真理源。
- **Superpowers**：把“AI 应该如何稳定执行、如何检索上下文、如何自检、如何合规交付”固化成默认能力层。

一句话总结：**OpenSpec 负责定义目标，Superpowers 负责保证执行质量。**

### 10.1 OpenSpec 新增分层

在原有 L1 任务记忆之上，建议把 Spec 进一步蒸馏成 5 类标准工件：

| Spec 类型 | 作用 | 推荐格式 | 产出时机 | 主要消费者 |
|----------|------|----------|----------|------------|
| **Product Spec** | 描述业务目标、用户行为、验收标准 | Markdown + BDD | 需求进入开发前 | 产品 / 开发 / 测试 / AI |
| **Change Spec** | 描述本次改动影响的仓库、模块、表、接口、事件 | Markdown + YAML Frontmatter | 建分支后立即生成 | AI / Reviewer / CI |
| **Contract Spec** | API、事件、Schema、状态机、枚举、兼容性声明 | OpenAPI / JSON Schema / Mermaid | 设计审计阶段 | 前后端 / 子系统 / CI |
| **Execution Spec** | 测试矩阵、迁移顺序、发布步骤、回滚策略 | Checklist + Runbook | 开发完成前 | CI / 发布 / 运维 |
| **Hotfix Spec** | 最小修复面、风险面、临时豁免、补账计划 | Markdown 模板 | 紧急修复时 | 开发 / 审计 / 值班 |

### 10.2 OpenSpec 最小必填字段

每个任务 Spec 不应只写“接口 + BDD”，还应强制补齐以下字段：

```yaml
spec_id: DISPATCH-2026-001
change_type: feature | refactor | migration | hotfix
repos: [主调度服务, 主调度Web]
owners: [架构组, 开发]
blast_radius: api | db | websocket | electron | worker
compatibility: backward-compatible | breaking
release_guard: feature_flag | canary | direct
rollback: required
```

这样做的价值是：AI 不只是知道“要实现什么功能”，还知道“会动到哪些边界、是否允许破坏兼容、上线如何回滚”。

### 10.3 Superpowers 能力层

建议在现有记忆/约束体系外，再补 6 类默认超能力：

| Superpower | 核心能力 | 对当前体系的补位 |
|------------|----------|------------------|
| **Context Loader** | 自动加载 L4→L1、关联仓库约束、最近变更记录 | 防止 AI 只看当前文件“裸跑” |
| **Impact Mapper** | 从 Spec 推导受影响仓库/表/API/WebSocket/IPC | 补足跨仓库影响分析 |
| **Contract Guardian** | 校验 OpenAPI / JSON Schema / 事件协议 / 状态机 | 把“设计审计”变成可执行检查 |
| **Test Synthesizer** | 从 BDD 自动生成单测、接口测试、E2E 骨架 | 保证测试与 Spec 同步演进 |
| **Release Guard** | 按变更类型选择 CI 卡点、灰度、回滚、值班检查 | 把工程化延伸到发布阶段 |
| **Knowledge Curator** | 将事故、复盘、常见修复沉淀回 L4/L3/L2/skills | 形成闭环学习，而非一次性 Prompt |

---

## 十一、与现有开发工作流的工程化整合

### 11.1 建议的端到端工作流

```text
需求/缺陷 → 建分支 → 生成 Change Spec → 设计审计 → AI 实现 → PR → CI/CD → 灰度/发布 → 复盘沉淀
```

### 11.2 各阶段挂钩点

| 阶段 | 人类动作 | AI/Skill 动作 | 关键工件 | 关卡 |
|------|----------|---------------|----------|------|
| **需求进入** | 明确业务目标与范围 | 生成 Product Spec 初稿 | `spec.md` | 无 |
| **建分支** | 创建任务分支/关联工单 | 生成 Change Spec、影响面清单 | `change-spec.md` | 范围检查 |
| **设计审计** | 确认状态机/事务边界 | 校验 Contract、数据库、事件、兼容性 | `contract/` `audit-report.md` | 审计通过 |
| **实现阶段** | 审核关键实现策略 | 代码生成、测试生成、迁移脚本生成 | code + tests + migration | 本地自检 |
| **PR 阶段** | 看 Diff、看边界、看业务意图 | 自动摘要、自动审计、自动回归建议 | PR Body / audit report | Review |
| **CI/CD** | 只处理失败例外 | Lint/Test/Security/Compatibility/Release Guard | pipeline report | 必须全绿 |
| **发布/灰度** | 决策是否放量 | 执行 runbook、检查监控、验证回滚 | release note | 放量门禁 |
| **复盘沉淀** | 审阅经验是否可复用 | 更新 rules/context/skill/runbook | 记忆与技能资产 | 知识回流 |

### 11.3 分支、PR、发布策略建议

| 场景 | 分支命名 | 必选工件 | CI 策略 | 发布策略 |
|------|----------|----------|---------|----------|
| **普通功能** | `feature/<ticket>-<slug>` | Product + Change + Contract Spec | 三道关卡全开 | Feature Flag / 小流量灰度 |
| **跨仓库改动** | `feature/xrepo-<ticket>` | 兼容性声明 + 影响矩阵 + 发布顺序 | 增加协议兼容检查 | 分批发布，先协议提供方 |
| **数据库迁移** | `migration/<ticket>` | Migration Spec + Rollback Script | 增加迁移前后校验 | 先预发验证，再生产 |
| **紧急修复** | `hotfix/<ticket>` | Hotfix Spec + 风险说明 | 跳过非关键覆盖率，保留审计 | 直接发布 + 事后补测 |
| **前端原型/UI** | `prototype/<ticket>` | UI Spec + 交互清单 | 降低覆盖率门槛，保留回归 | 预览环境确认后合入 |

### 11.4 与技术栈的绑定方式

| 技术栈 | OpenSpec 约束 | Superpowers 默认动作 |
|--------|---------------|----------------------|
| **FastAPI** | OpenAPI 为接口真理源；错误码枚举必须显式定义 | 自动校验路由、请求/响应 Schema、一致性测试 |
| **Vue 3 + TS** | 页面状态、交互流、接口依赖写入 UI Spec | 自动生成 store/api/types/test 骨架 |
| **Electron** | IPC 通道、权限边界、主渲染职责必须入 Spec | 自动检查是否绕过 IPC、是否访问本地敏感路径 |
| **PostgreSQL** | 表归属、迁移、回滚、枚举变更必须声明 | 自动检查 Migration、索引、事务边界 |
| **WebSocket** | 事件名、payload、重放/幂等语义入 Contract Spec | 自动校验事件兼容性与前后端订阅一致性 |
| **Redis** | 缓存键、TTL、失效策略、锁粒度入规则层 | 自动检查缓存击穿、锁释放、幂等风险 |

---

## 十二、蒸馏后的工程化 Skill 集合

原则：**Skill 不是 Prompt 碎片，而是可复用的工程动作单元。** 建议按“Spec → 设计 → 实现 → 验证 → 沉淀”五段建设。

### 12.1 Spec 阶段 Skills

| Skill 名称 | 职责 | 触发时机 | 输入 | 输出 |
|------------|------|----------|------|------|
| **spec-intake** | 将需求/缺陷整理为 Product Spec | 新需求进入时 | PRD/工单/会议记录 | `spec.md` |
| **change-scope-mapper** | 识别影响仓库、模块、表、协议、事件 | 建分支后 | Spec + 代码仓结构 | `change-spec.md` |
| **bdd-expander** | 将需求展开为 Given-When-Then 与边界场景 | Spec 初稿后 | Product Spec | BDD 场景清单 |
| **acceptance-matrix-builder** | 生成验收矩阵与测试覆盖目标 | 进入实现前 | BDD + 约束 | 测试矩阵 |

### 12.2 设计阶段 Skills

| Skill 名称 | 职责 | 触发时机 | 输入 | 输出 |
|------------|------|----------|------|------|
| **contract-auditor** | 审计 API / WebSocket / IPC / Schema 一致性 | 设计审计阶段 | Contract Spec | `audit-report.md` |
| **state-machine-checker** | 检查状态流转死胡同、非法跳转、补偿路径 | 涉及状态流时 | 状态机定义 | 状态机审计报告 |
| **migration-planner** | 设计 PostgreSQL 迁移、回滚、兼容窗口 | 有 DB 变更时 | Schema 变更 | migration plan |
| **architecture-impact-brief** | 输出跨仓库影响图与发布顺序 | 跨仓库变更时 | Change Spec | 图 + 顺序清单 |

### 12.3 实现阶段 Skills

| Skill 名称 | 职责 | 触发时机 | 输入 | 输出 |
|------------|------|----------|------|------|
| **tdd-implementer** | 先生成测试，再生成实现代码 | 核心后端/状态机逻辑 | Spec + 测试矩阵 | code + unit tests |
| **ui-prototyper** | 生成 Vue/Electron 原型与交互骨架 | 页面/桌面功能开发 | UI Spec | views/components/stores |
| **api-contract-coder** | 按 OpenAPI/Schema 生成路由、类型、客户端 | 接口开发时 | Contract Spec | API code + types |
| **event-wiring-builder** | 生成 WebSocket/IPC 事件接线与订阅代码 | 实时推送/桌面代理场景 | Event Spec | handlers + tests |

### 12.4 验证阶段 Skills

| Skill 名称 | 职责 | 触发时机 | 输入 | 输出 |
|------------|------|----------|------|------|
| **spec-test-sync** | 检查 Spec、测试、实现是否一致 | PR 前 | Spec + tests + diff | 差异报告 |
| **release-gate-checker** | 根据变更类型选择并执行发布前门禁 | 合并前/发布前 | Change Spec + CI 结果 | 放行/阻断结论 |
| **runtime-verifier** | 联动现有 `run` / `verify` 能力做真实运行验证 | 关键功能改动后 | 应用 + 场景清单 | 运行验证报告 |
| **security-boundary-reviewer** | 联动现有 `security-review` 检查密钥、鉴权、IPC/SQL 风险 | 安全敏感改动 | diff + rules | 安全审计报告 |

### 12.5 沉淀阶段 Skills

| Skill 名称 | 职责 | 触发时机 | 输入 | 输出 |
|------------|------|----------|------|------|
| **incident-to-rule** | 将事故/线上问题提炼成规则与门禁 | 事故复盘后 | 复盘记录 | L4/L3 规则更新 |
| **diff-to-context** | 把稳定实现回填到 L2 模块记忆 | 合并后 | merged diff | `context.md` 更新建议 |
| **skill-evolver** | 从高频任务中沉淀新 skill 模板 | 月度回顾 | 任务记录/Prompt | 新 skill 草案 |
| **changelog-and-runbook** | 生成变更说明、发布说明、回滚说明 | 发布前 | diff + Execution Spec | CHANGELOG + runbook |

---

## 十三、与现有能力的映射关系

可以把“新 skills”分成两类：

1. **直接复用现有能力**：`code-review`、`security-review`、`verify`、`run`、`architecture-diagram`、`drawio`
2. **围绕 OpenSpec × Superpowers 新增的工程化 skill**：`change-scope-mapper`、`contract-auditor`、`spec-test-sync`、`release-gate-checker`、`incident-to-rule` 等

推荐映射如下：

| 现有能力 | 在新体系中的位置 | 推荐包装方式 |
|----------|------------------|--------------|
| **code-review** | PR 阶段质量审计 | 作为 `release-gate-checker` 的一个子步骤 |
| **security-review** | 第三道关卡/安全红线检查 | 作为 `security-boundary-reviewer` 的底层能力 |
| **verify** | 发布前真实行为验证 | 作为 `runtime-verifier` 的执行器 |
| **run** | 本地/预发启动与冒烟 | 与 `runtime-verifier` 联动 |
| **architecture-diagram / drawio** | 跨仓库影响图、状态机图、发布拓扑图 | 作为 `architecture-impact-brief` 的图形产物 |
| **simplify** | 合并前降复杂度 | 作为实现后优化步骤，不替代正确性校验 |

---

## 十四、落地建议：Skill 目录与文件结构升级

在原有 `.ai/skills/` 基础上，建议升级为：

```text
.ai/
├── rules/
├── skills/
│   ├── spec/
│   │   ├── spec-intake.md
│   │   ├── change-scope-mapper.md
│   │   ├── bdd-expander.md
│   │   └── acceptance-matrix-builder.md
│   ├── design/
│   │   ├── contract-auditor.md
│   │   ├── state-machine-checker.md
│   │   ├── migration-planner.md
│   │   └── architecture-impact-brief.md
│   ├── implementation/
│   │   ├── tdd-implementer.md
│   │   ├── ui-prototyper.md
│   │   ├── api-contract-coder.md
│   │   └── event-wiring-builder.md
│   ├── verify/
│   │   ├── spec-test-sync.md
│   │   ├── release-gate-checker.md
│   │   ├── runtime-verifier.md
│   │   └── security-boundary-reviewer.md
│   └── evolve/
│       ├── incident-to-rule.md
│       ├── diff-to-context.md
│       ├── skill-evolver.md
│       └── changelog-and-runbook.md
├── specs/
│   ├── product/
│   ├── change/
│   ├── contract/
│   ├── execution/
│   └── hotfix/
└── runbooks/
```

---

## 十五、最终蒸馏：新的工程化开发范式

可将整套方法收敛成一句话：

> **用 OpenSpec 把需求变成真理源，用 Superpowers 把 AI 变成守纪律的工程执行体，再用 Skill 集合把每个阶段固化成可重复、可审计、可演化的工程动作。**

对应到团队角色：

- **SE/架构师**：负责 OpenSpec、边界、协议、发布拓扑。
- **组长/平台工程师**：负责 Superpowers、CI/CD 门禁、Skill 资产化。
- **开发/审计员**：负责把业务翻译成 BDD，驱动 AI 产出，并做首层 Diff 审计。
- **运维/值班**：负责 Execution Spec、灰度、回滚、监控验证。

这样，原方案就从“AI 辅助开发规范”进一步升级为：

**一套可跨仓库协作、可绑定发布流程、可持续沉淀技能资产的 AI 工程化操作系统。**
