# Deleting and managing clusters

This page covers **dashboard** actions for a provider cluster: API keys, pause/resume, and deletion. For installing and running the provider client on your machine, see the [Provider client](provider-client/introduction.md) docs.

## Where to find cluster settings

1. Open **Clusters** in the dashboard.
2. Select the cluster you want to manage.
3. Go to the **Settings** tab.

Settings replaces the old **Actions** tab. API key management, pause/resume, and delete all live here.

## API keys

Each cluster has one or more **provider API keys**. The client on your machine uses this key to authenticate with Inferoute.

On Settings you can:

- **View** the key label and a masked suffix (last characters of the lookup key).
- **Re-generate** the key — the old secret stops working immediately; copy the new key into your client config.
- **Create** a key if the cluster has none (for example after all keys were revoked).

The full API key is only shown **once** when it is created or re-generated. Store it somewhere safe before closing the modal.

Re-generating or creating a key does **not** delete the cluster. It only rotates credentials for that cluster.

## Pause and resume

**Pause cluster** stops new inference orders from being routed to your cluster. Your provider client can keep running, but the orchestrator will not send new work while the cluster is paused.

**Resume cluster** turns routing back on (subject to health and availability checks).

Use pause when you want to go idle temporarily without removing the cluster from your account.

## Delete cluster

**Delete cluster** is in the **Danger zone** on Settings. It is meant when you want to remove a cluster from your account and stop inference immediately.

### What happens when you delete

When you confirm deletion:

1. **The cluster is removed from your dashboard** — it no longer appears under **Clusters**.
2. **Inference stops** — the cluster is paused, marked unavailable, and health is set to red so the orchestrator will not route requests to it.
3. **All active API keys for that cluster are revoked** — your provider client can no longer authenticate with those keys.
4. **Historical data is kept** — past earnings and transactions stay in your account history for billing and audit. The cluster no longer appears in the dashboard, but we retain records tied to past payouts.

### What delete does not do

- It does **not** uninstall the provider client on your machine — stop the service locally if you no longer need it.
- It does **not** remove Cloudflare tunnels or DNS automatically from your Cloudflare account; clean those up separately if you created them outside the platform flow.
- It does **not** delete your Inferoute user account or other clusters.

### After deletion

You **cannot** pause, resume, view earnings for, or create new API keys against a deleted cluster. To offer inference again, deploy a **new** cluster (for example via **Deploy a cluster**).

Deletion is **not reversible** from the dashboard today. If you deleted a cluster by mistake, contact support.

## Pause vs delete

| | Pause | Delete |
| --- | --- | --- |
| Stops new inference | Yes | Yes |
| Cluster stays in dashboard | Yes | No |
| API keys keep working | Yes | No (revoked) |
| Reversible from Settings | Yes (resume) | No |
| Earnings history | Kept | Kept |

Use **pause** for temporary downtime. Use **delete** when you are done with that cluster for good.

## Related

- [Cluster location](cluster-location.md)
- [Model pricing](model-pricing.md)
- [Provider client introduction](../provider-client/introduction.md)
