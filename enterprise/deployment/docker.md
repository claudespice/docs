---
description: Deploy Spice.ai Enterprise using Docker container images.
icon: docker
---

# Docker

Enterprise container images are published to GitHub Container Registry (GHCR) and AWS Marketplace ECR.

## Images

| Image                                                | Description                                   |
| ---------------------------------------------------- | --------------------------------------------- |
| `ghcr.io/spicehq/spiceai-enterprise:latest-models`   | Default distribution with AI/ML model support |
| `ghcr.io/spicehq/spiceai-enterprise:latest`          | Data-only distribution                        |
| `ghcr.io/spicehq/spiceai-enterprise:latest-cuda`     | CUDA GPU-accelerated distribution             |
| `ghcr.io/spicehq/spiceai-enterprise:latest-nas`      | NAS distribution (SMB + NFS connectors)       |
| `ghcr.io/spicehq/spiceai-enterprise:latest-jemalloc` | jemalloc memory allocator variant             |
| `ghcr.io/spicehq/spiceai-enterprise:latest-mimalloc` | mimalloc memory allocator variant             |
| `ghcr.io/spicehq/spiceai-enterprise:latest-sysalloc` | System (glibc) memory allocator variant       |

## Pull an Image

```bash
docker pull ghcr.io/spicehq/spiceai-enterprise:latest-models
```

## Run

```bash
docker run -p 8090:8090 -p 50051:50051 -p 9090:9090 \
  ghcr.io/spicehq/spiceai-enterprise:latest-models \
  --http 0.0.0.0:8090 \
  --metrics 0.0.0.0:9090 \
  --flight 0.0.0.0:50051
```

### With a Spicepod Configuration

Mount a Spicepod YAML file into the container:

```bash
docker run -p 8090:8090 -p 50051:50051 -p 9090:9090 \
  -v $(pwd)/spicepod.yaml:/app/spicepod.yaml \
  ghcr.io/spicehq/spiceai-enterprise:latest-models
```

## Exposed Ports

| Port    | Protocol | Description         |
| ------- | -------- | ------------------- |
| `8090`  | HTTP     | HTTP/SQL API        |
| `50051` | gRPC     | Apache Arrow Flight |
| `9090`  | HTTP     | Prometheus metrics  |

## Image Details

Enterprise images are built `FROM scratch` with a minimal filesystem containing:

- The `spiced` binary
- CA certificates
- Timezone database
- Required shared libraries

The container runs as UID 65534 (nobody) for security.

### Environment Variables

| Variable        | Default                              |
| --------------- | ------------------------------------ |
| `HOME`          | `/app`                               |
| `HF_HOME`       | `/.cache/huggingface`                |
| `SSL_CERT_FILE` | `/etc/ssl/certs/ca-certificates.crt` |
| `SSL_CERT_DIR`  | `/etc/ssl/certs`                     |
