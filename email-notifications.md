# Email notifications

You can choose which emails Inferoute sends you. Preferences live in **Account Settings** and apply to the email on your account.

## Where to manage them

1. Open the dashboard and go to **Account Settings** (from the user menu).
2. Open the **Notifications** tab.
3. Turn each notification type **on** or **off**. Changes save as soon as you toggle them (except **Low Balance Warning**, which also needs a threshold — see below).

Emails go to the address shown on that page (your account email). The address must be **verified** for notification emails to be delivered.

## Notification types

| Type | What you get | Who it is for |
| --- | --- | --- |
| **Platform updates** | Product news, features, and announcements | Everyone who opts in |
| **Low balance warning** | An email when your account balance drops below an amount you choose | Anyone spending from a wallet balance |
| **Cluster paused** | An email when one of your GPU clusters is paused | Providers |
| **Cluster outage** | An email when a cluster is no longer healthy or available | Providers |

Turn **Platform updates** off if you only want operational alerts (or turn everything off if you prefer no email).

## Low balance

When **Low Balance Warning** is on, we check your available wallet balance about once an hour. If it is below the threshold you set, we email you so you can top up before requests start failing.

1. Turn **Low Balance Warning** on.
2. Enter an amount between **$1** and **$1000**.
3. Click **Save**.

Until you save a threshold, we do not send low balance emails for your account.

This is separate from [monthly spending caps](consumer/monthly-spending-caps.md). Caps limit spend on a single API key; a low balance means the account wallet itself is low.

## Cluster paused

If you (or someone with access) **pauses** a cluster in the dashboard, we can email you. The message includes a link to that cluster’s page under **Clusters** so you can resume or check settings.

Pausing stops new inference from being routed to the cluster. See [Deleting and managing clusters](provider/deleting-clusters.md).

## Cluster outage

If a cluster stops reporting as healthy (for example it goes offline or misses health checks) while it is **not** paused, we can email you. The email links straight to that cluster in the dashboard.

This is meant for clusters that should be running but are no longer available to receive work. A paused cluster does not trigger an outage email.

## Things to keep in mind

- **Toggles are per account.** Preferences apply to your user, not per API key or per cluster.
- **Verified email only.** If your email is not verified, notification emails will not be sent until you verify it.
- **Operational alerts can be quiet.** We avoid spamming the same alert repeatedly; you may not get another email for the same issue until the situation clears and happens again (or, for some cluster alerts, until some time has passed).
- **Platform updates are optional.** They are off by default. Turn them on only if you want product and announcement mail.

## Related

- [Deleting and managing clusters](provider/deleting-clusters.md) — pause, resume, and delete
- [Monthly spending caps](consumer/monthly-spending-caps.md) — per-key spend limits
- [Overview](overview.md)
