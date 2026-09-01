# Max price limits

You control how much you are willing to pay per request by setting **max input** and **max output** prices. Inferoute only routes your requests to providers whose prices are at or below your limits.

## Profile default

Your **profile default** (**Default max price**) is the max price applied to new API keys unless you choose otherwise.

These controls live on **Account Settings** and only show when the dashboard role is **Consumer**. Switch to **Consumer** in the sidebar if you do not see them.

1. Open **Account Settings** from the user menu.
2. Stay on the **Profile** tab.
3. Set **max input price** and **max output price** (shown as **per 1M tokens**).
4. Click **Save**.

For example, you might set **$0.50** input and **$1.00** output per 1M tokens as your account default.

## Per API key

Each API key can have its own max price. This is useful when one app needs cheaper models and another needs higher-quality routing.

1. Open **Usage** and select an API key.
2. Go to the **Settings** tab.
3. Under **Max price per request**, edit **max input price** and **max output price**, then click **Save pricing**.

When you **create** a key, it adopts your profile default. You can optionally set a custom max price for that key during creation.

## Per Request

You can override an API key's max price for one request by sending Inferoute routing options in the API request. Request overrides cannot exceed your profile default.

See [Request routing options](request-routing-options.md).

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

- [Using the API](using-the-api.md)
- [Prefer own providers](prefer-own-providers.md)
- [Request routing options](request-routing-options.md)
- [Monthly spending caps](monthly-spending-caps.md)
