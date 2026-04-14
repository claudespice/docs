# Table of contents

## Getting Started

* [Welcome to Spice.ai Cloud](README.md)
* [Getting Started](getting-started/get-started/README.md)
  * [Sign in with GitHub](getting-started/get-started/portal-login.md)
  * [Create a Spice app](getting-started/getting-started/portal-login-1.md)
  * [Add a Dataset and query data](getting-started/get-started/step-2-add-dataset-and-query-data.md)
  * [Add AI Model and chat with your data](getting-started/get-started/step-3-add-ai-model-and-chat-with-your-app.md)
  * [Next Steps](getting-started/get-started/next-steps.md)
* [FAQ](getting-started/faq.md)

## Features

* [Federated SQL Query](features/federated-sql-query.md)
* [Data Acceleration](features/data-acceleration/README.md)
  * [In-Memory Arrow Data Accelerator](features/data-acceleration/in-memory-arrow-data-accelerator.md)
  * [DuckDB Data Accelerator](features/data-acceleration/duckdb-data-accelerator.md)
  * [PostgreSQL Data Accelerator](features/data-acceleration/postgresql-data-accelerator.md)
  * [SQLite Data Accelerator](features/data-acceleration/sqlite-data-accelerator.md)
* [Search & Retrieval](features/search-and-retrieval.md)
* [AI Gateway](features/ai-gateway.md)
* [Semantic Models](features/semantic-models.md)
* [ML Models](building-blocks/spice-models.md)
* [Observability](features/observability/README.md)
  * [Task History](features/observability/task-history.md)
  * [Zipkin](features/observability/zipkin.md)

## Building Blocks

* [Data Connectors](building-blocks/data-connectors/README.md)
  * [ABFS](building-blocks/data-connectors/abfs.md)
  * [ClickHouse](building-blocks/data-connectors/clickhouse.md)
  * [Databricks](building-blocks/data-connectors/databricks.md)
  * [Debezium](building-blocks/data-connectors/debezium.md)
  * [Delta Lake](building-blocks/data-connectors/delta-lake.md)
  * [Dremio](building-blocks/data-connectors/dremio.md)
  * [DuckDB](building-blocks/data-connectors/duckdb.md)
  * [DynamoDB](building-blocks/data-connectors/dynamodb.md)
  * [FlightSQL](building-blocks/data-connectors/flightsql.md)
  * [FTP](building-blocks/data-connectors/ftp.md)
  * [GitHub](building-blocks/data-connectors/github.md)
  * [GraphQL](building-blocks/data-connectors/graphql.md)
  * [HTTPS](building-blocks/data-connectors/https.md)
  * [LocalPod](building-blocks/data-connectors/localpod.md)
  * [Memory](building-blocks/data-connectors/memory.md)
  * [MSSQL](building-blocks/data-connectors/mssql.md)
  * [MySQL](building-blocks/data-connectors/mysql.md)
  * [ODBC](building-blocks/data-connectors/odbc.md)
  * [Postgres](building-blocks/data-connectors/postgres.md)
  * [S3](building-blocks/data-connectors/s3.md)
  * [SharePoint](building-blocks/data-connectors/sharepoint.md)
  * [Snowflake](building-blocks/data-connectors/snowflake.md)
  * [Spark](building-blocks/data-connectors/spark.md)
  * [SpiceAI](building-blocks/data-connectors/spiceai.md)
* [Model Providers](building-blocks/model-providers/README.md)
  * [Anthropic](building-blocks/model-providers/anthropic.md)
  * [Azure](building-blocks/model-providers/azure.md)
  * [Hugging Face](building-blocks/model-providers/huggingface.md)
  * [OpenAI](building-blocks/model-providers/openai.md)
  * [Perplexity](building-blocks/model-providers/perplexity.md)
  * [SpiceAI](building-blocks/model-providers/spiceai.md)
  * [XAI](building-blocks/model-providers/xai.md)

## API

* [SQL Query API](api/sql-query/README.md)
  * [HTTP API](api/sql-query/http-api.md)
  * [Apache Arrow Flight API](api/sql-query/apache-arrow-flight-api.md)
