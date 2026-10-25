# Multi-node clusters

A **single-node** cluster is one machine. You deploy it, install the client, and that machine serves inference.

A **multi-node** cluster is several machines that serve **one** model together. You own the cluster. Your machine is the **master**. Other people join as **workers** with a join code you approve. Inferoute sends traffic to the master. Workers help that master run the model.

Use multi-node when one GPU is not enough for the model you want to serve. For example, a large `Qwen/Qwen2.5-7B-Instruct` build can need more VRAM than one card has. The cluster page tells you how many nodes that model needs once you set the model and the VRAM on each node.

{% hint style="warning" %}
Workers run part of the same workload as your master. Only accept people you trust. A join code lets that person’s machine into the cluster.
{% endhint %}

Multi-node setup runs on **Linux** with an NVIDIA GPU. A single-node cluster can still be Linux, macOS, or Windows. See [Software and hardware requirements](../getting-started/requirements.md).

## Master and worker

| | Master | Worker |
| --- | --- | --- |
| Who | You, the cluster owner | Someone you accept |
| What they install with | The cluster’s provider API key | A join code (`join-…`) |
| What they run | The model Inferoute dials | The same model, as part of that cluster |
| Their own cluster | This one | No. They do not get a separate cluster |

The master has to be healthy before you can run **Test cluster** on the **Nodes** tab.

## What you set on the cluster

Open **Clusters** (under **Inference**), select the cluster, and open **Nodes**.

- **Intended model** — the catalog model setup installs. Save it before anyone runs setup. The page treats it as a reminder: it does not stop other models the client might register later.
- **VRAM per node** — the minimum VRAM you expect on every machine. The page uses this, with the model, to say how many nodes you need.
- **Commission** — your cut of each payout. See [Cluster earnings](cluster-earnings.md).

**Explore** → **Clusters** shows the model, the VRAM minimum, how many slots are left, and your commission before someone asks to join.

## How the machines connect

Setup connects each machine into the cluster. Inferoute still reaches you through the outbound connection the client already opens. You do not open firewall ports, and you do not paste a network key yourself. A join code or the master API key is enough.

The master has to finish setup before workers can be useful. On **Nodes**, a machine stays **Waiting to join** until that setup completes. **Test cluster** stays off until the master is healthy.

## Related

- [Inviting nodes](inviting-nodes.md)
- [Joining a cluster](joining-a-cluster.md)
- [Cluster earnings](cluster-earnings.md)
- [Sign up and create a cluster](../getting-started/signup.md)
