# Using Ledger-backed Consensus Key with a Remote Signer

This guide will describe how you can set up your [_validator_ node](../set-up-your-node/run-validator.md) with a detached consensus key stored on a [Ledger](https://www.ledger.com/) wallet connected via [Oasis Core's Remote Signer](https://github.com/oasisprotocol/oasis-core/tree/master/go/oasis-remote-signer).

{% hint style="warning" %}
These instructions are still a work-in-progress draft.

Your feedback is welcome. Please, reach out via the [**\#nodeoperators** Oasis Community Slack channel](../../oasis-network/connect-with-us.md) with your questions, comments, and feedback.
{% endhint %}

{% hint style="danger" %}
These are advanced instructions intended for node operators that want to increase their validator node security by having the consensus key be detached and stored on a Ledger wallet and connected via Oasis Core's remote signer instead of having consensus key simply be a file on the file system.
{% endhint %}

## Prerequisites

Before following this guide, make sure you've followed the [Prerequisites](../prerequisites/) section and have the Oasis Node binary installed on your Oasis node system.

Also make sure you've followed the [Install Oasis Remote Signer Binary](install-oasis-remote-signer-binary.md) section and have the Oasis Remote Signer binary installed on your remote signer system where your Ledger wallet will be connected.

Finally, you need to do steps described in the [Setup](https://docs.oasis.dev/oasis-core-ledger/usage/setup) section of our [Oasis Core Ledger docs](https://docs.oasis.dev/oasis-core-ledger). Make sure you also install the _**OasisVal**_ **app** on your Ledger wallet.

## Set Up Remote Signer System

### Initialize Remote Signer

On your remote signer system, create a directory for the remote signer, e.g. `remote-signer`, by running:

```text
mkdir --mode=700 remote-signer
```

Then initialize remote signer's keys \(consensus, entity, identity, ...\) and server certificate by running:

```text
oasis-remote-signer init --datadir remote-signer/
```

Also initialize remote signer's client certificate which will be used by the Oasis node system to connect to the remote signer system:

```text
oasis-remote-signer init_client --datadir remote-signer/
```

### Run Remote Signer

{% hint style="info" %}
Before following the instructions below, make sure your Ledger wallet is connected to your remote signer system, unlocked and the OasisVal App is open.
{% endhint %}

{% hint style="warning" %}
While the OasisVal App is available in _Developer mode_, opening the App brings up the "Pending Ledger review" screen. You need to press both buttons at once to close that screen and transition to the _ordinary_ "Oasis Ready" screen where the OasisVal App is ready to be used.
{% endhint %}

Choose the gRPC port on which the remote signer will listen for client requests and set the path to the `ledger-signer` plugin in the appropriate environment variables and run:

```text
GRPC_PORT=<REMOTE-SIGNER-PORT>
LEDGER_SIGNER_PATH=<PATH-TO-LEDGER-SIGNER-PLUGIN>
oasis-remote-signer \
--datadir remote-signer \
--signer.backend composite \
--signer.composite.backends entity:file,node:file,p2p:file,consensus:plugin \
--signer.plugin.name ledger \
--signer.plugin.path $LEDGER_SIGNER_PATH \
--client.certificate remote-signer/remote_signer_client_cert.pem \
--grpc.port $GRPC_PORT \
--log.level DEBUG
```

{% hint style="info" %}
If necessary, you can configure the Ledger-based signer by setting the `--signer.plugin.config` flag appropriately, e.g.:

```text
--signer.plugin.config "wallet_id:1fc3be,index:5"
```

to configure the wallet with a specific ID and to use a non-zero account index.
{% endhint %}

{% hint style="success" %}
The Oasis Remote Signer is configured to run in the foreground by default.

We recommend you configure and use it with a process manager like [systemd](https://github.com/systemd/systemd) or [Supervisor](http://supervisord.org/).
{% endhint %}

### Copy Remote Signer Certificate and Client Key and Certificate

For the Oasis node system to be able to connect to the Oasis Remote Signer on the remote signer system and to be able to demonstrate its authenticity to it, you need to copy the following files from your remote signer system to your Oasis node system:

* `remote-signer/remote_signer_server_cert.pem`: The remote signer's certificate. This certificate ensures the Oasis node system is connecting to the trusted remote signer system.
* `remote-signer/remote_signer_client_key.pem`: The remote signer's client key. This key enables the Oasis node system to demonstrate its authenticity when it is requesting signatures from the remote signer system.
* `remote-signer/remote_signer_client_cert.pem`: The remote signer's client certificate. This certificate is the counterpart of the remote signer's client key.

## Set Up Oasis Node System

For setting up your Oasis Node system, follow the [Run a Validator Node](../set-up-your-node/run-validator.md) docs.

{% hint style="info" %}
Make sure you've copied the remote signer's certificate and remote signer's client key and certificate to your Oasis Node system as described in [Copy Remote Signer Certificate and Client Key and Certificate](using-ledger-backed-consensus-key-with-a-remote-signer.md#copy-remote-signer-certificate-and-client-key-and-certificate) section.
{% endhint %}

### Initialize Oasis Node

When [initializing your Oasis node](../set-up-your-node/run-validator.md#initializing-a-node), you need to pass appropriate `--signer.*` flags to configure the composite and remote signers. For example:

```text
ENTITY_DIR=<YOUR-ENTITY-DIR>
STATIC_IP=<YOUR-STATIC-IP>
REMOTE_SIGNER_IP=<YOUR-REMOTE-SIGNER-IP>
REMOTE_SIGNER_PORT=<YOUR-REMOTE-SIGNER-PORT>
oasis-node registry node init \
--signer.backend composite \
--signer.composite.backends 1:file,2:file,3:file,4:remote \
--signer.dir $ENTITY_DIR \
--node.consensus_address $STATIC_IP:26656 \
--node.role validator \
--signer.remote.address $REMOTE_SIGNER_IP:$REMOTE_SIGNER_PORT \
--signer.remote.client.certificate remote-signer/remote_signer_client_cert.pem \
--signer.remote.client.key remote-signer/remote_signer_client_key.pem \
--signer.remote.server.certificate remote-signer/remote_signer_server_cert.pem \
--datadir node/
```

This assumes you've copied the remote signer's certificate and remote signer's client key and certificate to the `remote-signer/` directory.

{% hint style="success" %}
The resulting directory only has `consensus_pub.pem` and no `consensus.pem` since the consensus key is Ledger-backed.
{% endhint %}

### Configure Oasis Node

When [configuring your Oasis Node](../set-up-your-node/run-validator.md#configuring-the-oasis-node), you need to add the appropriate `signer` section to configure the composite and remote signers. For example:

```yaml
# Signer.
signer:
  backend: composite
  # Use file-based signer for entity, node and P2P keys and remote signer for the
  # consensus key.
  composite:
    backends: entity:file,node:file,p2p:file,consensus:remote
  # Configure how to connect to the Oasis Remote Signer.
  remote:
    address: <YOUR-REMOTE-SIGNER-IP>:<YOUR-REMOTE-SIGNER-PORT>
    server:
      certificate: /srv/oasis/remote-signer/remote_signer_server_cert.pem
    client:
      key: /srv/oasis/remote-signer/remote_signer_client_key.pem
      certificate: /srv/oasis/remote-signer/remote_signer_client_cert.pem
```

This assumes you've copied the remote signer's certificate and remote signer's client key and certificate to the `/srv/oasis/remote-signer/` directory.

{% hint style="info" %}
To ensure your Oasis Node will be able to sign consensus transactions, make sure your Ledger wallet is connected to your remote signer system, unlocked and the OasisVal App is open.

Also make sure the Oasis Remote Signer is running on your remote signer system and accepting remote client connections via the designated port.
{% endhint %}

