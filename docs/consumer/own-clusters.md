# Using your own clusters

If you run Inferoute clusters on the same account you use for API requests, your clusters join the normal provider pool for your requests. You don't need to turn anything on.

## How your clusters are billed

Requests served by your own clusters are billed at **$0**. You pay no token cost and no platform service fee, and your cluster earns nothing for them. Inferoute still records the usage.

## How your clusters are picked

Inferoute ranks your clusters alongside marketplace providers, using each cluster's **listed price** and speed. Your clusters are not tried first.

| Rule | Marketplace providers | Your own clusters |
|---|---|---|
| **Max price** (profile, key, and request) | Applies | Does not apply |
| `min_tokens_per_second` | Applies | Applies |
| Must be healthy, verified, and connected | Yes | Yes |

For example, say you list `Qwen/Qwen2.5-7B-Instruct` on **inferoute-cluster1** at $2.00 per 1M output tokens, and your max price is $1.00 per 1M. **inferoute-cluster1** still qualifies for your requests, because max price doesn't apply to your own clusters. It is ranked by its $2.00 listed price, though, so a cheaper marketplace provider may be picked first.

!!! info

    There is no setting that forces your requests onto your own clusters. To make your cluster more likely to win, list it at a lower price on the **Models** tab.

## Related

- [Using the API](using-the-api.md)
- [Max price limits](max-price-limits.md)
- [Request routing options](request-routing-options.md)
- [Model pricing](../provider/model-pricing.md)
