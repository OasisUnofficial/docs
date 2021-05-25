# Install Oasis Node Binary

The Oasis Node is a binary that is created from the [Oasis Core](https://github.com/oasisprotocol/oasis-core) repository's `go/` path.

It contains both the logic for running an Oasis node and also provides a CLI for handling registry, staking and other operations.

{% hint style="warning" %}
The Oasis Node is currently only supported on x86\_64 Linux systems.
{% endhint %}

## Downloading a Binary Release

{% hint style="success" %}
We suggest that you build Oasis Node from source yourself for a production deployment of an Oasis node.
{% endhint %}

For convenience, we provide binaries that have been built by the Oasis Protocol Foundation. Links to the binaries are provided in the [Network Parameters](../../oasis-network/network-parameters.md) page.

## Building From Source

Although highly suggested, building from source is currently beyond the scope of this documentation.

See [Oasis Core's Build Environment Setup and Building](https://docs.oasis.dev/oasis-core/development-setup/build-environment-setup-and-building) documentation for more details.

{% hint style="danger" %}
The code in the [`master` branch](https://github.com/oasisprotocol/oasis-core/tree/master/) might be incompatible with the code used by other nodes in the Mainnet.

Make sure to use the version specified in the [Network Parameters](../../oasis-network/network-parameters.md).
{% endhint %}

## Adding `oasis-node` Binary to `PATH`

To install the `oasis-node` binary for the current user, copy/symlink it to `~/.local/bin`.

To install the `oasis-node` binary for all users of the system, copy it to `/usr/local/bin`.

## Running ParaTimes

If you intend to [run a ParaTime node](../set-up-your-node/run-a-paratime-node.md) you will need to additionally install the following software packages:

* [Bubblewrap](https://github.com/projectatomic/bubblewrap) \(at least version 0.4.1\), needed to create process sandboxes.

On Ubuntu 20.04+, you can install all the above with:

```text
sudo apt install bubblewrap
```

On Fedora 29+, you can install all the above with:

```text
sudo dnf install bubblewrap
```

