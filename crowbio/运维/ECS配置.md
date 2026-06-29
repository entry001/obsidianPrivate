> [!important] 本地敏感信息
> 真实密钥/密码仅保存在 `crowbio/运维/.local-secrets.env`（已加入本地 Git 忽略）；本文中的敏感字段统一使用占位符。

## aiagent
```

{
    "taskDefinitionArn": "arn:aws:ecs:ap-east-1:838261167460:task-definition/aiagent:7",
    "containerDefinitions": [
        {
            "name": "aiagent",
            "image": "838261167460.dkr.ecr.ap-east-1.amazonaws.com/crowbio/aiagent@sha256:2cd3a964f8dadec02a9509809a4044a2b94b37fce648438ab440cede62b04c09",
            "cpu": 0,
            "portMappings": [
                {
                    "containerPort": 8000,
                    "hostPort": 8000,
                    "protocol": "tcp",
                    "name": "aiagent-8000-tcp",
                    "appProtocol": "http"
                }
            ],
            "essential": true,
            "environment": [
                {
                    "name": "AWS_DEFAULT_REGION",
                    "value": "us-east-1"
                },
                {
                    "name": "BACKEND_BASE_URL",
                    "value": "http://backend.reportgen:8080/ai/tool"
                },
                {
                    "name": "OPENAI_API_VERSION",
                    "value": "2025-04-01-preview"
                },
                {
                    "name": "OPENAI_API_KEY",
                    "value": "<AZURE_OPENAI_TEST_KEY>"
                },
                {
                    "name": "OPENAI_MODEL",
                    "value": "gpt-5.4"
                },
                {
                    "name": "OPENAI_BASE_URL",
                    "value": "https://enterprisechatoai.cognitiveservices.azure.com/openai/responses?api-version=2025-04-01-preview"
                }
            ],
            "environmentFiles": [],
            "mountPoints": [],
            "volumesFrom": [],
            "ulimits": [],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/aiagent",
                    "awslogs-create-group": "true",
                    "awslogs-region": "ap-east-1",
                    "awslogs-stream-prefix": "ecs"
                },
                "secretOptions": []
            },
            "systemControls": []
        }
    ],
    "family": "aiagent",
    "taskRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "executionRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "networkMode": "awsvpc",
    "revision": 7,
    "volumes": [],
    "status": "ACTIVE",
    "requiresAttributes": [
        {
            "name": "com.amazonaws.ecs.capability.logging-driver.awslogs"
        },
        {
            "name": "ecs.capability.execution-role-awslogs"
        },
        {
            "name": "com.amazonaws.ecs.capability.ecr-auth"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.19"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.21"
        },
        {
            "name": "com.amazonaws.ecs.capability.task-iam-role"
        },
        {
            "name": "ecs.capability.execution-role-ecr-pull"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.18"
        },
        {
            "name": "ecs.capability.task-eni"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.29"
        }
    ],
    "placementConstraints": [],
    "compatibilities": [
        "EC2",
        "FARGATE",
        "MANAGED_INSTANCES"
    ],
    "runtimePlatform": {
        "cpuArchitecture": "X86_64",
        "operatingSystemFamily": "LINUX"
    },
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "1024",
    "memory": "3072",
    "registeredAt": "2026-06-02T14:41:35.942Z",
    "registeredBy": "arn:aws:sts::838261167460:assumed-role/AWSReservedSSO_cb-ps-ai-it-rw_66fc81985f275f27/microsoft-pilot3@crownbio.com",
    "enableFaultInjection": false,
    "tags": []
}
```

