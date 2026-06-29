# Multi-Agent Chat API

基于 FastAPI 与 Strands SDK 的多 Agent 对话系统，面向 CrownBio 报告生成场景。支持 SSE（Server-Sent Events）流式通信，通过 Orchestrator 调度项目列表、参数收集、报告生成、语义编辑等专业子 Agent，并与业务后端（AI 中台）对接。

## 项目架构

```
app/
├── main.py                          # FastAPI 应用入口
├── config.py                        # 配置管理（LLM、后端 API、AWS 等）
├── models/                          # 请求/响应数据模型
├── routes/
│   └── chat.py                      # 聊天 SSE / 同步接口
├── services/
│   ├── orchestrator_agent.py        # 主 Agent：路由 + 子 Agent 调度
│   ├── llm_service.py               # LLM 封装（OpenAI / Ollama / Strands）
│   ├── session_manager.py           # 会话状态管理
│   ├── backend_session_context.py   # 业务后端会话上下文
│   ├── bedrock_short_term_memory.py # Bedrock 短期记忆
│   └── agents/                      # 专业子 Agent
│       ├── project_list_agent.py
│       ├── parameter_collector_agent.py
│       ├── report_generator_agent.py
│       ├── semantic_converter_agent.py
│       ├── report_editor_agent.py
│       ├── file_recognizer_agent.py
│       └── animal_data_agent.py
└── utils/                           # SSE、语言偏好、提示词辅助等
```

## 核心特性

- **Orchestrator 路由**：确定性关键词路由 + LLM 工具调用回退
- **SSE 流式通信**：实时推送路由事件与生成内容
- **业务后端对接**：统一 `BACKEND_BASE_URL` 调用 AI 中台接口
- **多模型支持**：Azure OpenAI（默认）、Ollama 本地模型
- **AWS 集成**：Bedrock 短期记忆、S3 附件读取

---

## 本地开发

### 环境要求

| 项目 | 要求 |
|------|------|
| Python | 3.11+（推荐 3.11，与 Docker 镜像一致） |
| 操作系统 | Windows / Linux / macOS |
| 业务后端 | 本地或远程 AI 中台服务（默认 `127.0.0.1:8080`） |
| AWS（可选） | 使用 Bedrock 记忆或 S3 附件时需 `aws sso login` |

### 1. 创建虚拟环境并安装依赖

**Windows（PowerShell）**

```powershell
cd <项目根目录>
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

**Linux / macOS**

```bash
cd <项目根目录>
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 2. 配置环境变量

在项目根目录创建 `.env` 文件（Pydantic Settings 会自动加载）。常用配置如下：

```env
# ---------- 服务 ----------
DEBUG=True
HOST=0.0.0.0
PORT=8000

# ---------- 业务后端（本地开发） ----------
# 完整 URL = 协议 + 主机 + 端口 + /ai/tool
BACKEND_BASE_URL=http://127.0.0.1:8080/ai/tool
BACKEND_API_KEY=<与中台约定一致的 API Key>

# ---------- LLM（默认 Azure OpenAI） ----------
USE_OPENAI=True
OPENAI_API_KEY=<your-key>
OPENAI_BASE_URL=https://enterprisechatoai.cognitiveservices.azure.com/openai/responses?api-version=2025-04-01-preview
OPENAI_MODEL=gpt-5.4

# ---------- 本地 Ollama（可选） ----------
USE_OLLAMA=False
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3:8b

# ---------- AWS（Bedrock 记忆 / S3 附件，可选） ----------
AWS_DEFAULT_REGION=us-east-1
S3_BUCKET_NAME=reportdoc
USE_BEDROCK_SHORT_TERM_MEMORY=True
```

> **优先级**：环境变量 / `.env` > `app/config.py` 中的默认值。生产环境请通过环境变量注入敏感信息，勿将密钥提交到代码库。

### 3. 配置业务后端通信地址

所有子 Agent 通过 `settings.BACKEND_BASE_URL` 拼接各接口路径（详见 [docs/BACKEND_API.md](docs/BACKEND_API.md)）。不同环境的典型配置：

| 环境 | `BACKEND_BASE_URL` | 说明 |
|------|-------------------|------|
| 本地开发 | `http://127.0.0.1:8080/ai/tool` | 本机启动 AI 中台后端 |
| EC2 测试/生产 | `http://127.0.0.1:8080/ai/tool` 或内网地址 | 与后端同机部署时用 loopback；跨机则用内网 IP/域名 |
| ECS 生产（多容器 Task） | `http://backend.reportgen:8080/ai/tool` | 同一 ECS Task 内通过容器服务名通信 |

