---
description: Spice.ai Enterprise on AWS Marketplace.
icon: aws
---

# AWS Marketplace

Spice.ai Enterprise is available on AWS Marketplace with two licensing models:

| Model    | ECR Repository                     | Description                  |
| -------- | ---------------------------------- | ---------------------------- |
| **BYOL** | `spice-ai/spiceai-enterprise-byol` | Bring Your Own License       |
| **Plan** | `spice-ai/spiceai-enterprise-plan` | AWS Marketplace subscription |

## Container Images

Enterprise images are published to the AWS Marketplace ECR registry and support both `amd64` and `arm64` architectures:

```
709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:<tag>
709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan:<tag>
```

## Helm Chart

A Marketplace-specific Helm chart (`spiceai-enterprise`) is provided with the ECR image pre-configured:

```bash
helm install spiceai-enterprise ./chart \
  --set spicepod.name=my-app
```

## ECR Pull Access

AWS Marketplace grants the following ECR permissions to subscribed accounts:

- `ecr:GetDownloadUrlForLayer`
- `ecr:BatchGetImage`
- `ecr:BatchCheckLayerAvailability`
- `ecr:DescribeImages`
