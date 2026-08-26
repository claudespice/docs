---
description: Client libraries for querying Spice.ai Cloud and a local Spice runtime
icon: code
---

# SDKs

Client SDKs connect to a Spice project over [Apache Arrow Flight](../cloud/api/sql-query/apache-arrow-flight-api.md) and return results as Apache Arrow record batches, which convert cheaply into the dataframe type of each language. They are the recommended way to query Spice from application code.

Every SDK connects to either **Spice.ai Cloud** or a **local Spice runtime**. Authentication against Cloud uses a project [API key](../cloud/portal/apps/api-keys.md).

| Language            | Package                         | Latest      | Documentation                       |
| ------------------- | ------------------------------- | ----------- | ----------------------------------- |
| Python              | `spicepy`                       | `v4.0.0`    | [Python SDK](python-sdk/)           |
| TypeScript, Node.js | `@spiceai/spice`                | `3.2.0`     | [Node.js SDK](node.js-sdk/)         |
| Go                  | `github.com/spiceai/gospice/v9` | `v9.0.0`    | [Go SDK](go.md)                     |
| Rust                | `spiceai`                       | `4.0.0`     | [Rust SDK](rust-sdk/)               |
| Java                | `ai.spice:spiceai`              | `0.8.0`     | [Java SDK](java-sdk.md)             |
| C#, .NET            | `SpiceAI`                       | `0.4.0`     | [.NET SDK](dotnet-sdk.md)           |

## Synchronous and asynchronous queries

Every SDK separates the two query paths by method name:

* **`sql()`** runs a query synchronously and streams Arrow record batches back on the open connection. This is the default path for querying.
* **`query()`** submits the query for asynchronous execution and returns a job handle used to poll status and fetch results once the query completes. Asynchronous queries require the runtime to be running in distributed (scheduler) mode.

| Language            | Synchronous              | Asynchronous                     |
| ------------------- | ------------------------ | -------------------------------- |
| Python              | `sql`, `sql_with_params` | `query`, `query_with_params`     |
| TypeScript, Node.js | `sql`                    | `query`, `queryWithParams`       |
| Go                  | `Sql`, `SqlWithParams`   | `Query`, `QueryWithParams`       |
| Rust                | `sql`, `sql_with_params` | `query`, `query_with_bindings`   |
| Java                | `sql`, `sqlWithParams`   | `query`, `queryWithParams`       |
| C#, .NET            | `SqlAsync`, `SqlWithParamsAsync` | `QueryAsync`, `QueryWithParamsAsync` |

{% hint style="warning" %}
In the versions listed above, `query()` submits an asynchronous job. Earlier releases of the Python, Rust, Java, Node.js, and Go SDKs used `query()` for the synchronous path — code calling it compiles unchanged but returns a job handle instead of results. Call `sql()` for the synchronous behavior.
{% endhint %}

## Default endpoints

Each SDK ships defaults for both Cloud and a local runtime. The exact values and how to override them are on each SDK's page, but the hosts are common:

| Target        | Arrow Flight                   | HTTP                      |
| ------------- | ------------------------------ | ------------------------- |
| Spice.ai Cloud | `flight.spiceai.io` port `443` | `https://data.spiceai.io` |
| Local runtime  | `localhost` port `50051`       | `http://localhost:8090`   |

{% hint style="warning" %}
Most SDKs default to the **local** runtime for Flight. Connecting to Spice.ai Cloud generally requires setting the Cloud endpoint explicitly, or calling the SDK's Cloud helper — supplying an API key alone is not always enough. Each page states what its own defaults are.
{% endhint %}

## Choosing between an SDK and the APIs directly

An SDK is preferable for application code: it handles the Flight handshake, authentication headers, and retries. Query directly against the [HTTP API](../cloud/api/sql-query/http-api.md) or [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) when a language has no SDK, or for one-off requests from a shell.
