---
description: Publish app to https://spicerack.org
icon: globe
---

# Public Apps

Public Spice Apps can be forked, added as a dependency or connected to using Spice OSS Spice.ai connector.

### Making the app public

{% hint style="info" %}
The app must be connected to a public GitHub repository to be made public.\
Check out how to connect app to the repository - [connect GitHub](apps/connect-github.md).
{% endhint %}

To make your app public, go to your app settings and click **Make public**.

After that, the app will be visible to all users at `https://spice.ai/<org-name>/<app-name>` and searchable at [https://spicerack.org](https://spicerack.org).

### On the registry

A public app is indexed on [SpiceRack](https://spicerack.org), the package registry for Spicepods. Its package page sits at `https://spicerack.org/<org-name>/<app-name>` and lists the datasets, models, and dependencies declared in the app's Spicepod manifest.

Other users install the published Spicepod in one of three ways:

```bash
spice add <org-name>/<app-name>
```

```bash
spice connect <org-name>/<app-name>
```

```yaml
dependencies:
  - <org-name>/<app-name>
```

See [SpiceRack Registry](spicerack.md) for browsing, search, and the registry API.
