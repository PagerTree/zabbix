![](images/logo.png?raw=true)
# PagerTree Webhook

## Overview

This guide describes how to integrate your Zabbix installation with [**PagerTree**](https://pagertree.com) using Zabbix’s webhook feature. You'll learn how to import the media type, configure a user, and route alerts to PagerTree for incident management.

Official documentation can be found here: [https://pagertree.com/docs/integration-guides/zabbix](https://pagertree.com/docs/integration-guides/zabbix)

### Why PagerTree + Zabbix

- Forward alerts from Zabbix directly into PagerTree.
- Automatically route alerts to the correct on-call team or engineer.
- Synchronize problem and recovery events with your alert workflow.
- Ensure alerts resolve in PagerTree when problems are cleared in Zabbix.

### How It Works

- **Problem event**: A trigger in Zabbix fires, sending a webhook to PagerTree.
- PagerTree creates or groups an alert accordingly.
- **Recovery event**: When the trigger resolves, Zabbix sends another webhook, and PagerTree resolves the corresponding incident.

### Supported Versions

This integration requires **Zabbix 7.0 or higher**.

---

## Parameters

### Configurable Parameter

This is the only parameter you need to provide:

| Name                               | Value (example)                   | Description                                  |
|------------------------------------|-----------------------------------|----------------------------------------------|
| `.pagertree_integration_endpoint_url` | `https://api.pagertree.com/integration/int_xxxyyyzzz` | Your PagerTree integration endpoint URL. This is provided when you create a Zabbix integration in PagerTree. Configure in Zabbix as the User's **Send To** parameter under Users → Media|


### Built-In Event Macros

The media type template automatically injects Zabbix context values into the webhook payload using built-in macros:

- `{ALERT.SENDTO}` - The PagerTree integration endpoint URL
- `{ALERT.MESSAGE}` – Assgiend by templates
- `{ALERT.SUBJECT}` – Assigned by message templates
- `{EVENT.ID}` – Event ID
- `{EVENT.NSEVERITY}` – Numeric severity (0–5)
- `{EVENT.SOURCE}` - Event source (e.g., Trigger, Discovery, Autoregistration, Internal, Service)
- `{EVENT.TAGS}` – Comma seperated list of tags
- `{EVENT.VALUE}` - Event value (1 = problem, 0 = recovery)
- `{TRIGGER.ID}` - Trigger ID
- `{ZABBIX.URL}` - See [set Zabbis URL macro below](#set-zabbixurl-global-macro)

These values are automatically mapped into the PagerTree request body.

---

## PagerTree Setup

1. Log in to your **PagerTree** account.  
2. Navigate to the **Integrations** section and **create a new Zabbix integration** by selecting the Zabbix logo.
3. Fill in all the required fields and click create.
4. Copy the provided **Endpoint URL** — this will be used as the users `Send To` parameter.

---

## Zabbix Setup

### Set {$ZABBIX.URL} global macro

Go to Administration->General (Macro) and create new macro that points to your Zabbix frontend

`{$ZABBIX.URL}` = https://zx01.zabbix.com

### Import the PagerTree Media Type

- In Zabbix, go to **Administration → Media types**.
- Click **Import**, select the [`media_pagertree.yaml`](media_pagertree.yaml) file, and import it.
- Ensure the media type named **PagerTree** appears in the list and is **enabled**.

### Create a PagerTree User in Zabbix

- In Zabbix, navigate to **Administration → Users** and click **Create user**.
- Name the user (e.g., "PagerTree").
- Under the **Groups** tab, grant read access to all relevant hosts.
- Under the **Media** tab, add:
  - **Type**: PagerTree (the imported media type)
  - **Send to**: Paste your PagerTree integration endpoint URL
  - **Enabled**: Checked
- Save the user.

### 3. Assign the User to Actions

- Use the "PagerTree" user in your action configurations so that both **problem** and **recovery** events are sent to PagerTree.

### 4. Test the Integration

1. Go to **Administration → Media types → PagerTree → Test**.
2. Paste your copied endpoint URL in the `.pagertree_integration_endpoint_url` field.
3. Click **Test** and verify the test alert appears in PagerTree.

---

## Feedback & Support

If you encounter any issues with this media type:

- Report them at **https://support.zabbix.com**
- Or discuss on the **[Zabbix forums](https://www.zabbix.com/forum/)**.

---
