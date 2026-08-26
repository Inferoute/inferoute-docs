# Sign up and create a cluster

Create an Inferoute account, deploy a provider cluster, and copy the **provider API key**. You need that key for [Installation](installation.md).

## Sign up

1. Open [www.inferoute.com](https://www.inferoute.com).
2. Click **Sign Up**.
3. Enter your email, then a password (at least **8** characters), or continue with **Google** or **GitHub**.
4. Confirm your email if you are sent to **Verify your email**.

## Choose Provider

On first login you see **Welcome Onboard!** and **Please, choose your role:**

- **Provider** — earn by offering GPU inference
- **Consumer** — call models as a buyer
- **I want both** — if you will do both

Choose **Provider** (or **I want both**). You can switch profiles later from the dashboard.

## Deploy a cluster

1. Open **Clusters**.
2. Click **Deploy a Cluster**.

The wizard has four steps:

### 1. Requirements

Confirm your machine matches [Software and hardware requirements](requirements.md).

### 2. Software

Select **vLLM** or **Ollama**, then install that engine if you have not already. Check the confirmation that it is installed before you continue.

### 3. GPU

Pick the GPU closest to what you have (for example **RTX 4090**). If the label is wrong, Inferoute still detects the real hardware later from the client.

You set model prices **after** the client is running, on the cluster **Models** tab — see [Model pricing](../provider/model-pricing.md).

### 4. Client (cluster name and API key)

1. Set **Cluster name**. For example, `inferoute-cluster1`.
2. Set **API key label** (defaults to `{cluster name} API key`).
3. Click **generate API key**.

The full key is shown **once**. Copy it before you leave the page. Store it somewhere safe; you will paste it into the install command next.

The wizard also shows an install command with your key filled in. You can copy that, or follow [Installation](installation.md) and substitute the key yourself.

## Copy the key later from Settings

If you already have a cluster and need the key again:

1. Open **Clusters** and select the cluster (for example **inferoute-cluster1**).
2. Open the **Settings** tab.

You can **Create** a key if the cluster has none, or **Re-generate** an existing key. Re-generating immediately invalidates the old secret — update the client config before you expect inference to work.

The full secret is only shown when you create or re-generate it. Settings otherwise shows a masked suffix.

More on keys, pause, and delete: [Deleting and managing clusters](../provider/deleting-clusters.md).

## Next

Install the client with that key: [Installation](installation.md).
