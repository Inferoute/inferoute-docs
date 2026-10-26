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

Open **My Clusters** (under **Inference**), select the cluster, and open **Nodes**.

- **Intended model** — the catalog model setup installs. Save it before anyone runs setup. The page treats it as a reminder: it does not stop other models the client might register later.
- **VRAM per node** — the minimum VRAM you expect on every machine. The page uses this, with the model, to say how many nodes you need.
- **Commission** — your cut of each payout. See [Cluster earnings](cluster-earnings.md).

**Explore** → **GPU clusters** lists multi-node clusters as cards. Open a cluster to see the model, the VRAM minimum, open slots (for example `1/2`), and your commission. People request an invite on that page, not on the list. Share the page link so someone can read the requirements before they ask to join.

## How the machines connect

Setup connects each machine into the cluster. Inferoute still reaches you through the outbound connection the client already opens. You do not open firewall ports, and you do not paste a network key yourself. A join code or the master API key is enough.

The master has to finish setup before workers can be useful. On **Nodes**, a machine stays **Waiting to join** until that setup completes. **Test cluster** stays off until the master is healthy.

## Start every machine together

A multi-node model does not start one machine at a time. vLLM waits until **every** planned node is up. Every machine uses the OS and engine shown on the cluster page. Do this:

1. Deploy the cluster and save the intended model and VRAM per node on **Nodes**. That sets how many machines the model needs.
2. Copy the cluster link from **Nodes** and send it to the people you want. They can also find it under **Explore** → **GPU clusters**.
3. They open that page and click **Request invite**. You accept on **Nodes**. They run setup with the join code.
4. When enough machines are in the cluster, tell everyone to start the client (and the engine) **at the same time**.
5. All nodes have **60 minutes** to come up together. The clock starts when the first engine begins waiting for the others.

If a machine is late, the engines stop. Start them again, together. The **2 hour** wait during setup is only the first model download. It is not this 60 minute window.

For example, **inferoute-cluster1** needs 4 nodes. You start it, three people join, and you message them when the fourth slot is filled. Everyone starts within that hour.

## Related

- [Inviting nodes](inviting-nodes.md)
- [Joining a cluster](joining-a-cluster.md)
- [Cluster earnings](cluster-earnings.md)
- [Sign up and create a cluster](../getting-started/signup.md)
