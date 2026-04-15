---
description: Configuring Spice.ai runtime for your Spice application
icon: gear
---

# Runtime

Navigate to `Settings` -> `Runtime` to configure the runtime settings for your Spice application.

### Runtime Version

The runtime version determines the Spice.ai Open Source version for your Spice application. Each new deployment automatically adopts the latest stable version of the Spice runtime to ensure access to the most recent features and optimizations.

### Runtime Region

The runtime region specifies the geographic location of the data center hosting your Spice application. Region selection optimizes latency, compliance, and performance based on your business needs.

* **Availability**: Region selection is exclusive to Enterprise plan customers.
* **Supported Regions**:
  * **North America**:
    * **US East (N. Virginia)** - `us-east-1` (AWS)
    * **US West (Oregon)** - `us-west-2` (AWS)

### Compute

Compute settings define the resource allocation for your Spice application, balancing performance and cost.

**Standard Compute Instances**:

* **Developer**: 2 CPU / 4 GB
* **Pro for Teams**: 4 CPU / 8 GB
* **Enterprise**: Dedicated Instances with multiple high-availability replicas

### Storage

Provides a persistent storage for the runtime to save data acceleration files. Data remains intact across restarts and redeployments.

* **Availability**: Storage is exclusive to Enterprise plan customers.
* **Mount Path**: `/data`.
* **Size**: Configured per request. Contact your account executive to set or update capacity.