## ONLYOFFICE
```

{
    "taskDefinitionArn": "arn:aws:ecs:ap-east-1:838261167460:task-definition/onlyoffice-documentserver:6",
    "containerDefinitions": [
        {
            "name": "onlyoffice",
            "image": "838261167460.dkr.ecr.ap-east-1.amazonaws.com/crowbio/onlyoffice@sha256:4c849012f91fcfc65c93e3d1ac94afe00085a51b048a5a567541a24b1313fb61",
            "cpu": 0,
            "portMappings": [
                {
                    "containerPort": 80,
                    "hostPort": 80,
                    "protocol": "tcp",
                    "name": "onlyoffice-80-tcp"
                }
            ],
            "essential": true,
            "environment": [
                {
                    "name": "JWT_ENABLED",
                    "value": "false"
                },
                {
                    "name": "JWT_SECRET",
                    "value": "<ONLYOFFICE_JWT_SECRET>"
                }
            ],
            "environmentFiles": [],
            "mountPoints": [],
            "volumesFrom": [],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/onlyoffice-documentserver",
                    "awslogs-create-group": "true",
                    "awslogs-region": "ap-east-1",
                    "awslogs-stream-prefix": "ecs"
                },
                "secretOptions": []
            },
            "systemControls": []
        }
    ],
    "family": "onlyoffice-documentserver",
    "taskRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "executionRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "networkMode": "awsvpc",
    "revision": 6,
    "volumes": [],
    "status": "ACTIVE",
    "requiresAttributes": [
        {
            "name": "com.amazonaws.ecs.capability.logging-driver.awslogs"
        },
        {
            "name": "ecs.capability.execution-role-awslogs"
        },
        {
            "name": "com.amazonaws.ecs.capability.ecr-auth"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.19"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.21"
        },
        {
            "name": "com.amazonaws.ecs.capability.task-iam-role"
        },
        {
            "name": "ecs.capability.execution-role-ecr-pull"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.18"
        },
        {
            "name": "ecs.capability.task-eni"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.29"
        }
    ],
    "placementConstraints": [],
    "compatibilities": [
        "EC2",
        "MANAGED_INSTANCES",
        "FARGATE"
    ],
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "1024",
    "memory": "3072",
    "registeredAt": "2026-05-31T13:49:07.111Z",
    "registeredBy": "arn:aws:sts::838261167460:assumed-role/AWSReservedSSO_cb-ps-ai-it-rw_66fc81985f275f27/microsoft-pilot3@crownbio.com",
    "tags": []
}
```

