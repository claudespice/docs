---
description: Manage Spice.ai resources with Terraform
icon: cube
---

# Terraform Provider

The [Spice.ai Terraform Provider](https://registry.terraform.io/providers/spiceai/spiceai/latest) enables infrastructure-as-code management of your Spice.ai Cloud resources.

## Provider Configuration

```hcl
terraform {
  required_providers {
    spiceai = {
      source  = "spiceai/spiceai"
      version = "~> 0.1"
    }
  }
}

provider "spiceai" {
  # OAuth client credentials for authentication.
  # Can also be set via environment variables:
  #   SPICEAI_CLIENT_ID
  #   SPICEAI_CLIENT_SECRET
  client_id     = var.spiceai_client_id
  client_secret = var.spiceai_client_secret

  # Optional: Custom API endpoint (defaults to https://api.spice.ai)
  # api_endpoint = "https://api.spice.ai"
}
```

### Authentication

The provider authenticates using [OAuth 2.0 Client Credentials](./#2-oauth-20-client-credentials). Set credentials via environment variables:

```bash
export SPICEAI_CLIENT_ID="your-client-id"
export SPICEAI_CLIENT_SECRET="your-client-secret"
terraform plan
```

Create OAuth clients in the [Spice.ai Portal](https://spice.ai) under **Settings** → **OAuth Clients**.

## Resources

### spiceai\_app

Manages a Spice.ai application.

```hcl
# Basic app
resource "spiceai_app" "basic" {
  name        = "my-basic-app"
  description = "A basic Spice.ai app"
  visibility  = "private"
  cname       = "us-east-2.spice.cloud"
}

# App with spicepod and runtime configuration
resource "spiceai_app" "full" {
  name        = "my-full-app"
  description = "A fully configured Spice.ai app"
  visibility  = "private"
  cname       = "us-east-2.spice.cloud"

  spicepod = <<-YAML
    version: v1beta1
    kind: Spicepod
    name: my-full-app
    datasets:
      - name: taxi_trips
        from: s3://spiceai-demo-datasets/taxi_trips/2024/
        params:
          file_format: parquet
    models:
      - name: my_model
        from: openai:gpt-4
  YAML

  image_tag             = "latest"
  replicas              = 2
  region                = "us-east-2"
  production_branch     = "main"
  storage_claim_size_gb = 10.0
}
```

**Arguments:**

| Argument                | Type   | Required | Description                                      |
| ----------------------- | ------ | -------- | ------------------------------------------------ |
| `name`                  | string | **Yes**  | App name (min 4 chars, alphanumeric and hyphens) |
| `cname`                 | string | **Yes**  | Region identifier (from `spiceai_regions`)       |
| `description`           | string | No       | App description                                  |
| `visibility`            | string | No       | `public` or `private` (default: `private`)       |
| `spicepod`              | string | No       | Spicepod configuration (YAML or JSON string)     |
| `image_tag`             | string | No       | Spice runtime version tag                        |
| `replicas`              | number | No       | Number of replicas (1-10)                        |
| `region`                | string | No       | AWS region code                                  |
| `production_branch`     | string | No       | Git branch for production deployments            |
| `node_group`            | string | No       | Kubernetes node group                            |
| `storage_claim_size_gb` | number | No       | Persistent volume size in GB                     |

**Attributes:**

| Attribute | Description     |
| --------- | --------------- |
| `id`      | App ID          |
| `api_key` | Primary API key |

You can use JSON instead of YAML for the spicepod:

```hcl
resource "spiceai_app" "json_config" {
  name       = "my-json-app"
  visibility = "public"
  cname      = "us-west-2.spice.cloud"

  spicepod = jsonencode({
    version = "v1beta1"
    kind    = "Spicepod"
    name    = "my-json-app"
    datasets = [
      {
        name = "my_dataset"
        from = "postgres://mydb/table"
      }
    ]
  })
}
```

Or use a template file:

```hcl
resource "spiceai_app" "templated" {
  name  = "my-app"
  cname = "us-east-2.spice.cloud"

  spicepod = templatefile("${path.module}/spicepod.yaml.tftpl", {
    app_name = "my-app"
  })
}
```

### spiceai\_deployment

Creates a deployment for a Spice.ai app.

```hcl
# Basic deployment using app defaults
resource "spiceai_deployment" "basic" {
  app_id = spiceai_app.full.id
}

# Deployment with overrides and git tracking
resource "spiceai_deployment" "production" {
  app_id = spiceai_app.full.id

  image_tag      = "v0.18.0"
  replicas       = 5
  debug          = false
  branch         = "release/v1.0"
  commit_sha     = "abc123def456789"
  commit_message = "Production release v1.0"
}
```

**Arguments:**

| Argument         | Type    | Required | Description                            |
| ---------------- | ------- | -------- | -------------------------------------- |
| `app_id`         | string  | **Yes**  | The app ID to deploy                   |
| `image_tag`      | string  | No       | Override the Spice runtime image tag   |
| `replicas`       | number  | No       | Override the number of replicas (1-10) |
| `debug`          | boolean | No       | Enable debug mode                      |
| `branch`         | string  | No       | Git branch name (for tracking)         |
| `commit_sha`     | string  | No       | Git commit SHA (for tracking)          |
| `commit_message` | string  | No       | Git commit message (for tracking)      |

**Attributes:**

| Attribute | Description                                                        |
| --------- | ------------------------------------------------------------------ |
| `id`      | Deployment ID                                                      |
| `status`  | Deployment status (`queued`, `in_progress`, `succeeded`, `failed`) |

Use `triggers` to automatically create a new deployment when the app configuration changes:

```hcl
resource "spiceai_deployment" "auto" {
  app_id = spiceai_app.full.id

  triggers = {
    spicepod  = spiceai_app.full.spicepod
    image_tag = spiceai_app.full.image_tag
    replicas  = spiceai_app.full.replicas
  }
}
```

### spiceai\_secret

Manages secrets for a Spice.ai app.

```hcl
resource "spiceai_secret" "database_password" {
  app_id = spiceai_app.full.id
  name   = "DATABASE_PASSWORD"
  value  = var.database_password
}

resource "spiceai_secret" "aws_access_key" {
  app_id = spiceai_app.full.id
  name   = "AWS_ACCESS_KEY_ID"
  value  = var.aws_access_key_id
}

resource "spiceai_secret" "aws_secret_key" {
  app_id = spiceai_app.full.id
  name   = "AWS_SECRET_ACCESS_KEY"
  value  = var.aws_secret_access_key
}
```

**Arguments:**

| Argument | Type   | Required | Description              |
| -------- | ------ | -------- | ------------------------ |
| `app_id` | string | **Yes**  | The app ID               |
| `name`   | string | **Yes**  | Secret name              |
| `value`  | string | **Yes**  | Secret value (sensitive) |

**Attributes:**

| Attribute | Description |
| --------- | ----------- |
| `id`      | Secret ID   |

{% hint style="info" %}
After importing a secret, you must set the `value` attribute in your configuration since secret values are not returned by the API (they are masked).
{% endhint %}

### spiceai\_member

Manages organization members.

```hcl
resource "spiceai_member" "developer" {
  username = "johndoe"
  roles    = ["member"]
}

resource "spiceai_member" "admin" {
  username = "janedoe"
  roles    = ["admin", "member"]
}

# Add multiple team members
resource "spiceai_member" "team" {
  for_each = toset(["alice", "bob", "charlie"])

  username = each.key
  roles    = ["member"]
}
```

**Arguments:**

| Argument   | Type      | Required | Description                             |
| ---------- | --------- | -------- | --------------------------------------- |
| `username` | string    | **Yes**  | GitHub username                         |
| `roles`    | string\[] | No       | Roles to assign (default: `["member"]`) |

**Attributes:**

| Attribute | Description |
| --------- | ----------- |
| `user_id` | User ID     |

{% hint style="warning" %}
Organization owners cannot be managed via Terraform. Attempting to modify or delete an owner will result in an error.
{% endhint %}

## Data Sources

### spiceai\_regions

Lists available deployment regions.

```hcl
data "spiceai_regions" "available" {}

output "region_names" {
  value = data.spiceai_regions.available.regions[*].region
}

output "default_region" {
  value = data.spiceai_regions.available.default
}
```

### spiceai\_container\_images

Lists available Spice runtime container images.

```hcl
data "spiceai_container_images" "stable" {
  channel = "stable"
}

output "available_tags" {
  value = data.spiceai_container_images.stable.images[*].tag
}

output "default_tag" {
  value = data.spiceai_container_images.stable.default
}
```

### spiceai\_app

Gets details about an existing app by ID.

```hcl
data "spiceai_app" "existing" {
  id = "12345"
}

output "app_name" {
  value = data.spiceai_app.existing.name
}

output "app_api_key" {
  value     = data.spiceai_app.existing.api_key
  sensitive = true
}
```

### spiceai\_apps

Lists all apps in the organization.

```hcl
data "spiceai_apps" "all" {}

output "app_names" {
  value = [for app in data.spiceai_apps.all.apps : app.name]
}

# Filter apps by visibility
output "private_apps" {
  value = [for app in data.spiceai_apps.all.apps : app.name if app.visibility == "private"]
}

# Map of apps to their configurations
output "apps_config" {
  value = {
    for app in data.spiceai_apps.all.apps : app.name => {
      replicas  = app.replicas
      image_tag = app.image_tag
      region    = app.region
    }
  }
}
```

### spiceai\_members

Lists all organization members.

```hcl
data "spiceai_members" "all" {}

output "member_usernames" {
  value = [for m in data.spiceai_members.all.members : m.username]
}

output "organization_owner" {
  value = [for m in data.spiceai_members.all.members : m.username if m.is_owner][0]
}
```

### spiceai\_secrets

Lists secrets for an app (values are masked).

```hcl
data "spiceai_secrets" "app_secrets" {
  app_id = spiceai_app.full.id
}

output "secret_names" {
  value = [for s in data.spiceai_secrets.app_secrets.secrets : s.name]
}

output "has_database_password" {
  value = contains([for s in data.spiceai_secrets.app_secrets.secrets : s.name], "DATABASE_PASSWORD")
}
```

## Import

Import existing resources into Terraform state:

```bash
# Import an app by ID
terraform import spiceai_app.example 12345

# Import a deployment (app_id/deployment_id)
terraform import spiceai_deployment.example 12345/67890

# Import a secret (app_id/secret_name)
terraform import spiceai_secret.database_password 123/DATABASE_PASSWORD

# Import a member by user ID
terraform import spiceai_member.developer 123

# Import a member when using for_each
terraform import 'spiceai_member.team["alice"]' 789
```

## Complete Example

A full working example that creates an app with secrets, deploys it, and manages team members:

```hcl
terraform {
  required_providers {
    spiceai = {
      source  = "spiceai/spiceai"
      version = "~> 0.1"
    }
  }
}

provider "spiceai" {}

# Look up available regions and images
data "spiceai_regions" "available" {}

data "spiceai_container_images" "stable" {
  channel = "stable"
}

# Create the app
resource "spiceai_app" "production" {
  name        = "my-production-app"
  description = "Production analytics app"
  visibility  = "private"
  cname       = data.spiceai_regions.available.regions[0].cname

  spicepod = <<-YAML
    version: v1beta1
    kind: Spicepod
    name: my-production-app
    datasets:
      - name: taxi_trips
        from: s3://spiceai-demo-datasets/taxi_trips/2024/
        params:
          file_format: parquet
  YAML

  image_tag         = data.spiceai_container_images.stable.default
  replicas          = 2
  production_branch = "main"
}

# Configure secrets
resource "spiceai_secret" "database_password" {
  app_id = spiceai_app.production.id
  name   = "DATABASE_PASSWORD"
  value  = var.database_password
}

resource "spiceai_secret" "aws_credentials" {
  for_each = {
    AWS_ACCESS_KEY_ID     = var.aws_access_key_id
    AWS_SECRET_ACCESS_KEY = var.aws_secret_access_key
  }

  app_id = spiceai_app.production.id
  name   = each.key
  value  = each.value
}

# Deploy
resource "spiceai_deployment" "production" {
  app_id = spiceai_app.production.id

  triggers = {
    spicepod  = spiceai_app.production.spicepod
    image_tag = spiceai_app.production.image_tag
    replicas  = spiceai_app.production.replicas
  }
}

# Add team members
resource "spiceai_member" "team" {
  for_each = toset(["alice", "bob", "charlie"])

  username = each.key
  roles    = ["member"]
}

# Variables
variable "database_password" {
  type      = string
  sensitive = true
}

variable "aws_access_key_id" {
  type      = string
  sensitive = true
}

variable "aws_secret_access_key" {
  type      = string
  sensitive = true
}

# Outputs
output "app_id" {
  value = spiceai_app.production.id
}

output "app_api_key" {
  value     = spiceai_app.production.api_key
  sensitive = true
}

output "deployment_status" {
  value = spiceai_deployment.production.status
}
```

## Resource Mapping

| Terraform Resource         | API Endpoints                              |
| -------------------------- | ------------------------------------------ |
| `spiceai_app`              | `POST/GET/PUT/DELETE /v1/apps/{appId}`     |
| `spiceai_deployment`       | `POST/GET /v1/apps/{appId}/deployments`    |
| `spiceai_secret`           | `GET/POST/DELETE /v1/apps/{appId}/secrets` |
| `spiceai_member`           | `GET/POST/PATCH/DELETE /v1/members`        |
| `spiceai_regions`          | `GET /v1/regions`                          |
| `spiceai_container_images` | `GET /v1/container-images`                 |
| `spiceai_app` (data)       | `GET /v1/apps/{appId}`                     |
| `spiceai_apps` (data)      | `GET /v1/apps`                             |
| `spiceai_members` (data)   | `GET /v1/members`                          |
| `spiceai_secrets` (data)   | `GET /v1/apps/{appId}/secrets`             |

See also:

* [Terraform Provider Registry](https://registry.terraform.io/providers/spiceai/spiceai/latest)
* [Provider Source](https://github.com/spicehq/terraform-provider-spiceai)
* [Management API Overview](./)
