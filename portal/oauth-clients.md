---
icon: key-skeleton-left-right
---

# OAuth Clients

OAuth clients allow external applications to access the Spice.ai API on behalf of your organization using standard OAuth 2.0 flows.

## Creating an OAuth Client

1. Navigate to your organization's **Settings** and select **OAuth Clients**.
2. Click **Create Client**.
3. Fill in the required fields:
   - **Name**: A descriptive name for the client (e.g. "CI Pipeline" or "Internal Dashboard").
   - **Description** (optional): A brief description of what the client is used for.
   - **Scopes**: Select the permissions the client needs. Follow the principle of least privilege—only grant scopes the client requires.
4. Click **Create**.

After creation, a **Client ID** and **Client Secret** are displayed. Copy and securely store the client secret immediately—it cannot be retrieved later.

{% hint style="warning" %}
Treat the client secret like a password. Do not commit it to source control or share it in plaintext.
{% endhint %}

## Managing OAuth Clients

Organization administrators can view and delete OAuth clients from the **OAuth Clients** settings page.

- **View clients**: See all registered clients, their scopes, and creation dates.
- **Delete a client**: Revokes access for all tokens issued to that client. This action cannot be undone.

## Available Scopes

Scopes control what actions an OAuth client can perform. When creating a client, select only the scopes required for its intended use.

## Security Best Practices

- Rotate client secrets periodically.
- Audit active clients regularly and remove any that are no longer needed.
- Use descriptive names so clients are easy to identify and manage.
