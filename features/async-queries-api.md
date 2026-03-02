---
description: Asynchronous SQL query execution API for long-running queries
icon: clock-rotate-left
---

# Async Queries API

The async queries API provides asynchronous SQL query execution for long-running queries in cluster mode. Queries are submitted, assigned a unique job ID, and executed in the background. Clients poll for status and retrieve paginated results when ready.

{% hint style="warning" %}
This feature is experimental and requires cluster mode with the scheduler role. See [Distributed Query](distributed-query.md) for cluster setup.
{% endhint %}

## Features

* **Asynchronous execution**: Submit queries and retrieve results without blocking
* **Chunked results**: Large result sets are automatically split into chunks (default 10,000 rows each)
* **Pagination**: Iterate through result chunks via `next_chunk_url` links
* **Cancellation**: Cancel running queries at any time
* **Timeouts**: Set per-query execution timeouts
* **Size limits**: Set maximum result size per query
* **Result TTL**: Results are available for 12 hours after completion
* **Parameterized queries**: Bind variables using `$1`, `$2`, etc.

## Prerequisites

* Spice runtime running in cluster mode with the scheduler role (`--role scheduler`)
* `scheduler.state_location` configured (shared object store for job state and results)
* At least one executor node connected to the scheduler

## HTTP REST API

Base path: `/v1/queries`

### Endpoints

| Method | Path                                                  | Description                             |
| ------ | ----------------------------------------------------- | --------------------------------------- |
| `POST` | `/v1/queries`                                         | Submit a query for async execution      |
| `GET`  | `/v1/queries`                                         | List all queries                        |
| `GET`  | `/v1/queries/{query_id}`                              | Get query status and first result chunk |
| `GET`  | `/v1/queries/{query_id}/status`                       | Get query status only                   |
| `GET`  | `/v1/queries/{query_id}/results`                      | Get results (with pagination)           |
| `GET`  | `/v1/queries/{query_id}/results/chunks/{chunk_index}` | Get a specific result chunk             |
| `POST` | `/v1/queries/{query_id}/cancel`                       | Cancel a running query                  |

### Submit Query

`POST /v1/queries`

Submits a SQL query for asynchronous execution and returns immediately with a job ID.

**Request Body** (`application/json`):

| Field             | Type    | Required | Description                                                                      |
| ----------------- | ------- | -------- | -------------------------------------------------------------------------------- |
| `sql`             | string  | Yes      | SQL statement to execute                                                         |
| `parameters`      | array   | No       | Bind variables for parameterized queries (`$1`, `$2`, ...)                       |
| `timeout_seconds` | integer | No       | Maximum execution time in seconds. The query is cancelled and failed on timeout. |
| `maximum_size`    | integer | No       | Maximum result size in bytes. The query is failed if results exceed this limit.  |

**Request Example**:

```json
{
  "sql": "SELECT * FROM large_table WHERE status = $1 AND created_at > $2",
  "parameters": ["active", "2025-01-01"],
  "timeout_seconds": 300,
  "maximum_size": 104857600
}
```

**Response** (HTTP 202 Accepted):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "PENDING",
  "error": null,
  "status_url": "/v1/queries/01ABC-DEF-456-7890AB/status",
  "results_url": "/v1/queries/01ABC-DEF-456-7890AB/results"
}
```

### Get Query

`GET /v1/queries/{query_id}`

Returns the full query status, result manifest, and the first result chunk (if completed successfully).

**Response** (HTTP 200):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "SUCCEEDED",
  "error": null,
  "manifest": {
    "format": "ARROW_IPC",
    "schema": {
      "column_count": 3,
      "columns": [
        { "name": "id", "type_name": "Int64", "nullable": false, "position": 0 },
        { "name": "status", "type_name": "Utf8", "nullable": true, "position": 1 },
        { "name": "created_at", "type_name": "Timestamp(Microsecond, Some(\"UTC\"))", "nullable": true, "position": 2 }
      ]
    },
    "total_row_count": 25000,
    "total_chunk_count": 3
  },
  "result": {
    "chunk_index": 0,
    "row_offset": 0,
    "row_count": 10000,
    "next_chunk_index": 1,
    "next_chunk_url": "/v1/queries/01ABC-DEF-456-7890AB/results/chunks/1",
    "data_array": [
      { "id": 1, "status": "active", "created_at": "2025-06-15T10:30:00Z" }
    ]
  },
  "created_at": "2026-03-02T12:00:00+00:00",
  "started_at": "2026-03-02T12:00:00.050+00:00",
  "completed_at": "2026-03-02T12:00:05.200+00:00",
  "expires_at": "2026-03-03T00:00:05.200+00:00"
}
```

### Get Status

`GET /v1/queries/{query_id}/status`

Returns the current status of a query without result data. Use this for lightweight polling.

**Response** (HTTP 200):

```json
{
  "status": "RUNNING",
  "error": null
}
```

When the query has failed:

```json
{
  "status": "FAILED",
  "error": {
    "error_code": "EXECUTION_FAILED",
    "message": "Table 'missing_table' not found",
    "sql_state": null
  }
}
```

### Get Results

`GET /v1/queries/{query_id}/results`

