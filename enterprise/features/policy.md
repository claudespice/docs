---
description: Cedar-based authorization policies for Spice.ai Enterprise — fine-grained access control over datasets, models, tools, and endpoints.
icon: shield-keyhole
---

# Authorization Policy (Cedar)

Spice.ai Enterprise enforces fine-grained authorization using [Cedar](https://www.cedarpolicy.com/), the open-source policy language developed by AWS. Policies decide, on every request, whether an authenticated principal may perform a given action on a Spice resource.

{% hint style="info" %}
Cedar-based authorization policy is a **Spice.ai Enterprise** feature. It is layered on top of [Authentication](authentication.md) — authentication establishes _who_ the principal is; policy decides _what_ they can do.
{% endhint %}

## Authorization Model

Policy evaluation is the standard Cedar `(principal, action, resource, context)` decision. Spice.ai supplies an embedded Cedar schema that defines the entity types, actions, and attributes available in policies.

### Entity types

| Entity            | Description                                                                 | Notable attributes                       |
| ----------------- | --------------------------------------------------------------------------- | ---------------------------------------- |
| `Spice::User`     | An authenticated principal (OIDC subject, API key identity, …). `in [Role]` | `org_id`                                 |
| `Spice::Role`     | A role or group the user belongs to (mapped from OIDC groups / API key tags) | —                                        |
| `Spice::Dataset`  | A registered dataset (table) in the runtime                                 | `catalog`, `schema`                      |
| `Spice::Model`    | An LLM model available for inference                                        | —                                        |
| `Spice::Tool`     | A tool (built-in or MCP) available for execution                            | —                                        |
| `Spice::Endpoint` | An API endpoint category (e.g. `chat`, `search`, `sql`)                     | —                                        |

### Actions

| Action                                                 | Applies to        | Description                              |
| ------------------------------------------------------ | ----------------- | ---------------------------------------- |
| `Spice::Action::"query"`                               | `Spice::Dataset`  | `SELECT` and read paths.                 |
| `Spice::Action::"insert"`                              | `Spice::Dataset`  | `INSERT` write path.                     |
| `Spice::Action::"update"`                              | `Spice::Dataset`  | `UPDATE` write path.                     |
| `Spice::Action::"delete"`                              | `Spice::Dataset`  | `DELETE` write path.                     |
| `Spice::Action::"ddl"`                                 | `Spice::Dataset`  | DDL operations on a dataset.             |
| `Spice::Action::"invoke"`                              | `Spice::Model`    | Inference / chat completion.             |
| `Spice::Action::"execute"`                             | `Spice::Tool`     | Tool invocation (built-in or MCP).       |
| `Spice::Action::"access"`                              | `Spice::Endpoint` | Reaching an endpoint category.           |

The Cedar schema is fixed by the runtime; policies reference these types and actions directly.

## Configuration

Policies are configured under `runtime.authorization` in `spicepod.yaml`. Authentication must be configured separately under `runtime.auth` — see [Authentication](authentication.md).

```yaml
runtime:
  auth:
    oidc:
      issuer: https://auth.example.com/
      audience: spice-runtime

  authorization:
    enabled: true              # default: true
    default: allow             # allow | deny  — decision when no policy matches
    provider: local            # local | operator | cloud
    policies:
      - name: analysts-read-only
        cedar: |
          permit(
            principal in Spice::Role::"analyst",
            action == Spice::Action::"query",
            resource
          );
      - name: block-pii
        path: ./policies/block-pii.cedar
```

### `runtime.authorization` fields

| Field      | Type                                          | Default | Description                                                                                           |
| ---------- | --------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------- |
| `enabled`  | bool                                          | `true`  | Toggles policy evaluation.                                                                            |
| `default`  | `allow` \| `deny`                             | `allow` | Decision when no policy matches. Set to `deny` for production deny-by-default deployments.            |
| `provider` | `local` \| `operator` \| `cloud`              | `local` | Source of policies. `local` reads inline / file; `operator` polls the Spice K8s Operator; `cloud` polls the Spice Cloud Management API. |
| `policies` | list of [`PolicyDefinition`](#policydefinition) | `[]`    | Policy entries (used with `provider: local`).                                                         |
| `operator` | object                                        | —       | Operator provider config. Fields: `endpoint`, `poll_interval` (default `30s`).                        |
| `cloud`    | object                                        | —       | Cloud provider config. Fields: `poll_interval` (default `60s`).                                       |

#### PolicyDefinition

| Field   | Type   | Description                                                                                  |
| ------- | ------ | -------------------------------------------------------------------------------------------- |
| `name`  | string | Human-readable identifier used in logs and decision diagnostics.                             |
| `cedar` | string | Inline Cedar policy text. Mutually compatible with `path`; both may be used together.        |
| `path`  | string | Path to a `.cedar` file, resolved relative to `spicepod.yaml`. Mutually compatible with `cedar`. |

## Policy Examples

### Default-deny baseline

```yaml
runtime:
  authorization:
    default: deny
    policies:
      - name: admins-everything
        cedar: |
          permit(
            principal in Spice::Role::"admin",
            action,
            resource
          );
```

### Read-only analysts

```cedar
permit(
  principal in Spice::Role::"analyst",
  action == Spice::Action::"query",
  resource
);

forbid(
  principal in Spice::Role::"analyst",
  action in [
    Spice::Action::"insert",
    Spice::Action::"update",
    Spice::Action::"delete",
    Spice::Action::"ddl"
  ],
  resource
);
```

### Restrict a dataset to a single role

```cedar
forbid(
  principal,
  action,
  resource == Spice::Dataset::"sales.public.orders"
)
unless { principal in Spice::Role::"sales-ops" };
```

### Block PII columns from non-privileged roles

```cedar
forbid(
  principal,
  action == Spice::Action::"query",
  resource
)
when {
  resource.catalog == "customers" &&
  resource.schema == "pii" &&
  !(principal in Spice::Role::"compliance")
};
```

### Limit model invocation to a paid tier

```cedar
permit(
  principal in Spice::Role::"premium",
  action == Spice::Action::"invoke",
  resource == Spice::Model::"gpt-4o"
);
```

### Endpoint-level access (e.g. lock down `/v1/sql`)

```cedar
forbid(
  principal,
  action == Spice::Action::"access",
  resource == Spice::Endpoint::"sql"
)
unless { principal in Spice::Role::"engineer" };
```

## Policy Providers

| Provider   | Use case                                                   | Reload                                                                       |
| ---------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `local`    | Inline Cedar text and/or `.cedar` files in the spicepod.    | Reloaded on Spicepod restart or operator-driven config rollout.              |
| `operator` | Centralized policy distribution via the Spice K8s Operator. | Polled at `poll_interval` (default `30s`); engine atomically swaps the set.  |
| `cloud`    | Centralized policy distribution via Spice Cloud.            | Polled at `poll_interval` (default `60s`); engine atomically swaps the set.  |

Policy reloads are **atomic**: in-flight requests complete against the previous policy set, and subsequent requests evaluate against the new set. Empty policy fetches do not silently disable enforcement — combined with `default: deny`, an empty set denies everything.

## Combining Policy with Identity SQL Functions

Cedar handles coarse-grained allow/deny decisions across actions and resources. For **row-level** filtering, combine policy with the [identity SQL functions](authentication.md#identity-sql-functions) in dataset views or `WHERE` clauses:

```yaml
views:
  - name: my_orders
    sql: |
      SELECT *
      FROM sales.orders
      WHERE owner_email = current_principal_email()
        OR 'sales-ops' = ANY(current_principal_groups())
```

A typical layered model:

1. Cedar policy decides whether the principal may `query` a dataset at all.
2. SQL views with identity functions filter the rows the principal may see.

## Distributed Cluster Behavior

In a [`SpicepodCluster`](../kubernetes/spicepodcluster.md), the scheduler is the source of truth for policy. Executors pull policy as part of the `GetAppDefinition` bootstrap RPC and re-evaluate when policy changes are pushed; the same Cedar decision applies regardless of which scheduler routes a request. Policy diagnostics (matched policy IDs, decision) are emitted as structured logs and surface in `runtime.task_history`.

## Production Checklist

- [ ] `runtime.authorization.enabled: true` and `default: deny` for production deployments.
- [ ] Roles are sourced from a trusted identity provider (OIDC groups or API key tags) — see [Authentication](authentication.md).
- [ ] Policy files are stored in Git and rolled out via GitOps (Argo CD / Flux) rather than ad-hoc edits on the cluster.
- [ ] An "admin escape hatch" `permit` rule exists and is restricted to a small `Spice::Role::"admin"` group with audited membership.
- [ ] Policy decision logs are forwarded to the SIEM together with authentication logs.
- [ ] Cedar policies are validated in CI with `cedar validate` against the embedded Spice schema before merge.

## See also

- [Authentication](authentication.md)
- [Security](../production/security.md)
- [Distributed Query](distributed-query.md)
- [Cedar policy language reference](https://docs.cedarpolicy.com/)
