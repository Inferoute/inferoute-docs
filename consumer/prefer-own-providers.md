# Prefer your own providers

If you run your own Inferoute clusters, you can choose whether API requests should try your clusters first before routing to the public marketplace.

## When to enable it

Turn **Prefer own providers** on when you want to:

- Use your own GPU capacity for inference
- Avoid the platform service fee (5% by default) on requests served by your own clusters (same account as provider and consumer — those requests are billed at **$0**)

For example, you might run **inferoute-cluster1** at home for development and want every API call to hit that cluster when it is healthy.

## When to disable it

Turn it off when you want the platform to pick the best marketplace provider by price and performance, even if you also run clusters on the account.

Your clusters can still appear in the normal provider pool when disabled.

## How to change the setting

This control lives on **Account Settings** and only shows when the dashboard role is **Consumer**. Switch to **Consumer** in the sidebar if you do not see it.

1. Open **Account Settings** from the user menu.
2. Stay on the **Profile** tab.
3. Find **Prefer own providers**.
4. Use the toggle to turn the setting on or off.

Changes apply to your next API request.

When the setting is on, Inferoute tries your own clusters first. Those clusters still need to meet any `min_tokens_per_second` you send, but **max price** (profile, key, and request) does not apply to your own clusters — only to marketplace providers.

## Related

- [Using the API](using-the-api.md)
- [Max price limits](max-price-limits.md)
- [Monthly spending caps](monthly-spending-caps.md)
- [Request routing options](request-routing-options.md)
