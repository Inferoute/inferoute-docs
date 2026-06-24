# Max price limits

You control how much you are willing to pay per request by setting **max input** and **max output** prices. Inferoute only routes your requests to providers whose prices are at or below your limits.

## Profile default

Your **profile default** is the max price applied to new API keys unless you choose otherwise.

1. Open **Profile** in the dashboard.
2. Set **Max input price** and **Max output price** (shown as **$/1M tokens**).
3. Click **Save**.

For example, you might set **$0.50** input and **$1.00** output per 1M tokens as your account default.

## Per API key

Each API key can have its own max price. This is useful when one app needs cheaper models and another needs higher-quality routing.

1. Open **Usage** and select an API key.
2. Go to the **Settings** tab.
3. Edit **Max input price** and **Max output price**, then click **Save pricing**.

When you **create** a key, it adopts your profile default. You can optionally set a custom max price for that key during creation.

## Higher than your profile default

A key cannot be saved with a max price above your profile default unless you also update the profile.

If you try to save a higher key price, you will be asked whether to **update your profile default** to match. If you decline, the key price is not changed.

## Units

Dashboard prices are **per 1 million tokens** ($/1M), the same style as provider **Models** pricing:

| Field | Meaning |
|-------|---------|
| **Max input price** | Highest input (prompt) price you accept |
| **Max output price** | Highest output (completion) price you accept |

## Related

- [Monthly spending caps](monthly-spending-caps.md)