## reportgen-backend
```
{
    "taskDefinitionArn": "arn:aws:ecs:ap-east-1:838261167460:task-definition/reportgen-backend:14",
    "containerDefinitions": [
        {
            "name": "app",
            "image": "838261167460.dkr.ecr.ap-east-1.amazonaws.com/crownbio/server@sha256:d3e0a24c4ba2b81cfc94291a9a022a6a248ed59db0f89be47d20bfd75d8f5962",
            "cpu": 0,
            "portMappings": [
                {
                    "containerPort": 8080,
                    "hostPort": 8080,
                    "protocol": "tcp",
                    "name": "app-8080-tcp"
                }
            ],
            "essential": true,
            "environment": [
                {
                    "name": "S3_REGION",
                    "value": "ap-east-1"
                },
                {
                    "name": "AI_CHAT_BASE_URL",
                    "value": "http://agent.reportgen:8000/api/v1/chat"
                },
                {
                    "name": "SPRING_PROFILES_ACTIVE",
                    "value": "prod"
                },
                {
                    "name": "ONLYOFFICE_DOCUMENT-SERVER-URL",
                    "value": "https://reportgen-cn.crownbio.com/onlyoffice/"
                },
                {
                    "name": "JAVA_OPTS",
                    "value": "-Xms512m -Xmx1024m"
                },
                {
                    "name": "SPRING_DATASOURCE_DRUID_MASTER_PASSWORD",
                    "value": "<APE1_DB_MASTER_PASSWORD>"
                },
                {
                    "name": "FILE_STORAGE_BASE_PATH",
                    "value": "/data/uploadPath/chart-files"
                },
                {
                    "name": "AI_CHAT_TIMEOUT",
                    "value": "60000"
                },
                {
                    "name": "SERVER_PORT",
                    "value": "8080"
                },
                {
                    "name": "S3_VERIFY_CONFIG",
                    "value": "false"
                },
                {
                    "name": "SSO_REDIRECT-URI",
                    "value": "https://reportgen-cn.crownbio.com/api/sso/callback"
                },
                {
                    "name": "LOGGING_LEVEL_COM_CROWNBIO",
                    "value": "info"
                },
                {
                    "name": "ONLYOFFICE_DOWNLOAD-BASE-URL",
                    "value": "https://reportgen-cn.crownbio.com/api/files/onlyoffice/download"
                },
                {
                    "name": "S3_ACCESS_KEY",
                    "value": ""
                },
                {
                    "name": "S3_BUCKET_NAME",
                    "value": "reporthk"
                },
                {
                    "name": "S3_ENDPOINT",
                    "value": "https://s3.ap-east-1.amazonaws.com/"
                },
                {
                    "name": "SSO_ENABLED",
                    "value": "true"
                },
                {
                    "name": "SPRING_DATA_REDIS_CLUSTER_NODES",
                    "value": "valkey-reportgen-ape1-7okdyr.serverless.ape1.cache.amazonaws.com:6379"
                },
                {
                    "name": "FILE_STORAGE_TYPE",
                    "value": "local"
                },
                {
                    "name": "ONLYOFFICE_CALLBACK-BASE-URL",
                    "value": "https://reportgen-cn.crownbio.com/api"
                },
                {
                    "name": "SPRING_DATASOURCE_DRUID_MASTER_USERNAME",
                    "value": "postgres"
                },
                {
                    "name": "S3_SECRET_KEY",
                    "value": ""
                },
                {
                    "name": "RUOYI_PROFILE",
                    "value": "/data/uploadPath"
                },
                {
                    "name": "SPRING_DATASOURCE_DRUID_MASTER_URL",
                    "value": "jdbc:postgresql://reportgen-rds-ape1-instance-1.cp2i22gu6jas.ap-east-1.rds.amazonaws.com:5432/reportgen_rds_ape1?sslmode=require"
                },
                {
                    "name": "SSO_HOST-URL",
                    "value": "https://reportgen-cn.crownbio.com"
                }
            ],
            "mountPoints": [
                {
                    "sourceVolume": "uploads",
                    "containerPath": "/data/uploadPath"
                }
            ],
            "volumesFrom": [],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/reportgen-backend",
                    "awslogs-create-group": "true",
                    "awslogs-region": "ap-east-1",
                    "awslogs-stream-prefix": "ecs"
                },
                "secretOptions": []
            },
            "systemControls": []
        }
    ],
    "family": "reportgen-backend",
    "taskRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "executionRoleArn": "arn:aws:iam::838261167460:role/ecsTaskExecutionRole",
    "networkMode": "awsvpc",
    "revision": 14,
    "volumes": [
        {
            "name": "uploads",
            "host": {}
        }
    ],
    "status": "ACTIVE",
    "requiresAttributes": [
        {
            "name": "com.amazonaws.ecs.capability.logging-driver.awslogs"
        },
        {
            "name": "ecs.capability.execution-role-awslogs"
        },
        {
            "name": "com.amazonaws.ecs.capability.ecr-auth"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.19"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.21"
        },
        {
            "name": "com.amazonaws.ecs.capability.task-iam-role"
        },
        {
            "name": "ecs.capability.execution-role-ecr-pull"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.18"
        },
        {
            "name": "ecs.capability.task-eni"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.29"
        }
    ],
    "placementConstraints": [],
    "compatibilities": [
        "EC2",
        "FARGATE",
        "MANAGED_INSTANCES"
    ],
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "1024",
    "memory": "3072",
    "registeredAt": "2026-06-02T12:07:54.562Z",
    "registeredBy": "arn:aws:sts::838261167460:assumed-role/AWSReservedSSO_cb-ps-ai-it-rw_66fc81985f275f27/microsoft-pilot3@crownbio.com",
    "tags": []
}
```

