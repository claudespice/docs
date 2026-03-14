# Cloud Release Notes

Generate monthly release notes for the Spice.ai Cloud Platform by reconciling changes across multiple repositories. Run this after each open-source Spice runtime release to update the cloud release notes for the current month.

---

## Overview

Cloud release notes combine three categories of changes into a single monthly entry:

1. **Cloud-only changes** — Portal UI, management APIs, billing, onboarding, and other features unique to Spice Cloud (sourced from `spicehq/cloud`)
2. **Runtime updates** — OSS runtime releases that shipped during the month (sourced from `spiceai/spiceai` releases and `spiceai/docs` release posts)
3. **Bug fixes** — Platform-level fixes from the cloud repo, distinct from runtime fixes already covered in OSS release notes

---

## Source Repositories

### 1. `spicehq/cloud` (Cloud Portal & Platform)

The cloud monorepo at `github.com/spicehq/cloud`. This is the primary source for cloud-only changes.

**What to scrape:**

* **Merged PRs since last release notes entry.** Filter by merge date within the target month.
* **Focus areas in the portal app** (`apps/portal/`):
  * `app/(app)/(portal)/` — Portal pages (playground, monitoring, settings, deployments, code editor, plans)
  * `components/` — Shared UI components (chat, SQL explorer, secrets, models, traces, datasets)
  * `app/api/` — API routes (cron jobs, proxy endpoints, management APIs)
  * `packages/shared/` — Shared libraries (spicepod types, channels, icons)
  * `docs/adr/` — Architecture Decision Records (signal significant changes)
* **PR titles and descriptions** are the primary signal. Look for:
  * New portal pages or features
  * API endpoint additions (`/v1/...` routes)
  * UI redesigns or significant UX improvements
  * New integrations (model providers, data connectors in the UI)
  * Settings and configuration changes
  * Onboarding flow changes
  * Monitoring and observability additions

**What to exclude:**

* Internal refactors with no user-facing impact
* Dependency bumps (unless they unlock a user-facing feature)
* Test-only changes
* CI/CD pipeline changes

### 2. `spiceai/spiceai` (Open-Source Runtime)

The OSS runtime at `github.com/spiceai/spiceai`. Source for runtime version information.

**What to scrape:**

* **GitHub Releases** for the target month. Each release has a tag (e.g., `v1.11.4`), release date, and release notes body.
* **Release notes content** includes: new features, improvements, bug fixes, breaking changes, and contributors.

**How to use:** Extract the version number, release date, and a concise summary (3-5 bullet points) of the most significant changes. Link to the full release notes on the docs site.

### 3. `spiceai/docs` (OSS Docs Site — Release Posts)

The OSS docs site at `github.com/spiceai/docs`. Contains detailed release blog posts.

**What to scrape:**

* **Release post files** in `website/releases/` (e.g., `v1.11.4.md`).
* These contain the full narrative of each release — use them to write accurate, concise summaries for the cloud release notes.

### 4. `spicehq/docs` (Cloud Docs — Release Notes File)

The cloud docs at `github.com/spicehq/docs`. This is where the output is published.

**Target file:** `reference/release-notes.md`

---

## Output Format

Each monthly entry follows this exact structure. Insert new months at the **top** of the file, directly below the `# Release Notes` heading.

```markdown
## {Month} {Year}

### Highlights

* **{Feature Name}** – {One-sentence description of the feature and its user impact.}
* **{Feature Name}** – {One-sentence description.}
  ...

### Runtime

Spice runtime [{version}](https://spiceai.org/releases/{version}) ({Month} {Day}, {Year}):

* {Concise description of notable runtime change.}
* {Concise description of notable runtime change.}
  ...

{If multiple runtime versions shipped in the month, add a separate block for each:}

Spice runtime [{version}](https://spiceai.org/releases/{version}) ({Month} {Day}, {Year}):

* {Change description.}
  ...

### Bug Fixes

* {Description of cloud platform bug fix.}
* {Description of cloud platform bug fix.}
  ...
```

### Formatting Rules

