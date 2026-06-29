# CrownBio Server

## 1. 项目说明

本项目是一个基于 Spring Boot 3 + Maven 的多模块后端服务，根模块通过 `pom.xml` 聚合以下子模块：

- `crownBio-admin`：Web 入口，最终可执行 Jar 产物
- `crownBio-framework`：通用框架能力
- `crownBio-system`：系统管理能力
- `crownBio-quartz`：定时任务
- `crownBio-generator`：代码生成
- `crownBio-common`：公共组件
- `crownBio-report`：报告、OnlyOffice、S3、AI 相关业务

默认可执行产物为：

```text
crownBio-admin/target/crownBio-admin.jar
```

## 2. 环境要求

编译和部署前请先准备以下环境：

- JDK 17
- Maven 3.8+
- PostgreSQL
- Redis
- Docker（测试环境容器化或镜像打包时需要）
- Git
- Windows PowerShell 5.1+（使用 `deploy-ec2.ps1` 时需要）
- OpenSSH 客户端（使用 `ssh` / `scp` 时需要）
- AWS CLI（仅 `deploy.bat` 推送 ECR 时需要）

当前配置文件分层如下：

- `crownBio-admin/src/main/resources/application.yml`：公共配置，默认激活 `dev`
- `crownBio-admin/src/main/resources/application-dev.yml`：本地开发
- `crownBio-admin/src/main/resources/application-test.yml`：测试环境
- `crownBio-admin/src/main/resources/application-prod.yml`：生产环境

## 3. 编译与打包

### 3.1 标准打包命令

在项目根目录执行：
```bash
mvn clean package -DskipTests
```

### 3.2 本地启动

开发环境（如链接测试数据库和redis，需要手动修改对应地址application-dev.yml）：

```bash
java -jar crownBio-admin/target/crownBio-admin.jar --spring.profiles.active=dev
```

测试环境（EC2测试服务器）：

```bash
java -jar crownBio-admin/target/crownBio-admin.jar --spring.profiles.active=test
```

生产环境：

```bash
java -jar crownBio-admin/target/crownBio-admin.jar --spring.profiles.active=prod
```

默认端口来自公共配置 `application.yml`，未覆盖时服务监听 `8082`。如果希望在容器或服务器上监听 `8080`，请显式传入：

```bash
java -jar crownBio-admin/target/crownBio-admin.jar --server.port=8080 --spring.profiles.active=prod
```


## 5. 部署到 EC2 测试环境

### 5.1 说明

仓库内现有的 `deploy-ec2.ps1` 本质是“本地打包 Jar -> 通过 SSH/SCP 上传到 Linux 云主机 -> 执行远端更新脚本”的方式。虽然脚本名叫 `ec2`，但同样适用于阿里云 ECS、腾讯云 CVM 或任意可 SSH 登录的 Linux 服务器。

### 5.2 脚本方式部署

根目录脚本：

```text
deploy-ec2.ps1
```

脚本默认流程：

1. 测试 SSH 连通性
2. 执行 `git pull --ff-only`
3. 执行 `mvn clean package`
4. 上传 `crownBio-admin/target/crownBio-admin.jar`
5. 在远端执行 `updateBackend.sh`

示例命令：

```powershell
.\deploy-ec2.ps1
```

### 5.3 远端服务器需要准备的脚本

`deploy-ec2.ps1` 只负责上传 Jar 和触发远端脚本`updateBackend.sh`。


## 6. Docker 镜像打包与 ECR 推送

如果需要先打成镜像再交给容器平台部署，可使用根目录脚本：

```text
deploy.bat
```

该脚本当前会执行以下动作：

1. `git fetch` / `git checkout master` / `git pull origin master`
2. 执行 AWS SSO 登录
3. 将 `application.yml` 中的 `spring.profiles.active` 临时改为 `prod`
4. 执行 `mvn clean package -DskipTests`
5. 基于 `Dockerfile` 构建镜像
6. 打 tag 并推送到 AWS ECR

直接执行：

```bat
deploy.bat
```

指定镜像 tag：

```bat
deploy.bat 20260607
```

说明：

- 默认 ECR 仓库为 `838261167460.dkr.ecr.ap-east-1.amazonaws.com/crownbio/server`
- 脚本会切换到 `master` 分支，请确保当前改动已经提交或确认不会被流程影响
- `Dockerfile` 入口固定为 `prod` profile，运行容器时建议显式设置 `SERVER_PORT=8080`

如果希望使用更适合生产环境变量注入的镜像定义，建议优先使用：

```bash
docker build -f Dockerfile.prod --build-arg JAR_FILE=crownBio-admin/target/crownBio-admin.jar -t crownbio-backend:prod .
```

`Dockerfile.prod` 已预留如下环境变量注入方式：

- `SPRING_DATASOURCE_DRUID_MASTER_URL`
- `SPRING_DATASOURCE_DRUID_MASTER_USERNAME`
- `SPRING_DATASOURCE_DRUID_MASTER_PASSWORD`
- `SPRING_DATA_REDIS_CLUSTER_NODES`
- `FILE_STORAGE_TYPE`
- `S3_ENDPOINT`
- `S3_ACCESS_KEY`
- `S3_SECRET_KEY`
- `S3_BUCKET_NAME`
- `S3_REGION`
- `ONLYOFFICE_DOCUMENT_SERVER_URL`
- `ONLYOFFICE_JWT_SECRET`
- `ONLYOFFICE_CALLBACK_BASE_URL`
- `ONLYOFFICE_DOWNLOAD_BASE_URL`

## 7. 注意事项

- 不建议把数据库、Redis、S3、SSO 等敏感信息直接写入仓库，生产环境请优先使用环境变量或外部配置中心注入。
- `application.yml` 的默认 profile 是 `dev`，上线时请务必显式使用 `prod`。
- `Dockerfile` 暴露的是 `8080`，但应用默认端口来自配置文件，建议运行时显式传入 `SERVER_PORT` 或 `--server.port`。
- 如果走 OnlyOffice 场景，除了后端服务本身，还要保证文档服务、回调地址和下载地址互通。
