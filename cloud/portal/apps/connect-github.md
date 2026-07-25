---
description: Connect your Spice.ai app to a GitHub repository
icon: link-simple
---

# Connect GitHub

## Prepare the repository

Before connecting:

* **Admin access**: Ensure you have administrative access to the GitHub repository. This level of access is required to [install the Spice.ai GitHub app](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-github-marketplace-for-your-organizations#requirements-to-install-a-github-app-on-an-organization).
* **Matching repository name**: The Spice.ai app and the GitHub repository names **must match**. For example:
  * **Spice.ai app**: [spice.ai/spiceai/demo](https://spice.ai/spiceai/demo)
  * **GitHub repository**: [github.com/spiceai/demo](https://github.com/spiceai/demo)

To quickly set up a new repository, use the [spiceai/spicepod-template](https://github.com/spiceai/spicepod-template) as a starting point:

{% hint style="warning" %}
Make sure to copy app spicepod.yaml contents from **Build** > **Code** and place it in the root of the repository before linking.
{% endhint %}

## Connect

1. Ensure the repository is set up as per instructions above.
2. In the context of the Spice app to connect, navigate to **Settings,** then click the **Connect repository** button.
3. Follow GitHub App installation instructions.

> Ensure that you select all repositories or specifically the repository you intend to connect.

3. Finally, link the repository to your Spice.ai app.