Returns result data for a completed query. Use the `partition` query parameter to paginate through chunks.

**Query Parameters**:

| Parameter   | Type    | Default | Description                       |
| ----------- | ------- | ------- | --------------------------------- |
| `partition` | integer | `0`     | Chunk index to retrieve (0-based) |

**Response** (HTTP 200):

```json
{
  "chunk_index": 0,
  "row_offset": 0,
  "row_count": 10000,
  "next_chunk_index": 1,
  "next_chunk_url": "/v1/queries/01ABC-DEF-456-7890AB/results/chunks/1",
  "data_array": [
    { "id": 1, "status": "active" }
  ]
}
```

When the last chunk is reached, `next_chunk_index` and `next_chunk_url` are `null`.

**Error Responses**:

| HTTP Status   | Condition                                    |
| ------------- | -------------------------------------------- |
| 404           | Query not found                              |
| 410 Gone      | Query results expired or query was cancelled |
| 425 Too Early | Query is still pending or running            |
| 500           | Query execution failed                       |

### Get Chunk

`GET /v1/queries/{query_id}/results/chunks/{chunk_index}`

Returns a specific result chunk by index. Same response format as **Get Results**.

**Error Responses**:

| HTTP Status  | Condition                                    |
| ------------ | -------------------------------------------- |
| 404          | Query or chunk not found                     |
| 409 Conflict | Query not yet complete (pending or running)  |
| 410 Gone     | Query results expired or query was cancelled |
| 500          | Query execution failed                       |

### Cancel Query

`POST /v1/queries/{query_id}/cancel`

Cancels a running query. Also cancels the underlying distributed query on the Ballista scheduler.

**Response** (HTTP 200):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "CANCELLED",
  "error": null,
  "manifest": null,
  "result": null,
  "created_at": "2026-03-02T12:00:00+00:00",
  "started_at": "2026-03-02T12:00:00.050+00:00",
  "completed_at": "2026-03-02T12:00:02.100+00:00",
  "expires_at": null
}
```

### List Queries

`GET /v1/queries`

Lists all queries, optionally filtered by status.

**Query Parameters**:

| Parameter | Type    | Default | Description                                                                                               |
| --------- | ------- | ------- | --------------------------------------------------------------------------------------------------------- |
| `status`  | string  | *all*   | Filter by status: `queued`/`pending`, `running`, `completed`/`succeeded`, `failed`, `cancelled`, `closed` |
| `limit`   | integer | `100`   | Maximum number of results                                                                                 |

**Response** (HTTP 200):

```json
{
  "queries": [
    {
      "query_id": "01ABC-DEF-456-7890AB",
      "status": "RUNNING",
      "sql_preview": "SELECT * FROM large_table WHERE status = ...",
      "created_at": "2026-03-02T12:00:00+00:00"
    }
  ],
  "total_count": 1
}
```

The `sql_preview` field contains the first 100 characters of the SQL statement.

## Arrow Flight API

The async query API is also available via Apache Arrow Flight `DoAction` requests. This is more efficient for programmatic access since results are returned in Arrow IPC binary format instead of JSON.

### Actions

| Action Type           | Request Body (JSON)                     | Response                                                              |
| --------------------- | --------------------------------------- | --------------------------------------------------------------------- |
| `SubmitAsyncQuery`    | `{"sql": "...", "parameters": [...]}`   | JSON: `{"query_id": "...", "status": "PENDING"}`                      |
| `GetAsyncQueryStatus` | `{"query_id": "..."}`                   | JSON: query status with error/result metadata                         |
| `GetAsyncQueryResult` | `{"query_id": "...", "chunk_index": 0}` | Binary: Arrow IPC stream                                              |
| `CancelAsyncQuery`    | `{"query_id": "..."}`                   | JSON: `{"query_id": "...", "cancelled": true, "status": "CANCELLED"}` |

### SubmitAsyncQuery

**Request**:

```json
{
  "sql": "SELECT * FROM large_table",
  "parameters": []
}
```

**Response** (JSON):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "PENDING"
}
```

### GetAsyncQueryStatus

**Request**:

```json
{
  "query_id": "01ABC-DEF-456-7890AB"
}
```

**Response** (JSON):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "SUCCEEDED",
  "error": null,
  "result": {
    "total_row_count": 25000,
    "total_chunk_count": 3
  }
}
```

### GetAsyncQueryResult

**Request**:

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "chunk_index": 0
}
```

**Response**: Arrow IPC binary stream containing the `RecordBatch` data for the requested chunk.

### CancelAsyncQuery

**Request**:

```json
{
  "query_id": "01ABC-DEF-456-7890AB"
}
```

