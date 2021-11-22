---
description: This page describes how to run a ParaTime client node on the Oasis Network.
---

# Run a ParaTime Client Node

{% hint style="info" %}
These instructions are for setting up a _ParaTime client_ node which only observes ParaTime activity and can submit transactions. If you want to run a _ParaTime_ node instead, see the [instructions for running a ParaTime node](run-a-paratime-node.md). Similarly, if you want to run a _validator_ or a _non-validator_ node instead, see the [instructions for running a validator node](run-validator.md) or [instructions for running a non-validator node](run-non-validator.md).
{% endhint %}

{% hint style="success" %}
If you are looking for some concrete ParaTimes that you can run, see [the list of ParaTimes and their parameters](../../contribute-to-the-network/run-a-paratime-node.md).
{% endhint %}

{% hint style="success" %}
Oasis Core refers to ParaTimes as runtimes internally, so all configuration options will have runtime in their name.
{% endhint %}

This guide will cover setting up your ParaTime client node for the Oasis Network. This guide assumes some basic knowledge on the use of command line tools.

## Prerequisites

Before following this guide, make sure you've followed the [Prerequisites](../prerequisites/) and [Run a Non-validator Node](run-non-validator.md) sections and have:

* Oasis Node binary installed and configured on your system.
* The chosen top-level `/node/` working directory prepared. In addition to `etc` and `data` directories, also prepare the following directories:
  * `bin`: This will store binaries needed by Oasis Node for running the ParaTimes.
  * `runtimes`: This will store the ParaTime binaries and their corresponding signatures (if they are running in a Trusted Execution Environment).

{% hint style="success" %}
Feel free to name your working directory as you wish, e.g. `/srv/oasis/`.

Just make sure to use the correct working directory path in the instructions below.
{% endhint %}

* Genesis file copied to `/node/etc/genesis.json`.

{% hint style="success" %}
Reading the rest of the [ParaTime node setup instructions](run-a-paratime-node.md) may also be useful.
{% endhint %}

