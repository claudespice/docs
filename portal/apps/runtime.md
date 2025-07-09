---
description: Configuring Spice.ai runtime for your Spice application
icon: gear
---

# Runtime

<figure><img src="../../.gitbook/assets/CleanShot 2025-07-10 at 07.06.34@2x.png" alt=""><figcaption></figcaption></figure>

Navigate to `Settings` -> `Runtime` to configure the runtime settings for your Spice application.

### Runtime Version

The runtime version determines the Spice.ai Open Source version for your Spice application. Each new deployment automatically adopts the latest stable version of the Spice runtime to ensure access to the most recent features and optimizations.

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-08 at 21.07.03.png" alt=""><figcaption></figcaption></figure>

### Runtime Region

The runtime region specifies the geographic location of the data center hosting your Spice application. Region selection optimizes latency, compliance, and performance based on your business needs.

* **Availability**: Region selection is exclusive to Enterprise plan customers.
* **Supported Regions**:
  * **North America**:
    * **US East (Pittsburgh)** - `us-east-2` (Teraswitch, Current)
    * **US West (Oregon)** - `us-west-2` (AWS)
    * **US East (N. Virginia)** - `us-east` (Azure)

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-08 at 21.14.08.png" alt=""><figcaption></figcaption></figure>

### Compute

Compute settings define the resource allocation for your Spice application, balancing performance and cost.

**Standard Compute Instances**:

* **Developer**: 2 CPU / 4 GB
* **Pro for Teams**: 4 CPU / 8 GB
* **Enterprise**: Dedicated Instances with multiple high-availability replicas

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-08 at 21.15.52.png" alt=""><figcaption></figcaption></figure>
