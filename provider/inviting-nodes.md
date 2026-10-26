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

**Nodes** lists each machine: role (**Master** or **Worker**), whether they are in the cluster, and health. A worker shows up there after setup uses the code. Before that, they stay under **Invited**.

The join code works **once**. After setup uses it, that code cannot be used again.

**Invited** lists people you accepted who have not finished setup. It shows the same contact as the request. It does not show the code.

If they lose the code before setup, click **New code**. That kills the old code and shows a new one once, on that page. They can also open it once from **Explore** → **Clusters**. Inferoute emails them to look there. The code is not in the email.

**Revoke** withdraws an invite that has not been used. They can **Request invite** again.

## Remove a worker

On a machine that already joined, **Remove** takes that worker out of the cluster. Their machine can no longer serve it, and they stop earning from it. They can ask to join again. You cannot remove the master from this list. Deleting the cluster is still how you shut the whole thing down.

## Related

- [Joining a cluster](joining-a-cluster.md)
- [Cluster earnings](cluster-earnings.md)
- [Setup wizard](../provider-client/setup.md)
- [Email notifications](../email-notifications.md)
