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

Setup joins each machine to a private network so the nodes can reach each other. The program is **inferscale**. You do not paste a network key, and you do not open firewall ports. A join code or the master API key is enough. Inferoute still reaches the master through the outbound connection the client already opens.

On Linux, setup may ask for your password. It prints:

```text
inferscale needs root to create inferscale0
```

That creates the network device. Enter the password for `sudo`. If `sudo` is not installed, setup stops with `inferscale needs root to create inferscale0, and sudo is not installed`.

While it joins, the wizard prints **Downloading inferscale**, **Starting inferscale**, and **Joining the tailnet**. When this machine is on the network, it prints **Inferscale address:** and an address.

A worker does not serve HTTP. Its engine joins the master. Only the master answers `/v1/models` and takes inference.

On a multi-node cluster, vLLM must listen on **8000** and llama.cpp must listen on **8081**. Do not change `--port`. Other machines in the cluster can reach only those ports. A different port stays on this machine. See [Multi-node networking](multi-node-networking.md).

The master has to finish setup before workers can be useful. On **Nodes**, a machine stays **Waiting to join** until that setup completes. **Test cluster** stays off until the master is healthy.

If the wizard still asks you to pick an engine and a model, it did not see this multi-node cluster. Look for `could not read cluster mode` and run setup again. Do not start vLLM from that run.

These messages mean this machine is not on the network, and the one-shot network key is gone:

- `inferscale is not connected and the one-shot key was already used`
- `inferscale up: one-shot key was rejected`

On the **master**, run `inferoute-client setup` again. Setup requests a new key.

On a **worker**, the join code is already spent. Ask the owner for a **New code**. A later `inferoute-client setup` prints `This machine is already in the cluster.` and does not ask again. Pass the new code yourself:

```bash
inferoute-client setup --api-key "join-…"
```

If the owner removed the machine, the client says `the owner removed this machine from the cluster. Re-run setup and paste a new join code`. The next setup asks for the code.

If setup stops with `inferscaled did not answer`, read `~/.local/state/inferoute/log/inferscaled.log`, then run setup again.

## Start the master first

Start the master first. Once it is up, workers have **60 minutes** to join. vLLM waits that long for the last worker. Every machine uses the OS and engine shown on the cluster page. Do this:

1. Deploy the cluster and save the intended model and VRAM per node on **Nodes**. That sets how many machines the model needs.
2. Copy the cluster link from **Nodes** and send it to the people you want. They can also find it under **Explore** → **GPU clusters**.
3. They open that page and click **Request invite**. You accept on **Nodes**. They run setup with the join code.
4. Setup does not start the engine. It prints the command for that machine. The command uses the full path to the engine, so you do not have to activate a virtual environment first. **Copy vLLM command** on **Nodes** is different: it starts with `vllm`. If the shell says `vllm: command not found`, setup installed it in `~/.venv-vllm`. Activate that folder, or replace `vllm` with `~/.venv-vllm/bin/vllm`.
5. Run the command on the **master** first. Wait until it is up and waiting for the other machines.
6. Each worker then runs the command setup printed for them. They have **60 minutes** from when the master is up.
7. Then each person starts the client (`inferoute-client`). If the engine is not running, the client prints the command again. It does not start the engine for you.

If a worker misses that window, the engines stop. Start the master again, then the workers. The **2 hour** wait during setup is only the first model download. It is not this 60 minute window.

If vLLM crashes on any machine, stop it on every machine in the cluster and start again in that order. See [vLLM crashes at startup with "Could not find nvcc"](../provider-client/faq.md#vllm-crashes-at-startup-with-could-not-find-nvcc).

For example, **inferoute-cluster1** needs 4 nodes. You start the master. When it is up, tell the three workers. They join within that hour. The cluster is still not live. Wait for the steps below.

## Wait for the cluster to come live

Workers joining does not make the cluster ready. vLLM still loads the model across every machine. That can take a while after the last worker connects. **Test cluster** on **Nodes** stays off until the master is healthy.

On the **master**, watch the vLLM log. Loading is finished when you see:

```
INFO:     Application startup complete.
```

Then check the vLLM models API on the master. vLLM must be on **8000**. llama.cpp must be on **8081**. Do not change `--port`. It has to list at least one model:

```bash
curl http://127.0.0.1:8000/v1/models
```

An empty list is not ready. vLLM can answer that URL while weights are still loading.

Keep `inferoute-client` running on each machine. The client status shows **(awaiting health sync)** until the next health report. That report runs about every **3 minutes**. After it, the model name replaces that line and the cluster can take traffic. See [Health monitoring](../provider-client/how-it-works.md#health-monitoring).

## Read status on the machine

Open **http://localhost:8080/** or look at the terminal where `inferoute-client` is running. **CLUSTER STATUS** shows:

| Label | What it means |
| --- | --- |
| **Cluster** | `master`, `worker`, or `single` |
| **Engine** | Terminal only. Master: `not started`, `waiting for workers (0/3)`, `loading`, or `ready`. Worker: `running` or `not started`. |
| **Nodes** | This machine plus other machines that are online, out of the cluster size. For example, `1/4` means only this machine is on the network. |
| **Tailnet IP** | This machine's address on the private network. |
| **Peers** | How many other machines are online. For example, `3 online`. |

**Nodes** includes this machine. **waiting for workers** counts the other machines only. For example, a 4-node cluster with only the master up shows **Nodes** `1/4` and **Engine** `waiting for workers (0/3)`. The client also prints `Waiting for workers to join (0/3).`

Starting the client before vLLM prints the saved command and `Start it in another terminal. This client keeps running.` When the worker engine is up, the client prints `Worker is running. It joins the master and does not serve HTTP.` On the master, once vLLM is serving, it prints `vLLM is ready at` the local URL.

The next client start reconnects inferscale when that address is offline.

## Related

- [Multi-node networking](multi-node-networking.md)
- [Inviting nodes](inviting-nodes.md)
- [Joining a cluster](joining-a-cluster.md)
- [Cluster earnings](cluster-earnings.md)
- [Sign up and create a cluster](../getting-started/signup.md)