请求头统一携带 `X-API-Key: <BACKEND_API_KEY>`。

**修改方式（任选其一）**

1. `.env` 中设置 `BACKEND_BASE_URL` 和 `BACKEND_API_KEY`（推荐，本地开发首选）
2. 启动前导出环境变量：`export BACKEND_BASE_URL=http://...`
3. 直接修改 `app/config.py` 默认值（部署脚本会按需改写，见下文）

### 4. 启动服务

**方式 A：启动脚本（推荐）**

```bash
# 前台启动（开发调试）
python run.py

# Linux 后台启动
chmod +x start.sh stop.sh
./start.sh          # 启动，PID 写入 run.pid，日志 logs/app.log
./start.sh restart  # 重启
./stop.sh           # 停止占用 8000 端口的进程
```

`run.py` 行为：
- `DEBUG=True` 时启用热重载（Windows 单进程，Linux/Mac 单进程）
- `DEBUG=False` 时 Linux/Mac 使用 4 worker 多进程

**方式 B：uvicorn 直接启动**

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### 5. 验证

- 健康检查：`GET http://localhost:8000/health`
- API 文档：`http://localhost:8000/docs`
- 根路由：`GET http://localhost:8000/`

> 服务绑定 `0.0.0.0` 表示监听所有网卡；浏览器请使用 `localhost` 或 `127.0.0.1` 访问。

### 6. 运行测试（可选）

```bash
pytest tests/ -q
```

---

## 部署

### 概览

| 目标环境 | 方式 | 脚本 | 说明 |
|----------|------|------|------|
| EC2 测试 | SSH 打包上传 | `scripts/deploy_ec2.ps1` | 跳板机 SSH，快速迭代 |
| ECS 生产 | Docker 镜像推送 ECR | `deploy.bat` | 构建镜像并推送到 AWS ECR |

---

### EC2 部署（测试 / 生产）

通过 PowerShell 脚本将代码打包为 tar.gz，经 SSH 上传到 EC2，解压后执行 `start.sh` 后台启动。

#### 前置条件

1. 本地已安装 `ssh`、`scp`、`tar`（Windows 10+ 自带 OpenSSH 客户端）
2. `~/.ssh/config` 中配置好跳板机与目标主机，例如：

   ```
   Host ec2-test
       HostName <目标内网 IP>
       User ec2-user
       ProxyJump <跳板机 Host>

   Host ec2
       HostName <生产内网 IP>
       User ec2-user
       ProxyJump <跳板机 Host>
   ```

3. 目标 EC2 已创建 Python 虚拟环境 `.venv` 并安装依赖
4. 目标机业务后端已就绪，`BACKEND_BASE_URL` 指向正确地址（可在远程 `.env` 或 `app/config.py` 中配置）

#### 部署命令

```powershell
# 测试环境（默认 Host: ec2-test，远程目录: /home/ec2-user/agent）
powershell -ExecutionPolicy Bypass -File scripts/deploy_ec2.ps1
```

#### 脚本执行流程

1. 测试 SSH 连通性（经跳板机）
2. 打包项目（排除 `.git`、`.venv`、`.env`、`logs` 等）
3. `scp` 上传到目标机 `/tmp/`
4. 远程解压到 `/home/ec2-user/agent`
5. 强制 `app/config.py` 中 `PORT=8000`
6. 执行 `stop.sh` → `start.sh` 重启服务

#### 远程运维

```bash
ssh ec2-test                              # 或 ssh ec2
cd /home/ec2-user/agent
tail -f logs/app.log                      # 查看日志
bash ./stop.sh && bash ./start.sh         # 手动重启
```

---

### ECS 生产部署

通过 `deploy.bat` 构建 Docker 镜像并推送到 AWS ECR（`ap-east-1`），再由 ECS 拉取镜像更新服务。

#### 前置条件

1. 本地已安装 Docker Desktop 且可正常构建
2. 已配置 AWS CLI 与 SSO Profile（脚本默认 `cb-ps-ai-it-rw-838261167460`）
3. ECR 仓库 `crowbio/aiagent` 已创建
4. ECS Task Definition 中已配置环境变量（见下方）

#### 部署命令

```bat
REM 使用 latest 标签
deploy.bat

REM 指定版本标签
deploy.bat 1.0.0
```

#### deploy.bat 执行流程

