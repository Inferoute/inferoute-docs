# Model pricing

Set per-model token pricing from the cluster **Models** tab in the dashboard, or via the provider API.

## Dashboard

1. Open **Clusters** → select a cluster → **Models** tab.
2. Each registered model shows **Active** or **Inactive** status.
3. Edit input/output price per 1M tokens and click **Save**.
4. **Market avg** fills one row from market averages; **Apply market averages to all** fills every row.
5. If a price is more than 100% away from the market average, a confirmation dialog appears before saving.

Inactive models remain listed so you can adjust pricing for models you previously ran.

## Provider API

Use your cluster API key (`Authorization: Bearer sk-...`):

- `PUT /api/provider/models/:model_id` — update prices (per-token in request body)
- `POST /api/provider/models` — register a new model with initial prices
- `GET /api/provider/models` — list models

The inferoute-client registers models automatically at startup and on health checks with default market pricing.
