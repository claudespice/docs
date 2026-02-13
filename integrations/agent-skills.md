---
description: Agent Skills for AI coding agents working with Spice.ai
icon: wand-magic-sparkles
---

# Agent Skills

[Agent Skills](https://github.com/spiceai/skills) are packaged instructions and scripts that extend AI coding agent capabilities for working with the [Spice.ai](https://spiceai.org) runtime. Skills follow the [Agent Skills](https://agentskills.io/) format and work with agents like Claude Code, Cursor, Windsurf, and other AI coding assistants.

## Available Skills

| Skill | Description |
| ----- | ----------- |
| [spice-setup](https://github.com/spiceai/skills/tree/main/spice-setup) | Install Spice, initialize a project, and run the runtime |
| [spice-connect-data](https://github.com/spiceai/skills/tree/main/spice-connect-data) | Connect to data sources and query across them with federated SQL |
| [spice-acceleration](https://github.com/spiceai/skills/tree/main/spice-acceleration) | Accelerate data locally for sub-second query performance |
| [spice-search](https://github.com/spiceai/skills/tree/main/spice-search) | Search with vector similarity, full-text keywords, or hybrid RRF |
| [spice-ai](https://github.com/spiceai/skills/tree/main/spice-ai) | Add AI capabilities — chat, text-to-SQL, tools, memory, model routing |
| [spice-caching](https://github.com/spiceai/skills/tree/main/spice-caching) | Cache query and search results with TTL and stale-while-revalidate |
| [spice-secrets](https://github.com/spiceai/skills/tree/main/spice-secrets) | Manage credentials with secret stores |

## Installation

Install skills using the `npx skills` CLI:

```bash
npx skills add spiceai/skills
```

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

## Skill Overview

### spice-setup

Get started with Spice.ai — install the runtime, initialize a project, and run it. Use this skill when setting up a new Spice project, running `spice run`, looking up CLI commands, or creating a `spicepod.yaml`.

### spice-connect-data

Connect Spice to data sources and query across them with [federated SQL](../features/federated-sql-query.md). Use when connecting to databases (Postgres, MySQL, DynamoDB), data lakes (S3, Delta Lake, Iceberg), warehouses (Snowflake, Databricks), files, APIs, or catalogs.

### spice-acceleration

Accelerate data locally for sub-second query performance with [data acceleration](../features/data-acceleration/). Use when enabling acceleration, choosing an engine (Arrow, DuckDB, SQLite), configuring refresh modes, setting up retention policies, or materializing datasets.

### spice-search

Search data using vector similarity, full-text keywords, or hybrid methods with Reciprocal Rank Fusion (RRF). Use when setting up [search and retrieval](../features/search-and-retrieval.md), configuring embeddings, or writing search queries.

### spice-ai

Add AI and LLM capabilities — chat completions, text-to-SQL (NSQL), tool use, memory, and model routing. Use when configuring language models through the [AI Gateway](../features/ai-gateway.md), enabling tools, or using the OpenAI-compatible API.

### spice-caching

Cache SQL query results, search results, and embeddings with configurable TTL, max size, and eviction policies. Use when setting up or tuning in-memory caching with stale-while-revalidate support.

### spice-secrets

Manage credentials with secret stores including environment variables, Kubernetes secrets, AWS Secrets Manager, and OS keyring. Use when configuring API keys, passwords, or tokens for data sources and model providers.

## Skill Structure

Each skill contains:

- `SKILL.md` — Instructions for the agent
- `scripts/` — Helper scripts for automation (optional)
- `examples/` — Example configurations (optional)

## References

- [Skills Repository](https://github.com/spiceai/skills)
- [Agent Skills Format](https://agentskills.io/)
- [Spice.ai OSS](https://github.com/spiceai/spiceai)
- [Spice Cookbook](https://github.com/spiceai/cookbook)