* **Highlights** are bullet points with bold feature names followed by an em dash (`–`) and a description
* **Runtime** entries link the version number to `https://spiceai.org/releases/{version}` — the version includes the `v` prefix (e.g., `v1.11.4` → `https://spiceai.org/releases/v1.11.4`)
* **Bug Fixes** are for cloud platform fixes only. Runtime bug fixes belong in the Runtime section or the OSS release notes — do not duplicate them
* Use `*` for list items to match the existing convention in `reference/release-notes.md`
* Use backtick formatting for code elements: API paths (`/v1/secrets`), config keys (`on_conflict`), SQL functions (`ai()`)
* Do not use heading levels below `###` within a monthly entry
* Keep descriptions to one sentence. No hype, no marketing language. State what changed and why it matters
* If a feature links to cloud docs, use relative links (e.g., `[SQL](../portal/playground/sql-query-editor.md)`)
* If a feature links to OSS docs, use absolute links (e.g., `[Spice Cayenne](https://spiceai.org/docs/components/data-accelerators/cayenne)`)

---

## Change Categorization

### Cloud-Only (goes in Highlights)

These changes come from `spicehq/cloud` and have no equivalent in the OSS runtime:

| Category                           | Examples                                                                                      |
| ---------------------------------- | --------------------------------------------------------------------------------------------- |
| **Portal UI**                      | New pages, tabbed SQL editor, search in playground, dataset status display, async/sync toggle |
| **Management APIs**                | `api.spice.ai` endpoints, `/v1/secrets`, `/v1/queries`, `/v1/apps/{appId}/metrics`            |
| **Authentication & Authorization** | OAuth clients, PAT scopes, SSO changes                                                        |
| **Billing & Plans**                | Plan changes, usage dashboards, resource limits                                               |
| **Monitoring & Observability**     | Dashboards, log filters, timezone switcher, trace history, audit logging                      |
| **Onboarding**                     | New user flows, AI onboarding, sample datasets, suggested prompts                             |
| **Deployment Management**          | SpicepodCluster, SpicepodSet, private clusters, update channels                               |
| **Infrastructure**                 | Multi-region support, storage APIs, Terraform provider updates                                |
| **Integrations**                   | Databricks OAuth, GitHub branches, model provider additions (GPT-5, Claude, etc.)             |

### Runtime (goes in Runtime section)

These come from `spiceai/spiceai` releases:

| Category              | Examples                                                                   |
| --------------------- | -------------------------------------------------------------------------- |
| **Core engine**       | DataFusion upgrades, Arrow version bumps, acceleration engine improvements |
| **Data connectors**   | New connectors, connector improvements, CDC enhancements                   |
| **Search & AI**       | Vector search, hybrid search, LLM inference, MCP support                   |
| **Acceleration**      | Cayenne improvements, snapshot support, refresh modes                      |
| **Distributed query** | Ballista improvements, multi-node features, mTLS                           |

### Bug Fixes (goes in Bug Fixes section)

Cloud platform fixes only:

| Category           | Examples                                                         |
| ------------------ | ---------------------------------------------------------------- |
| **API fixes**      | Request validation, error handling, response body preservation   |
| **Portal fixes**   | UI rendering issues, editor bugs, navigation fixes               |
| **Reliability**    | Health check improvements, retry logic, deployment handler speed |
| **Authentication** | Login redirect fixes, token handling                             |

---

## Workflow

### Step 1: Determine the Target Month

Identify the calendar month for the release notes entry. This is typically the current month, triggered by an OSS release shipping.

### Step 2: Identify the Date Range

Find the date of the most recent entry in `reference/release-notes.md`. The scrape window is from that date to today.

### Step 3: Scrape Cloud Changes

Collect merged PRs from `spicehq/cloud` within the date range. Use the GitHub API or `gh` CLI:

```bash
gh pr list --repo spicehq/cloud --state merged --search "merged:{start_date}..{end_date}" --limit 100 --json title,body,mergedAt,url
```

Review PR titles and bodies. Categorize each as:

* **Cloud-only feature** → Highlights
* **Bug fix** → Bug Fixes
* **Internal / no user impact** → Skip

### Step 4: Scrape Runtime Releases

Check for OSS releases within the date range:

```bash
gh release list --repo spiceai/spiceai --limit 10
```

For each release in the target month:

