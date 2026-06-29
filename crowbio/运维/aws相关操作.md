

> [!important] 本地敏感信息
> 真实密钥/密码仅保存在 `crowbio/运维/.local-secrets.env`（已加入本地 Git 忽略）；本文只保留占位符，命令执行前请先在本机取值或导出环境变量。

## **登录命令：**
aws sso login --profile cb-ps-ai-it-rw-838261167460

## 登录服务器
ssh ec2
密码：<SERVER_PASSWORD>



## 访问ECS相关
**查看任务列表**
aws ecs list-tasks  --cluster vibrant-zebra-f7l8x2  --region ap-east-1

**查看任务参数**
aws ecs describe-services --cluster vibrant-zebra-f7l8x2 --services reportgen-cn-onlyoffice-service --region ap-east-1

**查看服务参数：**
aws ecs describe-services   --cluster vibrant-zebra-f7l8x2  --services aiagent-service-ysfvko7l --region ap-east-1

**更新服务参数允许执行命令**
aws ecs update-service --cluster vibrant-zebra-f7l8x2  --service reportgen-cn-onlyoffice-service  --enable-execute-command  --region ap-east-1

**强制部署服务**
aws ecs update-service --cluster vibrant-zebra-f7l8x2  --service reportgen-cn-onlyoffice-service  --force-new-deployment --region ap-east-1

**执行命令**：aws ecs execute-command  --cluster vibrant-zebra-f7l8x2  --task 50974d324f0b45f78c47589a35df56da  --container onlyoffice --interactive  --command "/bin/bash"
## **数据库：**
psql "host=$RDSHOST port=5432 dbname=postgres user=postgres sslmode=require password=<TEST_DB_PASSWORD>"

aws rds generate-db-auth-token --hostname reportgen-ape1.cluster-cp2i22gu6jas.ap-east-1.rds.amazonaws.comT --port 5432 --username postgres --region ap-east-1 


$env:PGPASSWORD="<TEST_DB_PASSWORD>"

**数据库地址**
use1:
endpoint: reportgen-rds-use1-instance-1.c2t26ukswjhd.us-east-1.rds.amazonaws.com
port: 5432
database name: reportgen_rds_use1
master username: postgres
master password: <USE1_DB_MASTER_PASSWORD>
 
ape1:
endpoint: reportgen-rds-ape1-instance-1.cp2i22gu6jas.ap-east-1.rds.amazonaws.com
port: 5432
database name: reportgen_rds_ape1
master username: postgres
master password: <APE1_DB_MASTER_PASSWORD>

### **导出数据库**
psql -h db.rkneihloapvlsqbubcjv.supabase.co -p 6543 -U postgres -d postgres -f ~/backup.sql
pg_dump -h db.rkneihloapvlsqbubcjv.supabase.co -p 6543 -U postgres -d postgres -f ~/backup.sql

public   sci_ai
### 数据库迁移
pg_dump -h db.rkneihloapvlsqbubcjv.supabase.co -p 6543 -U postgres -d postgres -F c|pg_restore  -U postgres -h reportgen-rds-ape1-instance-1.cp2i22gu6jas.ap-east-1.rds.amazonaws.com -p 5432 -d reportgen_rds_ape1 --clean --if-exists

**迁移指定scheme**
pg_dump -h db.rkneihloapvlsqbubcjv.supabase.co -p 6543 -U postgres -d postgres -F c -n public -n sci_ai|pg_restore  -U postgres -h reportgen-rds-ape1-instance-1.cp2i22gu6jas.ap-east-1.rds.amazonaws.com -p 5432 -d reportgen_rds_ape1 --clean --if-exists

**全量迁移**
pg_dump -h db.rkneihloapvlsqbubcjv.supabase.co -p 6543 -U postgres -d postgres -F c|pg_restore  -U postgres -h reportgen-rds-use1-instance-1.c2t26ukswjhd.us-east-1.rds.amazonaws.com -p 5432 -d reportgen_rds_use1 --clean --if-exists


### **aws数据库登录**
psql "host=reportgen-ape1.cluster-cp2i22gu6jas.ap-east-1.rds.amazonaws.com port=5432 dbname=postgres user=postgres sslmode=require"

## ECS部署问题

**aiagent**
网络：http://aiagent-8000-tcp.cloudmap-reportgen:8000   10.200.8.153

onlyoffice：
10.200.8.214


**applican参数：**
jdbc:postgresql://reportgen-rds-ape1-instance-1.cp2i22gu6jas.ap-east-1.rds.amazonaws.com:5432/reportgen_rds_ape1?sslmode=require

jdbc:postgresql://reportgen-rds-use1-instance-1.c2t26ukswjhd.us-east-1.rds.amazonaws.com:5432/reportgen_rds_use1?sslmode=require

## redis
**美国**
valkey-reportgen-use1-w3dixp.serverless.use1.cache.amazonaws.com:6379

[

{ "name": "OPENAI_API_KEY", "value": "<AZURE_OPENAI_TEST_KEY>" },

{ "name": "OPENAI_BASE_URL", "value": "https://enterprisechatoai.cognitiveservices.azure.com/openai/responses?api-version=2025-04-01-preview" },

{ "name": "OPENAI_MODEL", "value": "gpt-5.4" },

{ "name": "OPENAI_API_VERSION", "value": "2025-04-01-preview" },

{ "name": "OPENAI_MODEL_GROK", "value": "grok-4-1" }

]


