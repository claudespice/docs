---
description: User-defined SQL functions (UDFs) declared in a spicepod's `functions:` section — SQL and Remote HTTP tiers.
icon: function
---

# User-Defined Functions

Spice.ai supports **user-defined functions (UDFs)** declared declaratively in a spicepod's `functions:` section. Two execution tiers are available:

- **SQL** (`from: sql`) — in-process scalar UDF whose body is a DataFusion SQL expression.
- **Remote** (`from: http://…` or `from: https://…`) — async scalar UDF that invokes a remote HTTP endpoint with a JSON row batch and receives a JSON value column back.

Functions are automatically registered into the SQL session, exposed via the `list_udfs()` UDTF and the `GET /v1/functions` HTTP endpoint, and (by default) surfaced to LLM tool-calling. They hot-reload when the spicepod changes on disk.

{% hint style="warning" %}
User-defined functions are currently in **ALPHA**. Behaviour, on-disk schema, and APIs may change without notice. A one-time warning is logged when the first `functions:` entry is registered.
{% endhint %}

## Quickstart

Declare a function in `spicepod.yaml`:

```yaml
version: v2
kind: Spicepod
name: my_app

functions:
  - name: haversine_km
    from: sql
    description: Haversine distance in kilometres.
    volatility: immutable
    signature:
      args:
        - { name: lat1, type: float64 }
        - { name: lon1, type: float64 }
        - { name: lat2, type: float64 }
        - { name: lon2, type: float64 }
      returns: float64
    body: |
      6371 * acos(
        cos(radians(lat1)) * cos(radians(lat2)) *
        cos(radians(lon2) - radians(lon1)) +
        sin(radians(lat1)) * sin(radians(lat2))
      )
```

Use it in SQL:

```sql
SELECT haversine_km(lat1, lon1, lat2, lon2) FROM trips;
```

## Schema Reference

Each entry in `functions:` is a `Function` object. Fields are strictly validated (`deny_unknown_fields` is enforced).