{% hint style="info" %}
To speed up bootstraping your new node, we recommend [copying node's state from your existing node](../advanced/copy-state-from-one-node-to-the-other.md) or [syncing it using state sync](../advanced/sync-node-using-state-sync.md).
{% endhint %}

{% hint style="success" %}
Running a ParaTime client node doesn't require registering an entity or its nodes.

It also doesn't require having any stake.
{% endhint %}

{% hint style="success" %}
Running a client node for a ParaTime that runs in a Trusted Execution Environment (TEE) doesn't require having the same  TEE available on the ParaTime client node.

For example, running a ParaTime client node for an SGX-enabled ParaTime like Cipher doesn't require having SGX on the ParaTime client node.
{% endhint %}

### The ParaTime Identifier and Binary

In order to run a ParaTime node you need to obtain the following pieces of information first, both of these need to come from a trusted source:

* ****[**The ParaTime Identifier**](https://docs.oasis.dev/oasis-core/high-level-components/index-1/identifiers) is a 256-bit unique identifier of a ParaTime on the Oasis Network. It provides a unique identity to the ParaTime and together with the [genesis document's hash](https://docs.oasis.dev/oasis-core/high-level-components/index/genesis#genesis-documents-hash) serves as a domain separation context for ParaTime transaction and cryptographic commitments.\
  \
  It is usually represented in hexadecimal form, for example:\
  `8000000000000000000000000000000000000000000000000000000000000000`\

*   **The ParaTime Binary** contains the executable code that implements the ParaTime itself. It is executed in a sandboxed environment by Oasis Node and its format depends on whether the ParaTime is running in a Trusted Execution Environment (TEE) or not.\


    For ParaTime client nodes, one _does not_ need to run the SGXS binary, even for ParaTimes running in a TEE. ParaTime client node always runs the regular Linux executable (an [ELF binary](https://en.wikipedia.org/wiki/Executable\_and\_Linkable\_Format), usually without an extension).

{% hint style="danger" %}
Like the genesis document, make sure you obtain these from a trusted source.
{% endhint %}

{% hint style="warning" %}
#### **Compiling the ParaTime Binary from Source Code**

In case you decide to build the ParaTime binary from source yourself, make sure that you follow our [guidelines for deterministic compilation](https://docs.oasis.dev/oasis-sdk/guide/reproducing) to ensure that you receive the exact same binary.
{% endhint %}

### Install ParaTime Binary

For each ParaTime, you need to obtain its binary and install it to `runtimes` subdirectory of your node's working directory.

### Install Bubblewrap Sandbox (at least version 0.3.3)

ParaTime client nodes execute ParaTime binaries inside a sandboxed environment provided by [Bubblewrap](https://github.com/containers/bubblewrap). In order to install it, please follow these instructions, depending on your distribution:

{% tabs %}
{% tab title="Ubuntu 18.10+" %}
```bash
sudo apt install bubblewrap
```
{% endtab %}

{% tab title="Fedora" %}
```bash
sudo dnf install bubblewrap
```
{% endtab %}

{% tab title="Other Distributions" %}
On other systems, you can download the latest [source release provided by the Bubblewrap project](https://github.com/containers/bubblewrap/releases) and build it yourself.

Make sure you have the necessary development tools installed on your system and the `libcap` development headers. On Ubuntu, you can install them with:

```bash
sudo apt install build-essential libcap-dev
```

After obtaining the Bubblewrap source tarball, e.g. [bubblewrap-0.4.1.tar.xz](https://github.com/containers/bubblewrap/releases/download/v0.4.1/bubblewrap-0.4.1.tar.xz), run:

```bash
tar -xf bubblewrap-0.4.1.tar.xz
cd bubblewrap-0.4.1
./configure --prefix=/usr
make
sudo make install
```

{% hint style="warning" %}
Note that Oasis Node expects Bubblewrap to be installed under `/usr/bin/bwrap` by default.
{% endhint %}
{% endtab %}
{% endtabs %}

Ensure you have a new enough version by running:

```
bwrap --version
```

{% hint style="warning" %}
Ubuntu 18.04 LTS (and earlier) provide overly-old `bubblewrap`. Follow _Other Distributions_ section on those systems.&#x20;
{% endhint %}

## Configuration

In order to configure the ParaTime client node, create the `/node/etc/config.yml` file with the following content:

```yaml
datadir: /node/data

log:
  level:
    default: info
    tendermint: info
    tendermint/context: error
  format: JSON

genesis:
  file: /node/etc/genesis.json

consensus:
  tendermint:    
    p2p:
      # List of seed nodes to connect to.
      # NOTE: You can add additional seed nodes to this list if you want.
      seed:
        - "{{ seed_node_address }}"

runtime:
  supported:
    # List of ParaTimes that the node should support.
    - "{{ runtime_id }}"

  paths:
    # Paths to ParaTime non-SGX binaries for all of the supported ParaTimes.
    "{{ runtime_id }}": {{ non-sgx_runtime_bin_path }}

worker:

  storage:
    enabled: true
    checkpoint_sync:
      disabled: true
  
  p2p:
    # External P2P configuration.
    enabled: true

```

Before using this configuration you should collect the following information to replace  the  variables present in the configuration file:.

* `{{ seed_node_address }}`: The seed node address in the form `ID@IP:port`.
  * You can find the current Oasis Seed Node address in the [Network Parameters](../../oasis-network/network-parameters.md).
* `{{ runtime_id }}`: The [ParaTime identifier](run-a-paratime-node.md#the-paratime-identifier-and-binary).
  * You can find the current Oasis-supported ParaTime identifiers in the [Network Paramers](../../foundation/testnet/#paratimes).
* `{{ non-sgx_runtime_bin_path }}`: Path to the [ParaTime's non-SGX binary](run-a-paratime-node.md#the-paratime-identifier-and-binary) of the form `/node/runtimes/foo-paratime`.

## Starting the Oasis Node

You can start the node by running the following command:

```bash
oasis-node --config /node/etc/config.yml
```

## Checking Node Status

To ensure that your node is properly connected with the network, you can run the following command after the node has started:

```bash
oasis-node control status -a unix:/node/data/internal.sock
```
