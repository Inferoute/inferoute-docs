# Multi-node networking

On a multi-node cluster, the machines that run one model together share a private network. This page says what those machines can reach on each other, what stays closed, and what is still on you.

## Single-node vs multi-node

A **single-node** cluster is one machine. The engine stays on that machine. Inferoute reaches you over the connection the client opens outbound. Other providers cannot talk to your GPU box.

A **multi-node** cluster is several machines serving one model. For example, **inferoute-cluster1** across two hosts. Those machines join a private network so they can run that model together.

## Who is on that network

Only members of this cluster. Other Inferoute clusters cannot see you. Buyers do not get a path onto it. Inferoute does not use it as a VPN onto your LAN.

{% hint style="warning" %}
Peers can be other people's machines. Joining or accepting a join is like plugging into a shared private network. Only create or join a multi-node cluster with people you intend to share GPUs with. Inferoute cannot make those peers fully trusted.
{% endhint %}

## Required engine ports

{% hint style="warning" %}
On a multi-node cluster, vLLM listens on **8000** and llama.cpp listens on **8081**. Leave `--port` alone. Those two, plus a few other ports in the table below, are how the machines find each other. Pick a different port and the engine stays home on this machine.
{% endhint %}

Setup and **Copy vLLM command** already use those ports. Change them and the other machines walk right past you. These are not the droids you are looking for. 


| Traffic | Why it is open |
| --- | --- |
| TCP **8000** | vLLM HTTP API. Required. Not a default you can change. |
| TCP **8081** | llama.cpp. Required. Not a default you can change. |
| TCP **29501** | Distributed setup between the machines. |
| TCP **32768–60999** | GPU-to-GPU engine traffic. Open **on purpose**. Any process you bind on these ports on all interfaces is reachable by cluster peers. This range is **not** a place to move vLLM or llama.cpp. |
| ICMP | Ping between nodes. |

## What stays closed

Everythong else should remain closed.


## What is still on you

{% hint style="info" %}
The high-port window is wide because the engine needs it. Bind extra services to `127.0.0.1`, not `0.0.0.0`. A debug server or database on a high port on all interfaces **is** reachable by every node in the cluster.
{% endhint %}

This is not a host firewall for your public network card.

Inferoute does not scan your machine. It does not give buyers or other clusters a path in. It does not replace `ufw` or iptables on the public interface.

## Leave or delete

Leaving the cluster, or [deleting it](deleting-clusters.md), drops that machine from the private network. Stop the client on the machine too.

## Related

- [Multi-node clusters](multi-node-clusters.md)
- [Joining a cluster](joining-a-cluster.md)
- [Deleting and managing clusters](deleting-clusters.md)
- [FAQ](../provider-client/faq.md)
