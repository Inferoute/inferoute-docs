# Cluster location

The dashboard shows a **country flag** next to each cluster on **Clusters** so you can see where your GPU is running at a glance.

## What you see

On **Clusters**, each row shows:

- Cluster name
- **Country flag** (when location is known)
- GPU count and other status

If location is not available yet, you see **—** instead of a flag. That usually means the client has not connected yet.

## How location is determined

You do **not** configure location in the client or dashboard.

Inferoute detects location automatically from the connection your provider client opens:

1. Your provider client connects to Inferoute.
2. Inferoute reads the public IP of that connection.
3. The platform resolves that IP to a **country code** and stores it on your cluster.

Location updates when:

- The cluster first connects
- The client reconnects from a different network (for example after you move the machine)

## What you need to do

Nothing extra. Keep the provider client running, as described in [How it works](../provider-client/how-it-works.md).

## Related

- [Deleting and managing clusters](deleting-clusters.md)
- [Cluster location](cluster-location.md)
- [Provider client: connection](../provider-client/how-it-works.md#connection-to-inferoute)
