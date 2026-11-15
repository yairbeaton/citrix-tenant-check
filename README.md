# Citrix Tenant Check

A small Postman collection that connects to a Citrix Cloud tenant and reports whether any Cloud Connector has a pending update.

It runs three requests:

| # | Request | What it does |
|---|---------|--------------|
| 1 | Get Token | Authenticates with a Secure Client and stores the bearer token for the next requests |
| 2 | Resource Locations | Lists the tenant's resource locations |
| 3 | Connectors | Lists every Cloud Connector and compares its current version with the expected version |

## Prerequisites

- [Postman](https://www.postman.com/downloads/)
- A Citrix Cloud **Secure Client**: in Citrix Cloud go to **Identity and Access Management > API Access**, create a client, and copy the **Client ID**, **Client Secret** and **Customer ID**. The client inherits the permissions of the administrator who creates it.

## Setup

1. In Postman, click **Import** and select `Citrix-Tenant-Check.postman_collection.json`.
2. Open the collection's **Variables** tab and fill in:

   | Variable | Value |
   |----------|-------|
   | `customerId` | Your Citrix Cloud Customer ID |
   | `clientId` | Secure Client ID |
   | `clientSecret` | Secure Client secret |
   | `apiHost` | API host for your tenant's region (see below) |

3. Save the collection.

`access_token` and `rlMap` are filled in automatically during the run; leave them as they are.

### API host by region

| Region | `apiHost` |
|--------|-----------|
| European Union (default) | `api-eu.cloud.com` |
| United States | `api-us.cloud.com` |
| Asia Pacific South | `api-ap-s.cloud.com` |
| Japan | `api.citrixcloud.jp` |

## Running the check

Run the whole collection (**Run collection**) so the requests execute in order. Request 1 must run first; the token is valid for about an hour.

## Reading the result

The run results contain one line per connector and a final summary:

```
PENDING UPDATE - cc01.example.com (Main Site): 4.458.0.22492 -> 4.497.0.35733
Up to date - cc02.example.com (Main Site): 4.497.0.35733
RESULT: 1 of 2 connectors have a pending update
```

- A connector is reported as **pending** when its `currentVersion` differs from its `expectedVersion`.
- `[upgrades disabled]` and `[in maintenance now]` are appended when those flags are set on the connector.
- A pending update is reported as a passed test, so the run stays green either way. Read the `RESULT:` line for the outcome.
- When request 3 is sent on its own (not through the Runner), the summary line also appears in the response's **Visualize** tab.

## Limitations

- The check shows *that* an update is pending, not *when* it will run. The scheduled date and time appear only in the Citrix Cloud console, under **Resource Locations**.
- Citrix Cloud notifications are not included; reading them with a Secure Client returned `401 Unauthorized`.

## Security

Once you fill in the variables, the collection holds your client secret. Do not commit or share an exported copy that contains real values. The file in this repository ships with empty credentials.

## API references

- [Get started with Citrix Cloud APIs](https://developer-docs.citrix.com/en-us/citrix-cloud/citrix-cloud-api-overview/citrix-cloud-api-walkthrough)
- [Managing Connectors](https://developer-docs.citrix.com/en-us/citrix-cloud/citrix-cloud-connectors/getting-started)
- [Connector updates](https://docs.citrix.com/en-us/citrix-cloud/citrix-cloud-resource-locations/connector-updates.html)