* [Search API](api/search.md)
* [LLM API](api/openai-api.md)
* [Management API](api/management/README.md)
  * [Health](api/management/health.md)
  * [Regions](api/management/regions.md)
  * [Apps](api/management/apps.md)
  * [Deployments](api/management/deployments.md)
  * [Secrets](api/management/secrets.md)
  * [API Keys](api/management/api-keys.md)
  * [Members](api/management/members.md)
  * [Container Images](api/management/container-images.md)
  * [Terraform Provider](api/management/terraform.md)
* [Metrics API](api/metrics.md)
* [Health API](api/health.md)

## Portal

* [Playground](portal/playground/README.md)
  * [SQL Query](portal/playground/sql-query-editor.md)
  * [NSQL Query](portal/playground/nsql-query.md)
  * [AI Chat](portal/playground/ai-chat.md)
  * [Search](portal/playground/search.md)
* [Organizations](portal/organizations.md)
* [OAuth Clients](portal/oauth-clients.md)
* [Apps](portal/apps/README.md)
  * [API keys](portal/apps/api-keys.md)
  * [Secrets](portal/apps/secrets.md)
  * [Tags](portal/apps/tags.md)
  * [Publish](portal/apps/publish.md)
  * [Connect GitHub](portal/apps/connect-github.md)
  * [Transfer](portal/apps/transfer.md)
  * [Delete](portal/apps/delete.md)
  * [Runtime](portal/apps/runtime.md)
* [Public Apps](portal/public-apps.md)
* [App Spicepod](portal/app-spicepod/README.md)
  * [Spicepod Configuration](portal/app-spicepod/spicepod-configuration.md)
  * [Deployments](portal/app-spicepod/deployments.md)
  * [Spice Runtime Versions](portal/app-spicepod/spice-runtime-versions.md)
* [Datasets](portal/datasets-and-views.md)
* [Models](portal/models.md)
* [Monitoring](portal/monitoring-and-request-logs.md)
* [Observability](portal/observability.md)
* [Profile](portal/profile/README.md)
  * [Personal Access Tokens](portal/profile/personal-access-tokens.md)
* [External Data Sources](portal/external-data-sources.md)

## Use-Cases

* [Agentic AI Apps](use-cases/agentic-ai-apps.md)
* [Database CDN](use-cases/database-cdn.md)
* [Data Lakehouse](use-cases/data-lakehouse.md)
* [Enterprise Search](use-cases/enterprise-search.md)
* [Enterprise RAG](use-cases/enterprise-rag.md)

## SDKs

* [Python SDK](sdks/python-sdk/README.md)
  * [Streaming](sdks/python-sdk/streaming.md)
* [Node.js SDK](sdks/node.js-sdk/README.md)
  * [Streaming](sdks/node.js-sdk/streaming.md)
  * [API Reference](sdks/node.js-sdk/api-reference.md)
* [Go SDK](sdks/go.md)
* [Rust SDK](sdks/rust-sdk/README.md)
* [Dotnet SDK](sdks/dotnet-sdk.md)
* [Java SDK](sdks/java-sdk.md)

## Monitoring

* [Monitoring](monitoring/README.md)
  * [Portal](monitoring/portal.md)
  * [Grafana & Prometheus](monitoring/grafana.md)
  * [Datadog](monitoring/datadog.md)
  * [Zipkin](monitoring/zipkin.md)

## Integrations

* [GitHub Copilot](integrations/github-copilot.md)
* [Grafana](integrations/grafana.md)
* [Databricks](integrations/databricks.md)

## REFERENCE

* [Core Concepts](reference/core-concepts/README.md)
  * [Duration Literals](reference/core-concepts/duration-literals.md)
* [SQL Reference](reference/sql-reference.md)

## Changelog

* [Changelog](changelog/README.md)

## Pricing

* [Paid Plans](pricing/plans.md)
* [Community Plan](pricing/community.md)

## Support

* [Support](support/support.md)

## Security

* [Security at Spice AI](security/security.md)
* [Report a vulnerability](security/report.md)

## Legal

* [Privacy Policy](legal/privacy.md)
* [Website Terms of Use](legal/terms.md)
* [Terms of Service](legal/terms-of-service.md)
* [End User License Agreement](legal/eula.md)
