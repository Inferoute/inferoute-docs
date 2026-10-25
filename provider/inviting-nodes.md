# Inviting nodes

You invite workers to a multi-node cluster you own. They ask to join. You accept or reject. Accepting creates a **join code** they paste into setup. For what a master and a worker are, see [Multi-node clusters](multi-node-clusters.md).

## Create the cluster

1. Open **Clusters** under **Inference**.
2. Click **Deploy a Cluster**.
3. On **Check Compatibility**, set **Cluster type** to **Multi-node**.
4. Finish the wizard and copy the provider API key. That key is the **master**. It is shown once.

Run `inferoute-client setup` on your machine with that key. Setup sees a multi-node cluster and installs this machine as the master. It uses the **intended model** you saved on **Nodes**. If no model is saved, setup stops and tells you to pick one in the dashboard.

## Review a request

When someone asks to join, you get an email (if their account email is verified). The email does **not** contain a join code. It names them and their preferred contact, and links to the **Nodes** tab.

1. Open the cluster.
2. Open **Nodes**.
3. Under **Join requests**, read the username and how to reach them (email, Telegram, or Discord).
4. Click **Accept** or **Reject**.

**Accept** shows a join code once, on that page:

`join-` followed by a long hex string.

Copy it before you leave. Inferoute does not show it again, and it is not emailed. You can also send it yourself using the contact on the request. The person who asked can open the same code once from **Explore** → **Clusters**. See [Joining a cluster](joining-a-cluster.md).

**Reject** declines the request. They can ask again.

They can **Cancel request** while it is still pending. Then it disappears from **Join requests**.

## After they join

**Nodes** lists each machine: role (**Master** or **Worker**), whether they are in the cluster, and health. A worker stays **Waiting to join** until setup on their machine finishes.

The join code works **once**. After setup uses it, that code cannot be used again.

If they lose the code before setup, neither of you can open it again. **Accept** does not mint a replacement. **Request invite** stays closed while that accepted code is still unused. Copy the code as soon as you accept, and send it to them if they do not copy it from **Show join code**.

## Related

- [Joining a cluster](joining-a-cluster.md)
- [Cluster earnings](cluster-earnings.md)
- [Setup wizard](../provider-client/setup.md)
- [Email notifications](../email-notifications.md)