**Response** (JSON):

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "cancelled": true,
  "status": "CANCELLED"
}
```

## CLI

The `spice query` command provides a CLI and interactive REPL for the async queries API.

### Submit and Wait

```bash
spice query "SELECT * FROM orders WHERE total > 100 LIMIT 50;"
```

The CLI auto-polls with a spinner and displays results when ready. Press `Ctrl+C` to stop waiting — the query continues running in the background.

### Submit Without Waiting

```bash
spice query "SELECT * FROM very_large_table;" --no-wait
```

### Options

| Option                  | Default | Description                                                                                       |
| ----------------------- | ------- | ------------------------------------------------------------------------------------------------- |
| `--no-wait`             | `false` | Submit the query and return immediately without waiting for results                               |
| `--timeout <DURATION>`  | *none*  | Maximum client-side wait time (e.g., `30s`, `5m`). The query itself continues running on timeout. |
| `-o, --output <FORMAT>` | `table` | Output format: `table` or `json`                                                                  |

### Subcommands

```bash
spice query list [--status X] [--limit N]    # List queries
spice query status <query_id>                # Check query status
spice query results <query_id>               # Fetch results of completed query
spice query cancel <query_id>                # Cancel a running query
```

### Interactive REPL

When invoked without arguments, `spice query` starts an interactive REPL:

```
query> SELECT COUNT(*)
     > FROM large_table
     > WHERE status = 'active';
Submitted query: 01ABC-DEF-456-7890AB (PENDING)
Press Ctrl+C to stop waiting (query continues in background)
⠹ RUNNING (2.3s)...
✓ SUCCEEDED (5.1s)
+----------+
| count(*) |
+----------+
| 42000    |
+----------+

Time: 5.10000000 seconds. 1 rows.
```

**REPL Commands**:

| Command                | Description                                    |
| ---------------------- | ---------------------------------------------- |
| `.list`                | List all queries tracked in this REPL session  |
| `.status <id>`         | Show detailed status of a query                |
| `.results <id>`        | Fetch and display results of a completed query |
| `.wait <id>`           | Resume waiting for a query to complete         |
| `.cancel <id>`         | Cancel a running query                         |
| `.clear`               | Clear the local tracked queries list           |
| `.clear history`       | Clear command history                          |
| `.help`                | Show available commands                        |
| `.exit`, `.quit`, `.q` | Exit the REPL                                  |

Query IDs can be abbreviated if they uniquely identify a query within the tracked session.

## Job Lifecycle

```
PENDING → RUNNING → SUCCEEDED → CLOSED (after 12h TTL)
                   → FAILED
                   → CANCELLED
```

### Statuses

| Status      | Description                                          |
| ----------- | ---------------------------------------------------- |
| `PENDING`   | Job is queued but not yet executing                  |
| `RUNNING`   | Job is actively executing on the distributed cluster |
| `SUCCEEDED` | Job completed successfully, results are available    |
| `FAILED`    | Job execution failed (see `error` field for details) |
| `CANCELLED` | Job was cancelled by the user                        |
| `CLOSED`    | Job results have expired and been cleaned up         |

## Error Codes

When a query fails, the `error` object contains an `error_code` field:

| Error Code                 | Description                                             |
| -------------------------- | ------------------------------------------------------- |
| `SCHEDULER_UNAVAILABLE`    | The Ballista scheduler is not reachable                 |
| `SUBMISSION_FAILED`        | Failed to submit the query to the distributed scheduler |
| `EXECUTION_FAILED`         | The query failed during execution                       |
| `FETCHING_RESULTS_FAILED`  | Failed to retrieve results from executor nodes          |
| `CANCELLED`                | The query was explicitly cancelled                      |
| `PARAMETER_BINDING_FAILED` | Failed to bind the provided query parameters            |
| `NOT_FOUND`                | The referenced query or job was not found               |
| `INTERNAL`                 | An unexpected internal error occurred                   |
| `TIMEOUT`                  | The query exceeded the configured `timeout_seconds`     |

## HTTP Error Responses

| HTTP Status               | Condition                                                              |
| ------------------------- | ---------------------------------------------------------------------- |
| 202 Accepted              | Query successfully submitted                                           |
| 200 OK                    | Status/results retrieved successfully                                  |
| 404 Not Found             | Query ID, chunk, or result not found                                   |
| 409 Conflict              | Query not yet complete (when fetching results)                         |
| 410 Gone                  | Query results have expired                                             |
| 425 Too Early             | Query still running (results endpoint)                                 |
| 500 Internal Server Error | Execution or serialization failure                                     |
| 503 Service Unavailable   | Not running in scheduler cluster mode, or executor not yet initialized |

## Storage Layout

Job state and result chunks are stored in the shared object store configured via `scheduler.state_location`:

```
{base_prefix}/
├── jobs/
│   ├── {job_id}.json          # Job state (JSON)
│   └── {job_id}/
│       ├── chunk_0.arrow      # Result chunk 0 (Arrow IPC)
│       ├── chunk_1.arrow      # Result chunk 1
│       └── ...
```

## Defaults

| Setting    | Default     |
| ---------- | ----------- |
| Chunk size | 10,000 rows |
| Result TTL | 12 hours    |
| List limit | 100 queries |

## Limitations

* Only available in cluster mode with `--role scheduler`
* Requires `scheduler.state_location` to be configured
* The `format` query parameter on the results endpoint is declared but not yet implemented (results are always JSON over HTTP, Arrow IPC over Flight)
* Result TTL is not yet configurable per-query (fixed at 12 hours)
* Chunk size is not yet configurable per-query (fixed at 10,000 rows)
