# Model pricing

Each **cluster** has its own model list and prices. Changing prices on one cluster does **not** change prices on your other clusters, even when they run the same model name.

For example, if **inferoute-cluster1** and **inferoute-cluster2** both serve `Qwen/Qwen2.5-7B-Instruct`, you can set different input and output prices on each cluster. Buyers see the price for the cluster that actually runs their request.

## Where to edit prices

1. Open **Clusters** in the dashboard.
2. Select a cluster (for example **inferoute-cluster1**).
3. Open the **Models** tab.
4. Edit **Input / 1M** and **Output / 1M**.
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

- **Input / 1M** — prompt / input tokens  
- **Output / 1M** — completion / output tokens  

That matches how **Models & Prices** displays prices elsewhere in the dashboard.

## Apply market averages

**Apply market averages to all** fills every row on that cluster’s Models tab with the **Market avg** values. It does not save automatically — review each row and click **Save**, or save rows individually after applying.

If your prices differ a lot from market averages, you may see a confirmation before saving a single row.

## Deploy wizard

When you deploy a new cluster, the wizard does **not** set model prices. You set them **after** the cluster is running, on that cluster’s **Models** tab (once health reports which models are available).

## Provider API (automation)

If you automate with a **provider API key**, prices are still **per cluster** (one key per cluster). Call the platform at `https://core.inferoute.com` — not the [local REST API](../provider-client/rest-api.md) on the machine.

Authenticate with `Authorization: Bearer <provider API key>`. The HTTP API uses **per-token** decimals (`input_price_tokens`, `output_price_tokens`), not $/1M:

| Dashboard (Models tab) | Provider API (`POST` / `PUT /api/provider/models`) |
|------------------------|-------------------------------------|
| $0.15 / 1M input | `0.00000015` per input token |
| $0.60 / 1M output | `0.0000006` per output token |

Divide your $/1M value by **1,000,000** to get the API value. Updating a model via the API on **inferoute-cluster1** does not change pricing on **inferoute-cluster2**.

```bash
curl -X PUT "https://core.inferoute.com/api/provider/models/{model_id}" \
  -H "Authorization: Bearer YOUR_PROVIDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model_name": "Qwen/Qwen2.5-7B-Instruct",
    "service_type": "vllm",
    "input_price_tokens": 0.00000015,
    "output_price_tokens": 0.0000006
  }'
```

Also: `POST /api/provider/models` (add), `GET /api/provider/models` (list). Full request/response shapes are in the GitBook **API Reference**.

## Related

- [Deleting and managing clusters](deleting-clusters.md)
- [Provider client FAQ](../provider-client/faq.md)
