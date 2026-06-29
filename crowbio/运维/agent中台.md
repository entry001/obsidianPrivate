
> [!important] 本地敏感信息
> 真实密钥/密码/License 仅保存在 `crowbio/运维/.local-secrets.env`（已加入本地 Git 忽略）；本文只保留占位符，避免再次进入 Git 历史。

微软模型地址：
测试环境：
    OPENAI_API_KEY: Optional[str] = "<AZURE_OPENAI_TEST_KEY>"  # OpenAI API密钥

    OPENAI_BASE_URL: Optional[str] = "https://enterprisechatoai.cognitiveservices.azure.com/openai/responses?api-version=2025-04-01-preview"  # OpenAI/Azure OpenAI服务地址

    OPENAI_MODEL: Optional[str] = "gpt-5.4"  # 使用的模型名称（多模态需 API 支持 vision）

    OPENAI_API_VERSION: Optional[str] = "2025-04-01-preview"  # API版本（Azure OpenAI需要）

    OPENAI_MODEL_GROK: Optional[str] = "grok-4-1"  # Grok模型名称

生产环境：
https://ai-report-6002-resource.cognitiveservices.azure.com/openai/responses?api-version=2025-04-01-preview

<AZURE_OPENAI_PROD_KEY>

gpt-5.2-chat
gpt-5.4
