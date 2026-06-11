# Monthly spending caps

A monthly spending cap lets you set a dollar limit on how much a single API key can spend in one month. It is a simple way to control costs if you share a key across apps, run experiments, or want a safety net on top of your account balance.

## Where to set it

1. Open **Usage** in the dashboard.
2. Select the API key you want to limit.
3. Go to the **Settings** tab.
4. Turn on **Monthly Price Cap**, enter an amount, and click **Save cap**.

You can turn the cap off at any time with the same toggle. Your saved amount stays in place if you turn it back on later.

## How your spend is counted

We track spend **per API key**, not across your whole account.

Each time a request finishes and you are charged, that cost is added to the key’s total for the current month. The Settings tab shows how much that key has used so far, for example **$42.00 / $100.00 spent this month**.

The month resets on the **first day of each calendar month** (UTC). After that, counting starts again from zero for that key.

## What happens when you reach the cap

When a key has used its full monthly limit, **new requests with that key are blocked**. Your app will get an error indicating the spending cap was exceeded.

Requests that are **already in progress** are allowed to finish. The cap stops new usage; it does not cut off a response that has already started.

Because of that, if many requests are sent at the same time right as you approach the limit, your total for the month might end up **slightly above** the cap. Once those finish, further requests with that key will be blocked until the next month (or until you raise the cap).

## Things to keep in mind

**Each key has its own cap.** If you use several API keys, set a cap on each one you want to limit. There is no single account-wide cap in the dashboard today.

**You still need balance in your account.** The monthly cap is an extra limit on top of your wallet. If your balance is too low, requests can fail for that reason even when you are under the cap.

**Uncapped keys are unchanged.** Keys with the cap turned off behave as before; only keys with the cap enabled are limited.

**Spending charts match the cap.** The usage and spending views for a key use the same monthly totals as the cap, so what you see on the dashboard is what we use for enforcement.

## Example

You set a **$100** monthly cap on your production API key.

- On March 15, the key has spent **$97** — requests still work.
- By March 28, spend reaches **$100** — new requests with that key are blocked.
- On April 1, the counter resets — the key can spend up to **$100** again in April.

If you need a higher limit mid-month, raise the cap in Settings and click **Save cap**.
