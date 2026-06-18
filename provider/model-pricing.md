# Model pricing

Each **cluster** has its own model list and prices. Changing prices on one cluster does **not** change prices on your other clusters, even when they run the same model name.

For example, if **inferoute-cluster1** and **inferoute-cluster2** both serve `Qwen/Qwen2.5-7B-Instruct`, you can set different input and output prices on each cluster. Buyers see the price for the cluster that actually runs their request.

## Where to edit prices

1. Open **Clusters** in the dashboard.
2. Select a cluster (for example **inferoute-cluster1**).
3. Open the **Models** tab.
4. Edit **Input price** and **Output price** (shown as **$/1M tokens**).
5. Click **Save** on that row.

The **Market avg** column is a reference from public pricing data. It does not change your prices until you edit and save, or use **Apply market averages to all**.

## Pricing is per cluster, not global

| What you do | Effect |
|-------------|--------|
| Change prices on **inferoute-cluster1** | Only that cluster’s listed prices change |
| Same model name on **inferoute-cluster2** | Unchanged unless you edit that cluster too |
| **Models & Prices** (account-wide tab) | Read-only overview across clusters — not where you edit |

Use the cluster **Models** tab when you want different prices per machine, region, or GPU tier. Set the same numbers on each cluster if you want uniform pricing everywhere.

## Units in the dashboard

All editable prices in the cluster **Models** tab are **per 1 million tokens** ($/1M):

- **Input price** — prompt / input tokens  
- **Output price** — completion / output tokens  

That matches how **Models & Prices** displays prices elsewhere in the dashboard.

## Apply market averages

**Apply market averages to all** fills every row on that cluster’s Models tab with the **Market avg** values. It does not save automatically — review each row and click **Save**, or save rows individually after applying.

If your prices differ a lot from market averages, you may see a confirmation before saving a single row.

## Deploy wizard

When you deploy a new cluster, step 3 is **GPU selection** only. You set model prices **after** the cluster is running, on that cluster’s **Models** tab (once health reports which models are available).

## Provider API (automation)

If you automate with a **provider API key**, prices are still **per cluster** (one key per cluster). The HTTP API uses **per-token** decimals, not $/1M:

| Dashboard (Models tab) | Provider API (`POST` / `PUT` model) |
|------------------------|-------------------------------------|
| $0.15 / 1M input | `0.00000015` per input token |
| $0.60 / 1M output | `0.0000006` per output token |

Divide your $/1M value by **1,000,000** to get the API value. Updating a model via the API on **inferoute-cluster1** does not change pricing on **inferoute-cluster2**.

See [Local REST API](../provider-client/rest-api.md) for endpoints.

## Related

- [Deleting and managing clusters](deleting-clusters.md)
- [Provider client FAQ](../provider-client/faq.md)