| 步骤 | 操作 |
|------|------|
| 1/7 | 将 `app/config.py` 中 `PORT` 改写为 `8000` |
| 2/7 | `aws sso login`（使用配置的 AWS Profile） |
| 3/7 | Docker 登录 ECR（当前默认注释，需手动取消注释或单独执行） |
| 4/7 | 将 `BACKEND_BASE_URL` 改写为 ECS 内网地址（当前默认注释） |
| 5/7 | `docker build -t crowbio/aiagent:<tag> .` |
| 6/7 | 打标签到 ECR 完整地址 |
| 7/7 | `docker push` 到 ECR |

脚本顶部可修改的关键变量：

```bat
set "AWS_PROFILE=cb-ps-ai-it-rw-838261167460"
set "AWS_REGION=ap-east-1"
set "ECR_REGISTRY=838261167460.dkr.ecr.ap-east-1.amazonaws.com"
set "ECR_REPO=crowbio/aiagent"
set "IMAGE_TAG=latest"
set "BACKEND_BASE_URL_ECS=http://backend.reportgen:8080/ai/tool"
```

#### ECS Task 环境变量建议

在 ECS Task Definition 中为 Agent 容器注入（覆盖镜像内默认值）：

```env
DEBUG=false
BACKEND_BASE_URL=http://backend.reportgen:8080/ai/tool
BACKEND_API_KEY=<生产 API Key>
OPENAI_API_KEY=<生产 Key>
OPENAI_BASE_URL=<生产 Azure OpenAI 地址>
OPENAI_MODEL=gpt-5.4
AWS_DEFAULT_REGION=us-east-1
S3_BUCKET_NAME=reportdoc
USE_BEDROCK_SHORT_TERM_MEMORY=true
```

> ECS 多容器 Task 中，Agent 与后端容器通过 **服务发现名称**（如 `backend.reportgen`）通信，无需经过公网。若启用 `deploy.bat` 第 4 步，会将此地址写入 `app/config.py` 默认值；更推荐在 ECS Task Definition 中通过环境变量注入，避免修改源码。

#### 手动 ECR 登录（deploy.bat 第 3 步被注释时）

```bat
aws ecr get-login-password --region ap-east-1 --profile cb-ps-ai-it-rw-838261167460 ^
  | docker login --username AWS --password-stdin 838261167460.dkr.ecr.ap-east-1.amazonaws.com
```

#### Docker 本地构建与测试

网络受限时可参考 [DOCKER_BUILD.md](DOCKER_BUILD.md) 使用国内镜像源，或使用 `Dockerfile.cn`：

```bash
docker build -f Dockerfile.cn -t crowbio/aiagent:latest .
docker run -p 8000:8000 -e DEBUG=false -e BACKEND_BASE_URL=http://host.docker.internal:8080/ai/tool crowbio/aiagent:latest
```

#### ECS 更新服务

镜像推送完成后，在 AWS 控制台或通过 CLI 触发 ECS Service 滚动更新，使用新镜像标签。健康检查端点为 `GET /health`（Dockerfile 已配置 `HEALTHCHECK`）。

---

## API 端点

### SSE 流式聊天

```http
POST /api/v1/chat/sse
Content-Type: application/json

{
  "message": "绑定项目 E0164-U2303",
  "session_id": "session_123456",
  "user_id": "user_001"
}
```

### 同步聊天

```http
POST /api/v1/chat/sync
Content-Type: application/json

{
  "message": "查询当前项目参数",
  "session_id": "session_123456"
}
```

### 其他

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/v1/chat/agents` | 可用 Agent 列表 |
| GET | `/health` | 健康检查 |
| GET | `/docs` | Swagger UI |

---

## 相关文档

| 文档 | 内容 |
|------|------|
| [docs/BACKEND_API.md](docs/BACKEND_API.md) | Agent 调用的业务后端接口规范 |
| [docs/接口对接文档.md](docs/接口对接文档.md) | 中台接口对接说明 |
| [STRANDS_INTEGRATION.md](STRANDS_INTEGRATION.md) | Strands SDK 集成说明 |
| [DOCKER_BUILD.md](DOCKER_BUILD.md) | Docker 构建与网络问题排查 |

---

## 技术栈

- **FastAPI** + **Uvicorn**：Web 框架与 ASGI 服务器
- **Strands SDK**：Agent 与 LLM 统一调用
- **Pydantic Settings**：配置管理
- **boto3**：AWS S3 / Bedrock
- **SSE**：Server-Sent Events 流式通信

## 许可证

MIT License
