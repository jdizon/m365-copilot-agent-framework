# Microsoft Graph App Registration — Setup Guide

**Applies to:** Phase 0 Foundations  
**Audience:** IT administrator performing the initial setup  
**Outcome:** An Entra ID App Registration with the correct Graph API permissions, ready to connect Copilot Studio and Power Automate to the customer's Microsoft 365 tenant.

---

## Why this is required

Copilot Studio event triggers and Power Automate connectors authenticate using **maker credentials by default**. For production-grade deployments — especially where a dedicated service account is preferred, or where the implementation account differs from the agent maker — an App Registration provides a stable, auditable identity for the agent's access to Microsoft Graph.

> **Platform constraint (Feb 2026):** Copilot Studio event triggers run under maker credentials only — not end-user credentials. Confirm the maker account's access scope before publishing. This App Registration configures what that account (or the registered app) can access.

---

## Prerequisites

- Global Administrator or Application Administrator role in Entra ID
- Access to the Microsoft Entra admin centre (`entra.microsoft.com`)
- Confirmation of which M365 services are in scope (see permissions table below)
- Admin consent rights (required to grant tenant-wide permissions)

---

## Step 1 — Create the App Registration

1. Sign in to **Microsoft Entra admin centre** (`entra.microsoft.com`) with an account that has Application Administrator or Global Administrator rights.
2. Navigate to **Identity → Applications → App registrations**.
3. Click **New registration**.
4. Configure:
   - **Name:** `[CustomerName]-CopilotAgent` (or similar — choose a name that identifies the deployment)
   - **Supported account types:** Accounts in this organizational directory only (single tenant)
   - **Redirect URI:** Leave blank for now (add later if using delegated interactive login)
5. Click **Register**.
6. Note the **Application (client) ID** and **Directory (tenant) ID** from the Overview page — you will need both.

---

## Step 2 — Create a client secret

1. In the App Registration, go to **Certificates & secrets → Client secrets**.
2. Click **New client secret**.
3. Set a **description** (e.g., `copilot-agent-prod`) and an **expiry** (12 months recommended — calendar a rotation reminder).
4. Click **Add**.
5. **Copy the secret value immediately** — it will not be shown again.

