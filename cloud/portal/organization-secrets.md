---
icon: key
---

# Organization Secrets

**Organization Secrets** are encrypted key-value pairs defined once for an organization and shared with the apps that need them. A shared credential — a warehouse password, a model provider API key — is stored in one place instead of being copied into every app that uses it.

An organization secret reaches an app only after it is linked to that app. Storing a secret at the organization level does not expose it to every app in the organization.

Once a secret is saved, its value cannot be read back through Spice Cloud. The portal lists only the secret name and when it was created or last updated.

### Access by role

The **Secrets** section is available to organization owners, admins, and members. Viewers cannot view or manage secrets, and the section is hidden from them.

| Role   | Organization secrets            |
| ------ | ------------------------------- |
| Owner  | Create, update, delete          |
| Admin  | Create, update, delete          |
| Member | Create, update, delete          |
| Viewer | No access                       |

Roles are assigned per organization member. [Learn more about organizations.](organizations.md)

### Create an organization secret

1. Select the organization from the application selector.
2. Navigate to the **Settings** tab and select the **Secrets** section.
3. Fill the **Name** and **Value** fields and click **Add**.

Secret names must start with a letter or an underscore and may contain only letters, numbers, and underscores, for example `GITHUB_TOKEN`.

### Update or delete a secret

Editing a secret replaces its value. The name cannot be changed — to rename a secret, create a new one and delete the old one.

Deleting a secret requires confirmation, and removes it from every app it is linked to.

### Link a secret to an app

1. Select the app.
2. Navigate to the **Settings** tab and select the **Secrets** section.
3. Under **Linked Organization Secrets**, select each secret the app should receive.
4. Click **Save linked secrets**.

Linked secrets are referenced in the Spicepod configuration exactly like app secrets, as `${secrets:<SECRET_NAME>}`:

```yaml
models:
  - from: openai:gpt-4o
    name: gpt-4o
    params:
      openai_api_key: ${secrets:OPENAI_API_KEY}
```

{% hint style="info" %}
When an app secret and a linked organization secret have the same name, the app secret takes precedence at runtime.
{% endhint %}

To apply a change to linked secrets, initiate a new Spicepod deployment. [Learn more about deployments.](app-spicepod/deployments.md)
