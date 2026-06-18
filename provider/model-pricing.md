# Model pricing

Set per-model token pricing from the cluster **Models** tab in the dashboard, or via the provider API with your cluster API key.

## Pricing scope (per cluster)

**Each cluster has its own model prices.** A cluster is a `provider` row in the database. Model prices live in `provider_models`, one row per `(cluster, model_name)`.

- Changing pricing on **Home cluster 42** does **not** change pricing on **Charles London Cluster**, even if both run the same model name (e.g. `Qwen/Qwen2.5-7B-Instruct`).
- The account **Models & Prices** tab is a read-only view across all clusters; edit prices in each cluster’s **Models** tab.
- Billing uses the price on the cluster that served the request.

## Storage units

| Layer | Unit | Example |
|-------|------|---------|
| Database (`provider_models.input_price_tokens`) | **$/token** | `0.00004014` |
| Dashboard Models tab & JWT `PATCH` body | **$/1M tokens** | `40.14` |
| Provider API `POST` / `PUT` body | **$/token** | `0.00004014` |

Conversion: `per_1m = per_token × 1_000_000`, `per_token = per_1m ÷ 1_000_000`.

## Dashboard (JWT)

1. Open **Clusters** → select a cluster → **Models** tab.
2. Each registered model shows **Active** or **Inactive** status.
3. Edit input/output price per 1M tokens (shown with `$`) and click **Save**.
4. **Market avg** fills one row from market averages; **Apply market averages to all** fills every row (still requires **Save** per row).
5. If a price is more than 100% away from the market average, a confirmation dialog appears before saving.

Inactive models remain listed so you can adjust pricing for models you previously ran.

### Dashboard API (session cookie / JWT)

- `GET /api/auth/me/provider-models?provider_id=…` — list models for one cluster (optional `include_inactive=true`)
- `GET /api/auth/me/provider-models/market-prices?models=…` — market averages (response in **$/1M**)
- `PATCH /api/auth/me/providers/:provider_id/models/:model_id` — update prices (body in **$/1M**)

## Provider API (cluster API key)

Use your cluster API key (`Authorization: Bearer sk-…`). The key is scoped to **one cluster**; updates only affect that cluster’s models.

- `GET /api/provider/models` — list models and current **per-token** prices
- `POST /api/provider/models` — register a new model with initial **per-token** prices
- `PUT /api/provider/models/:model_id` — update name, service type, and **per-token** prices
- `DELETE /api/provider/models/:model_id` — soft-deactivate the model (`is_active = false`); pricing and history are preserved

Example `PUT` body (per-token):

```json
{
  "model_name": "Qwen/Qwen2.5-7B-Instruct",
  "service_type": "vllm",
  "input_price_tokens": 0.00004,
  "output_price_tokens": 0.00004
}
```

That is **$40 / 1M** on the dashboard (`0.00004 × 1_000_000`).

## How models get initial pricing

- **inferoute-client** registers models at startup via `POST /api/provider/models`, using prices from the model-pricing service (returned as **per-token** after server normalization).
- **Health pushes** can add new models when the client reports them; default prices come from `model_pricing_data`, normalized to per-token before insert.
- Models missing from a health payload are **soft-deactivated** (`is_active = false`), not deleted, so pricing can be edited when they return.
