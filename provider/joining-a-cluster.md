# Joining a cluster

You can ask to join someone else’s multi-node cluster. You do not create that cluster, and you do not get your own provider API key for it. You get a **join code** after the owner accepts. Paste that code into setup. Your machine becomes a **worker**.

You need an Inferoute account with a verified email. Multi-node workers run on **Linux** with an NVIDIA GPU.

## Set how the owner can reach you

The owner sees one contact method on your request. It defaults to your account email.

1. Open **Account Settings**.
2. Stay on **Profile**.
3. Under **How a cluster owner can reach you**, add Telegram and Discord if you want.
4. Choose **Preferred contact**. Telegram and Discord stay off until that handle is filled in.
5. Click **Save contact**.

This is not a second login. Inferoute still emails your account address.

## Ask to join

1. In the sidebar, open **Explore**, then **Clusters**.
2. This list is only multi-node clusters. It is not the **Clusters** list under **Inference** (those are clusters you own).
3. Check the model, VRAM minimum, slots left, and the owner’s commission.
4. Click **Request invite**.

You and the owner each get an email. The email does not include a join code. The button changes to **Cancel request**.

**Cancel request** withdraws a pending ask. The owner no longer sees it. You can request again.

You cannot request to join a cluster you own.

## After the owner decides

| Result | What you see | Email |
| --- | --- | --- |
| Still waiting | **Cancel request** | “We sent your request…” |
| Accepted | **Show join code** and **Cancel invite** | Tells you to open **Explore** → **Clusters**. The code is not in the email. |
| Rejected | **Request invite** again | The owner declined. |

**Show join code** displays the code **once**. Copy it. A second visit does not show it.

**Cancel invite** withdraws an accepted code that setup has not used yet. You can **Request invite** again.

If you close the page before you copy the code, ask the owner for a **New code**. That replaces the old one. The new code shows once for the owner and once for you under **Show join code**. The old code stops working.

After setup has used the code, this cluster shows **In this cluster**. Only the owner can remove you. You can request again after that.

## Run setup

On the worker machine:

```bash
inferoute-client setup
```

When setup asks for a provider API key, paste the join code (`join-…`). Setup does not ask you to pick a model. It uses the model the owner saved, installs the engine, and joins that cluster as a worker.

The code is spent when setup succeeds. Running setup again with the same code fails.

If the owner has not saved an intended model, setup stops and tells you to wait until they pick one.

## Related

- [Multi-node clusters](multi-node-clusters.md)
- [Inviting nodes](inviting-nodes.md)
- [Cluster earnings](cluster-earnings.md)
- [Setup wizard](../provider-client/setup.md)
