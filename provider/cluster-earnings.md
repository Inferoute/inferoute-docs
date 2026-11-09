# Cluster earnings

Buyers pay for inference. After Inferoute’s fee, the rest is the cluster payout. A **single-node** cluster credits that whole payout to you.

A **multi-node** cluster can split it. You set a **commission** on the **Nodes** tab. That percent comes off first. What remains is split evenly across the **master** (you) and every worker marked **In the cluster**.

## Set the commission

1. Open **My Clusters** under **Inference**.
2. Select the multi-node cluster.
3. Open **Nodes**.
4. Under **Commission**, enter a percent from 0 to 100.
5. Click **Save commission**.

People see this percent on the cluster page (**Explore** → **GPU clusters**, then the cluster) before they request an invite.

If you never save a commission, or no worker is **In the cluster** yet, you keep the whole payout.

Machines marked **Waiting to join** are not in the split. Only workers marked **In the cluster** are.

## Example

For example, a payout of **$100** on **inferoute-cluster1**, with commission **10%** and **two** active workers:

| Who | Share |
| --- | --- |
| Commission (you) | $10 |
| Remainder | $90, split 3 ways (you + 2 workers) |
| You, total | $40 |
| Each worker | $30 |

The Nodes page states the same rule: at 10% with two workers, you get the 10% plus one third of what remains.

At **100%** you keep the payout and workers get nothing. At **0%** the whole payout is split evenly, with no commission taken off first.

## Where it shows up

The share is added to each person’s balance. Open **My Clusters**, then the **Earnings** tab. **Your balance** is that account balance (the same number **Withdraw** uses). **Earned today** on that page is income from clusters you **own**, and it is the cluster total before the split. A worker who does not own a cluster will not see their join income in **Earned today** there. It is still in **Your balance**.

Open the cluster from **My Clusters** for the per-cluster **Earnings** tab:

| Who is looking | **Earned today** and **Total earnings** on that cluster |
| --- | --- |
| You (the owner) | The cluster total, before commission and the worker split |
| A worker | That person’s share only |

A worker does not get their own cluster, a provider API key, or the **Nodes** and **Settings** tabs. They can open **Earnings**, **GPUs**, and **Models**. **Models** is read-only for them.

Changing commission affects **later** payouts. It does not rewrite payouts already credited.

Prices buyers pay are still the prices on the cluster **Models** tab. Commission does not change those prices. See [Model pricing](model-pricing.md).

## Request volume and speed

**Earnings** also shows how much work was served.

1. Open **My Clusters**.
2. Open the **Earnings** tab for every cluster you own. Or open one cluster and its **Earnings** tab for that cluster only.

The charts have a period menu: **today**, **last week**, **last month**, and **last year**. The page opens on **last week**. These windows use Inferoute’s clock.

| Menu | What it covers |
| --- | --- |
| **today** | The current day |
| **last week** | The current calendar week, Monday through Sunday. This is the week you are in, not the previous seven days. |
| **last month** | The last 30 days, including today |
| **last year** | The last 12 months, including this month |

| Card | Meaning |
| --- | --- |
| **Requests today** | Completed requests since the start of the current day |
| **Requests ·** the period you picked | Completed requests in that window. For example, **Requests · last week**. |
| **Tokens in / out** | Input tokens in that window. The line under the number is output tokens, and it ends with **out**. |
| **Avg speed** | Average output speed in that window, in tok/s. The line under it is average latency (milliseconds, or seconds once it reaches 1s). A dash means there were no completed requests. |

The **Requests** chart is that same completed-request count across the window. On a cluster, **Total earnings** is the money chart for the window.

**Recent requests** lists the latest 25. It includes requests that are still in progress. An empty table says **No requests yet.**

| Column | Meaning |
| --- | --- |
| **Time** | When the request was recorded, in your browser’s timezone |
| **Model** | Model id. For example, `Qwen/Qwen2.5-7B-Instruct`. |
| **In** / **Out** | Input and output tokens |
| **Latency** | End-to-end time |
| **Speed** | Output tokens per second |
| **Earned** | Owner or worker amount. See the table below. |
| **Status** | **completed**, **pending**, or **settling** |

**settling** means the payout is still being paid.

On a cluster, **Earnings by Model** does not follow the chart menu. **today**, **this week**, and **this month** are money. **this week** is the current calendar week. **requests**, **avg tok/s**, and **latency** cover every completed request for that model. An empty table says **No models registered for this cluster.**

| Who is looking | Counts, tokens, speed, and **Earned** |
| --- | --- |
| You (the owner) | The cluster total, before commission and the worker split |
| A worker | The requests that paid you. **Earned** is your share. |

A worker who does not own a cluster still will not see that join income on **My Clusters** → **Earnings**. Open the cluster **Earnings** tab.

## Related

- [Multi-node clusters](multi-node-clusters.md)
- [Inviting nodes](inviting-nodes.md)
- [Joining a cluster](joining-a-cluster.md)
