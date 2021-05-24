# Hardware Requirements

{% hint style="info" %}
The hardware requirements listed on this page are the suggested **minimum requirements**. It might be possible to configure a system with less resources, but you run the risk of being underprovisioned and thereby prone to loss of stake.
{% endhint %}

The Oasis Network is composed of multiple classes of nodes that participate in different committees. The majority of committees have common system configurations for the participant nodes.

## Consensus Nodes <a id="suggested-minimum-configurations"></a>

To run a non-validator or a validator consensus node, your system should meet the following minimum system requirements:

* 2.0 GHz x86-64 CPU

{% hint style="warning" %}
The CPU must have [AES-NI](https://en.wikipedia.org/wiki/AES_instruction_set) support.
{% endhint %}

* 4 GB ECC RAM

{% hint style="info" %}
Initial state sync is more resource intensive than ordinary node operation once the node is caught up with the network. Once the node is synced, it can operate with 2 GB of RAM.
{% endhint %}

* 100+ GB High Speed Storage

{% hint style="info" %}
The network accumulates state over time. The speed at which the state grows depends on the network's usage.

For example, the Mainnet accumulated over 80 GB of state in 5+ months between [Mainnet launch](../../mainnet/previous-upgrades/mainnet-upgrade.md) \(Nov 18, 2020\) and [Cobalt upgrade](../../mainnet/cobalt-upgrade.md) \(Apr 28, 2021\).
{% endhint %}

{% hint style="info" %}
Node doesn't need to keep the state from before the latest dump & restore upgrade \(e.g. before the [Cobalt upgrade](../../mainnet/cobalt-upgrade.md)\). Historical state can be archived separately.
{% endhint %}

{% hint style="info" %}
It is also possible to configure the Node to _not_ keep all the state from the genesis onward, reducing the amount of storage needed to keep the network's state.

To do that, set the **`consensus.tendermint.abci.prune.strategy`** and **`consensus.tendermint.abci.prune.num_kept`** parameters appropriately in your [Node's configuration](../set-up-your-node/run-validator.md#configuring-the-oasis-node).
{% endhint %}

## ParaTime Nodes

To run a ParaTime node, your system should meet the following minimum system requirements:

* 2.0 GHz x86-64 CPU

{% hint style="warning" %}
The CPU must have [AES-NI](https://en.wikipedia.org/wiki/AES_instruction_set) support.
{% endhint %}

{% hint style="warning" %}
If you want to be able to run ParaTimes which require the use of a Trusted Execution Environment \(TEE\), the CPU needs to support Intel SGX.
{% endhint %}

* 8 GB ECC RAM
* 100+ GB High Speed Storage

