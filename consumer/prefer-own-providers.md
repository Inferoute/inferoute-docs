# Prefer your own providers

If you run your own Inferoute clusters, you can choose whether API requests should try your clusters first before routing to the public marketplace.

## When to enable it

Turn **Prefer own providers** on when you want to:

- Use your own GPU capacity for inference
- Avoid platform service fees on requests served by your clusters (same account as provider and consumer)

For example, you might run **inferoute-cluster1** at home for development and want every API call to hit that cluster when it is healthy.

## When to disable it

Turn it off when you want the platform to pick the best marketplace provider by price and performance, even if you also run clusters on the account.

Your clusters can still appear in the normal provider pool when disabled.

## How to change the setting

1. Open **Profile** in the dashboard.
2. Find **Prefer own providers**.
3. Use the toggle to turn the setting on or off.

Changes apply to your next API request.

## Related

- [Max price limits](max-price-limits.md)
- [Monthly spending caps](monthly-spending-caps.md)
- [Request routing options](request-routing-options.md)