1. Read the release notes body from GitHub
2. Read the corresponding release post in `spiceai/docs/website/releases/{version}.md`
3. Extract the 3-5 most significant changes for the Runtime section summary

### Step 5: Draft the Entry

Compose the monthly entry following the Output Format above. Order Highlights by significance — the most impactful cloud features first, followed by smaller improvements.

### Step 6: Validate

* Confirm all runtime version links resolve correctly
* Confirm all doc links are valid (relative for cloud docs, absolute for OSS docs)
* Confirm no duplicate information between Highlights and Runtime sections
* Confirm Bug Fixes are cloud-only (not repeating OSS fixes)
* Confirm the entry is inserted at the correct position (top of the file, below the `# Release Notes` heading)

### Step 7: Publish

1. Create a branch in `spicehq/docs`
2. Edit `reference/release-notes.md` — insert the new month at the top
3. Commit with message: `Add {Month} {Year} release notes`
4. Open a PR for review

---

## Examples

### Minimal Month (Patch Releases Only)

```markdown
## March 2026

### Highlights

* **Runtime Dataset Status** – Dataset status from the runtime is now displayed in the Datasets list.

### Runtime

Spice runtime [v1.11.4](https://spiceai.org/releases/v1.11.4) (Mar 12, 2026):

* Accelerated views now support `on_zero_results: use_source` for source fallback.
* Improved S3 metadata column query robustness.

### Bug Fixes

* Improved error messages for invalid spicepod configurations.
```

### Major Month (Multiple Releases + Cloud Features)

```markdown
## January 2026

### Highlights

* **Arrow v18 Upgrade** – Upgraded platform to Apache Arrow v18 with improved query performance and compatibility.
* **API Enhancements** – Updated OpenAPI spec with new server URLs and improved multi-value header forwarding.
* **Monitoring Dashboards** – Added HTTP and Flight services metrics dashboards; updated data egress, caching, and org usage monitoring charts.
* **AI Onboarding** – OpenAI credits offered on new app creation; sample data option and suggested prompts in the AI chat playground.
* **Provider Management** – Unified provider formats with category support for embeddings and catalogs.

### Runtime

Spice runtime [v1.11.0](https://spiceai.org/releases/v1.11.0) (Jan 28, 2026) – a major release:

* **Spice Cayenne (Beta)** – The Cayenne data accelerator reaches Beta with acceleration snapshots, key-based deletion vectors, and S3 Express One Zone support.
* **DataFusion v51** – SQL pipe operators (`|>`), `DESCRIBE <query>`, named arguments in SQL functions, and significant performance improvements.
* **Arrow v57.2** – 4× faster Parquet metadata parsing with a rewritten thrift metadata parser.
* **Distributed Query** – Active-active HA schedulers, mTLS for cluster communication, and cloud credential propagation to executors.
* **Prepared Statements** – Parameterized queries with query plan caching and SQL injection prevention across all SDKs.
* **Acceleration Snapshots** – Snapshot-based acceleration recovery for faster startup and rollback support.

Spice runtime [v1.10.4](https://spiceai.org/releases/v1.10.4) (Jan 5, 2026):

* Kafka/Debezium batch commit fixes and ABFSS URL support for Azure Data Lake Storage Gen2.

### Bug Fixes

* Improved header forwarding reliability for multi-value headers.
* Fixed retry logic for storage volume queries with backoff.
* Fixed monaco-editor web worker for YAML editing.
```

---

## Common Mistakes

* **Duplicating OSS bug fixes in the cloud Bug Fixes section.** Runtime fixes go in the Runtime block only.
* **Including internal refactors.** If a PR doesn't change user-facing behavior, skip it.
* **Missing runtime releases.** Check for all releases in the month — there can be multiple patch releases.
* **Marketing language.** Keep descriptions factual: what changed, not how great it is.
* **Wrong link format.** Runtime release links use `https://spiceai.org/releases/{version}` (with the `v` prefix in the version, e.g., `v1.11.4`). Cloud doc links are relative (e.g., `../portal/playground/sql-query-editor.md`).
* **Inserting in the wrong position.** New months go at the top, directly after `# Release Notes`.
* **Using `-` instead of `*` for list items.** The existing `reference/release-notes.md` file uses `*` for all list items — maintain that convention in the output.