| Field         | Type     | Required | Description                                                                                                                                                                   |
| ------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`        | string   | yes      | Identifier the function is registered under. Referenced by that name in SQL.                                                                                                  |
| `from`        | string   | yes      | Source URI selecting the execution tier. `sql`, `http://…`, `https://…`.                                                                                                      |
| `description` | string   | no       | Free-form description surfaced in `list_udfs()` and `GET /v1/functions`.                                                                                                      |
| `kind`        | enum     | no       | `scalar` (default), `aggregate`, `window`, `table`. Only `scalar` is wired today; other values parse but are rejected at registration with a clear error.                     |
| `volatility`  | enum     | no       | `immutable`, `stable`, `volatile` (default). See [Volatility](#volatility).                                                                                                   |
| `signature`   | object   | yes      | Typed signature. See below.                                                                                                                                                   |
| `body`        | string   | SQL only | Inline SQL expression. Mutually exclusive with `body_ref`.                                                                                                                    |
| `body_ref`    | string   | SQL only | Path to a file whose contents are the function body. Resolved relative to the runtime's CWD. Mutually exclusive with `body`. Must **not** be set for non-SQL `from:` schemes. |
| `metadata`    | map      | no       | Free-form metadata surfaced alongside the declaration.                                                                                                                        |
| `params`      | map      | no       | Tier-specific knobs. Supports `${ secrets:KEY }` / `${ env:KEY }` interpolation. See [Remote params](#remote-params).                                                         |
| `dependsOn`   | string[] | no       | Names of spicepod components that must load before this function.                                                                                                             |
| `metrics`     | object   | no       | Per-function metrics configuration.                                                                                                                                           |
| `as_tool`     | bool     | no       | Expose the function as an LLM tool. Defaults to `true`. See [LLM Tool Exposure](#llm-tool-exposure).                                                                          |

### `signature`

```yaml
signature:
  args:
    - { name: x, type: int64 }
  returns: int64           # required for scalar / aggregate / window
  returns_schema: []       # required for table kind, ignored otherwise
  null_aware: false        # optional; default false
```

| Field            | Description                                                                                                                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `args`           | Positional argument list. Empty for niladic functions.                                                                                                                                                                      |
| `args[].name`    | Argument name. Referenced by name in SQL bodies.                                                                                                                                                                            |
| `args[].type`    | Arrow logical-type string (e.g. `float64`, `utf8`, `timestamp(us)`). See [Supported types](#supported-types).                                                                                                               |
| `returns`        | Arrow return type. Required for non-table kinds.                                                                                                                                                                            |
| `returns_schema` | Output columns for table functions. Required for `kind: table`.                                                                                                                                                             |
| `null_aware`     | When `false` (default), DataFusion short-circuits any call with a NULL argument to a NULL result without invoking the function. Set to `true` if the body must inspect NULL inputs itself. Matches Spark default semantics. |

## Execution Tiers

### SQL (`from: sql`)

The body is parsed into a DataFusion logical expression against a schema derived from the argument list, then lowered to a physical expression at build time. The standard DataFusion scalar functions (math, string, datetime), Spark built-ins, and `datafusion-functions-json` are all available in bodies.

```yaml
- name: shout
  from: sql
  volatility: immutable
  signature:
    args: [{ name: s, type: utf8 }]
    returns: utf8
  body: "upper(s)"
```

The body runs entirely in-process with no sandbox. Prefer SQL-tier UDFs for anything that can be expressed in a SQL expression — they're fastest, type-checked at startup, and don't leave the runtime.

#### `body_ref` for longer bodies

Keep non-trivial SQL in its own file with proper editor support:

```yaml
- name: haversine_km
  from: sql
  signature: { args: [...], returns: float64 }
  body_ref: ./queries/haversine.sql
```

`body` and `body_ref` are mutually exclusive; exactly one must be set when `from: sql`.

### Remote (`from: http://…` | `https://…`)

The remote tier invokes an external HTTP endpoint for each batch of rows. Powered by DataFusion's `AsyncScalarUDFImpl`, so the query scheduler overlaps remote calls with other work.

```yaml
- name: classify_intent
  from: http://classifier.internal/v1/classify
  volatility: volatile
  signature:
    args: [{ name: prompt, type: utf8 }]
    returns: utf8
  params:
    timeout: 2s
    batch_size: 256
    auth_bearer: ${secrets:classifier_token}
```

#### Wire contract

- **Request** — `POST <endpoint>`, `Content-Type: application/json`, body:
  ```json
  {"rows": [{"<arg_name>": <value>, ...}, ...]}
  ```
- **Response** — HTTP `200` with body:
  ```json
  {"values": [<row_0_result>, <row_1_result>, ...]}
  ```
- `values.length` **must** equal `rows.length`. A mismatch is treated as an execution error.
- Non-`2xx` responses surface as query errors, with the response body snippet (up to 256 chars, one line) included in the error for debugging.

#### Remote params

| Key           | Type                                                   | Default | Description                                            |
| ------------- | ------------------------------------------------------ | ------- | ------------------------------------------------------ |
| `timeout`     | integer seconds **or** duration string (`2s`, `500ms`) | `30s`   | Per-call HTTP timeout.                                 |
| `batch_size`  | integer, `1..=100000`                                  | `1024`  | Rows per HTTP request. Large inputs are chunked.       |
| `auth_bearer` | string (supports `${secrets:…}`)                       | none    | Sets `Authorization: Bearer <value>` on every request. |

Batches are currently issued **sequentially** per query. Parallel fan-out is on the roadmap.

## Supported Types

Type support differs by tier — pick the narrower set if you need to switch between tiers later.

### SQL tier

Type names are case-insensitive. Accepts shorthand (`string` ↔ `utf8`, `bool` ↔ `boolean`).

| Arrow type                                                        | Notes                    |
| ----------------------------------------------------------------- | ------------------------ |
| `int8`, `int16`, `int32`, `int64`                                 |                          |
| `uint8`, `uint16`, `uint32`, `uint64`                             |                          |
| `float32`, `float64`                                              |                          |
| `utf8` / `string`                                                 |                          |
| `boolean` / `bool`                                                |                          |
| `binary`                                                          |                          |
| `date32`, `date64`                                                |                          |
| `timestamp(s)`, `timestamp(ms)`, `timestamp(us)`, `timestamp(ns)` | No timezone support yet. |

Return types are coerced implicitly where DataFusion knows how (e.g. `Int32 → Int64`, `Float32 → Float64`, `Utf8 → LargeUtf8`) — a literal like `6371` widening to `Float64` is fine.

Complex types (`list<…>`, `struct<…>`, `decimal(p,s)`, timestamp with timezone) return a clear `UnsupportedArrowType` error at build time.

### Remote tier

| Arrow type                                      | JSON wire form        |
| ----------------------------------------------- | --------------------- |
| `int64` (also accepts `int8/16/32/int`)         | JSON number (integer) |
| `float64` (also accepts `float32/float/double`) | JSON number           |
| `utf8` / `string`                               | JSON string           |
| `boolean` / `bool`                              | JSON boolean          |

Narrower integers and `float32` silently widen to the canonical Arrow type over the JSON wire. Any other type (`binary`, `date32`, `timestamp(…)`, complex) returns `UnsupportedArrowType` at build time.

## Volatility

Volatility governs caching, constant-folding, and plan-time evaluation. Defaults to `volatile` — the safest choice.

| Value       | Meaning                                                                 | Safe to                       |
| ----------- | ----------------------------------------------------------------------- | ----------------------------- |
| `immutable` | Same inputs always yield the same output (e.g. `abs`, `upper`).         | Constant-fold, cache forever. |
| `stable`    | Stable within a single query, may change across queries (e.g. `now()`). | Cache per query.              |
| `volatile`  | Unpredictable on every call (e.g. `random()`).                          | Never cache.                  |

{% hint style="info" %}
**Remote tier auto-caps at `stable`.** A user-declared `immutable` or `stable` remote function is lowered to DataFusion's `Stable` volatility — constant-folding is disabled because the runtime cannot prove the remote service is deterministic or stable across process lifetimes. Queries within a single execution still benefit from common-subexpression elimination.
{% endhint %}

## Federation Deny-List

Every user function is automatically added to Spice's federation deny-list. This prevents the query planner from attempting to push a user function down into a federated data source (e.g. Postgres, MySQL, Databricks) — the remote source doesn't know about it, and a pushdown would either fail or, worse, silently call a different function with the same name.

Built-in Spice UDFs (`ai`, `embed`, `bucket`, identity SQL functions, etc.) are likewise denied. Functions are added on registration and removed on deregistration / hot-reload removal. No manual configuration is required.

## Hot-Reload

When the spicepod file changes on disk, Spice diffs the `functions:` section against the live set:

- **Removed** functions are deregistered from DataFusion, removed from the deny-list, removed from `USER_FUNCTION_INFO`, and dropped from the tool registry.
- **Changed** functions are deregistered and re-registered (equality is structural — any field change triggers a re-register).
- **Added** functions are built and registered.

Failures to build a new or changed function are logged at `ERROR` and do not halt the diff — other functions still apply.

## Introspection

### SQL: `list_udfs()`

A built-in UDTF returns every function registered in the current session — Spice built-ins, DataFusion standard library, and user-declared functions together.

```sql
SELECT name, source, kind, volatility, "from", description
FROM list_udfs()
WHERE source = 'user';
```

| Column        | Type             | Description                                                                 |
| ------------- | ---------------- | --------------------------------------------------------------------------- |
| `name`        | `utf8`, NOT NULL | UDF identifier.                                                             |
| `source`      | `utf8`, NOT NULL | `"builtin"` or `"user"`.                                                    |
| `kind`        | `utf8`, nullable | `"scalar"` \| `"aggregate"` \| `"window"` \| `"table"`. NULL for built-ins. |
| `volatility`  | `utf8`, nullable | `"immutable"` \| `"stable"` \| `"volatile"`. NULL for built-ins.            |
| `from`        | `utf8`, nullable | Source URI for user functions. NULL for built-ins.                          |
| `description` | `utf8`, nullable | Free-form description.                                                      |

### HTTP: `GET /v1/functions`

Returns only the user-declared functions (not built-ins). Intended for operational tooling.

```bash
curl -H "Authorization: Bearer $API_KEY" http://localhost:8090/v1/functions
```

```json
[
  {
    "name": "haversine_km",
    "kind": "scalar",
    "volatility": "immutable",
    "from": "sql",
    "description": "Haversine distance in kilometres"
  }
]
```

The OpenAPI spec exposes this under `operation_id: list_functions`, tag `Functions`.

## LLM Tool Exposure

When `as_tool: true` (the default), every declared function is also registered as an LLM tool. The tool invokes the function through a `SELECT fn_name(arg0, …) AS result` query over the runtime's DataFusion session, so both SQL and Remote tiers dispatch transparently.

- Argument values are interpolated as **typed SQL literals** by the tool bridge — integers, floats, strings, booleans. No free-form SQL from the model is ever concatenated.
- The tool's JSON Schema `parameters` is derived from the function's Arrow signature. Types outside the JSON-encodable primitive set (`int64`, `float64`, `utf8`, `boolean`) are not eligible for tool exposure — the function still registers for SQL use, but tool registration is skipped with a `WARN` log.
- Name collisions with a previously-registered tool (built-in or `tools:` entry) are skipped with a `WARN` log. Rename one, or set `as_tool: false` on the function.

```yaml
# SQL-callable only — not surfaced to the LLM.
- name: internal_hash
  from: sql
  signature: { args: [{ name: x, type: int64 }], returns: int64 }
  body: "x * 2654435761"
  as_tool: false
```

## Distributed Execution

In a Spice.ai Enterprise [distributed cluster](distributed-query.md), UDFs declared on the scheduler are automatically propagated to executors as part of the `GetAppDefinition` bootstrap RPC — executors load the full Spicepod definition (datasets, catalogs, views, and UDFs) from the scheduler at startup, so query planning and execution use a consistent view of the function set across all nodes.

User functions are deny-listed from federation pushdown on every node, so there's no risk of a stray executor attempting to push a user function into a downstream source.

## Limitations

Current (ALPHA) scope:

- Only `kind: scalar` is implemented. `aggregate`, `window`, `table` parse but are rejected at registration.
- Only `from: sql`, `from: http://…`, `from: https://…` are implemented. Other schemes (e.g. `wasm:…`, `grpc://…`, `flight://…`) parse but are rejected — forward-compatible spicepods still load.
- SQL tier does **not** support complex Arrow types (list, struct, decimal, timestamp-with-timezone).
- Remote tier supports only primitive types over the JSON wire (`int64`, `float64`, `utf8`, `boolean`).
- Remote tier issues batches sequentially per query; parallel fan-out, retries, and circuit-breaker behaviour are not yet implemented.
- No aggregate/window semantics, UDAF pushdown, or execution-engine delegation beyond what DataFusion's scalar UDF path provides.