> **Security:** Store the client secret in a secrets vault (Azure Key Vault, or your organisation's equivalent). Do not store it in plain text in any document or flow variable.

---

## Step 3 — Add API permissions

Navigate to **API permissions → Add a permission → Microsoft Graph**.

### Delegated permissions (minimum for Phase 0–3)

| Permission | Scope | Required for |
|---|---|---|
| `Mail.Read` | Delegated | Read inbox signals |
| `Mail.ReadWrite` | Delegated | Move emails to folders (Phase 3) |
| `MailboxSettings.Read` | Delegated | Read mailbox timezone/locale context |
| `Sites.ReadWrite.All` | Delegated | Read/write SharePoint project registry |
| `Tasks.ReadWrite` | Delegated | Create and update Planner tasks |
| `TeamsMember.Read.All` | Delegated | Identify Teams channel members for alerts |
| `ChannelMessage.Send` | Delegated | Post urgent alerts to Teams channels |
| `Notes.ReadWrite.All` | Delegated | Add OneNote notes (Phase 5+) |
| `User.Read` | Delegated | Basic profile — required by most connectors |
| `offline_access` | Delegated | Maintain token without re-prompting (required for Power Automate) |

### Application permissions (if using service account / unattended flows)

Add `Mail.Read`, `Mail.ReadWrite`, `Sites.ReadWrite.All`, and `Tasks.ReadWrite` as **Application** permissions if the agent will operate without interactive sign-in.

> **Minimum viable set for Phase 2 (classify + log, no moves):** `Mail.Read`, `Sites.ReadWrite.All`, `User.Read`, `offline_access`.  
> Add `Mail.ReadWrite`, `Tasks.ReadWrite`, `ChannelMessage.Send` before enabling Phase 3.

---

## Step 4 — Grant admin consent

After adding all required permissions:

1. Click **Grant admin consent for [Tenant Name]**.
2. Confirm when prompted.
3. Verify all permissions show **Granted for [Tenant Name]** with a green tick.

> **Note:** Admin consent is required for all Application permissions and for any Delegated permissions that require tenant-wide approval (e.g. `Sites.ReadWrite.All`).

---

## Step 5 — Configure a redirect URI (for delegated interactive login)

If the agent maker will authenticate interactively (e.g. via Power Automate's OAuth connection):

1. Go to **Authentication → Add a platform → Web**.
2. Add the Power Automate OAuth callback URI:  
   `https://global.consent.azure-apim.net/redirect`
3. Under **Implicit grant and hybrid flows**, enable **Access tokens** if required by the connector.
4. Click **Save**.

> For Power Automate custom connectors, also add:  
> `https://global.consent.azure-apim.net/redirect/microsoftgraph`

---

## Step 6 — Record credentials for use in Power Automate

Store the following in your secrets vault and document the vault path in your Phase 0 exit gate:

| Credential | Where to find it | Notes |
|---|---|---|
| Tenant ID | App Registration → Overview | Also called Directory ID |
| Application (Client) ID | App Registration → Overview | Used in all connector configs |
| Client Secret | Created in Step 2 | Rotate before expiry date |
| Secret expiry date | Certificates & secrets tab | Calendar a reminder 30 days before |

---

## Step 7 — Connect Power Automate

1. In **Power Automate**, open your solution.
2. Go to **Connections → New connection → Microsoft Graph HTTP** (or the relevant connector).
3. Select **OAuth 2.0 / Service Principal** and enter the Tenant ID, Client ID, and Client Secret.
4. Test the connection — confirm a successful response.
5. Assign the connection to the flows that will use it.

---

## Step 8 — Connect Copilot Studio knowledge sources

For SharePoint knowledge sources (Phase 2+):

1. In Copilot Studio, open the agent.
2. Go to **Knowledge → Add knowledge source → SharePoint**.
3. Authenticate with the maker account (or the service principal if configured for that connector).
4. Select the SharePoint site and lists: `ProjectRegistry`, `AgentAuditLog`.
5. Test that the agent can retrieve project data before proceeding.

---

## Step 9 — Verify permissions end-to-end

Run the following checks before marking Phase 0 complete:

- [ ] Power Automate flow can read at least one email from the target mailbox
- [ ] Power Automate flow can write a test row to the `AgentAuditLog` SharePoint list
- [ ] Copilot Studio agent can query the `ProjectRegistry` knowledge source
- [ ] (Phase 3) Power Automate flow can move a test email to an Outlook folder
- [ ] (Phase 3) Power Automate flow can create a Planner task
- [ ] (Phase 3) Power Automate flow can post a message to the Teams alert channel

---

## Step 10 — Governance

### Least-privilege principles

- Do not add permissions beyond what the current phase requires. Add scope incrementally as phases are enabled.
- Prefer **Delegated** permissions over **Application** permissions where interactive sign-in is feasible — Delegated permissions are scoped to the signed-in user's access, not the entire tenant.
- Remove Application permissions for any service not yet in scope.

### Secret rotation

- Set a calendar reminder 30 days before the client secret expires.
- To rotate: create a new secret in the App Registration, update the value in your secrets vault and all Power Automate connections, then delete the old secret.
- Notify the delivery team when a rotation is due — Power Automate connections that reference the old secret will break immediately on expiry.

### Compliance checklist

- [ ] App Registration is named clearly and documented in the organisation's app inventory
- [ ] Client secret is stored in a secrets vault — not in a document, email, or flow variable
- [ ] Privacy notice has been updated to disclose that an automated agent processes signals (Australian Privacy Act APP 3/6 requirement)
- [ ] M365 data residency region confirmed in Admin Centre (relevant for GDPR / Australian Privacy Act / other local legislation)
- [ ] App Registration access is reviewed at least annually

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Power Automate connection fails with 401 | Client secret expired or wrong | Check expiry in Entra, regenerate if needed |
| Admin consent button greyed out | Insufficient role | Requires Global Admin or Application Admin |
| SharePoint knowledge source not returning data | Sites.ReadWrite.All not granted | Check admin consent status in API permissions |
| Teams alert flow fails with 403 | ChannelMessage.Send not consented | Add permission + re-grant admin consent |
| Trigger not firing | Generative orchestration not enabled, or maker credentials issue | Enable generative orchestration; check maker account permissions |
| Token expiry issues in Power Automate | offline_access permission missing | Add offline_access and re-authenticate the connection |
| Application permission not visible | Delegated vs Application confusion | Switch to Application tab when adding the permission |

---

## Phase 0 exit gate — Graph access

Before proceeding to Phase 1, confirm all of the following are true:

- [ ] App Registration created with documented client ID and tenant ID
- [ ] Client secret created, stored in vault, rotation date calendared
- [ ] Admin consent granted for all required permissions
- [ ] Power Automate connection tested: read mail ✅, write SharePoint ✅
- [ ] Copilot Studio knowledge source connected to SharePoint ✅
- [ ] Permissions are documented and scoped to current phase only
- [ ] Secret stored in vault — not in any document
- [ ] Privacy notice updated
- [ ] M365 data residency confirmed
