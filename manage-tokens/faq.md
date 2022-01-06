---
description: >-
  This documents answers frequently asked questions about Oasis Wallets and 3rd
  party wallets & custody providers
---

# Frequently Asked Questions

### How can I transfer ROSE tokens from my [BitPie wallet](holding-rose-tokens/bitpie-wallet.md) to my Oasis Wallet?

{% hint style="warning" %}
BitPie wallet doesn't use the standardized account key generation process specified in [ADR 0008](https://github.com/oasisprotocol/oasis-core/blob/master/docs/adr/0008-standard-account-key-generation.md). Consequently, your **Bitpie wallet's mnemonic phrase will not open the same account in Oasis Wallet**.
{% endhint %}

The **preferred way** is to create a new wallet with an Oasis Wallet and transfer the tokens to this new address using your BitPie wallet. The cost (i.e. transaction fee) should be negligible.

If your tokens are staked/delegated, then you need to debond them first which will take approximately 14 days. Afterwards, you can transfer them to the new Oasis Wallet address and stake/delegate them via an Oasis Wallet again.

**Alternatively** however, if you do not want to transfer the tokens over the network, you can export the private key from your BitPie wallet and import it in Oasis Wallet in 2 steps:

1. [Export your BitPie wallet's Oasis account private key](faq.md#how-can-i-export-my-bitpie-wallets-oasis-account-private-key)
2. [Open your Oasis wallet via exported private key in Oasis Wallet - Web](https://docs.oasis.dev/general/manage-tokens/oasis-wallets/web#open-wallet-via-private-key)

### How can I export my [BitPie wallet](holding-rose-tokens/bitpie-wallet.md)'s Oasis account private key?

On the main BitPie wallet screen, click on the "Receive" button.

![](../.gitbook/assets/bitpie-mainscreen.png)

The QR code with your ROSE address will appear. Then, in the top right corner, tap on the kebab menu "⋮" and select "Display Private Key"_._

![](../.gitbook/assets/bitpie-show-private-key.png)

BitPie wallet will now ask you to enter your PIN to access the private key.

Finally, your account's private key will be shown to you encoded in Base64 format (e.g. `YgwGOfrHG1TVWSZBs8WM4w0BUjLmsbk7Gqgd7IGeHfSqdbeQokEhFEJxtc3kVQ4KqkdZTuD0bY7LOlhdEKevaQ==`) which you can [import into Oasis Wallet](oasis-wallets/web.md#access-an-existing-wallet).

### Chromium under Ubuntu does not recognize my Ledger device. What is the problem?

First check that you added the Ledger udev device descriptors as mentioned in the [Linux installation guide](https://support.ledger.com/hc/en-us/articles/4404389606417-Download-and-install-Ledger-Live). Next, check that your Ledger wallet is recognized by the [Oasis Core Ledger tool](https://docs.oasis.dev/oasis-core-ledger/usage/address). You should see something like this:

```bash
$ oasis-core-ledger show_address
oasis1qp8d9kuduq0zutuatjsgltpugxvl38cuaq3gzkmn
Ensure account address shown on device's screen matches the outputted address.
```

If all of the above works, then the issue is most likely that Chromium does not have the permission to access your Ledger device. Starting with Ubuntu 20.04 the Chromium browser is installed via snap package by default. Snap is more convenient for upstream developers to deploy their software and it also adds additional layer of security by using apparmor. In our case however, it prevents Chromium to access arbitrary USB devices with WebUSB API including your Ledger device. A workaround for this issue is to install Chromium natively using the official [Chormium beta PPA](https://launchpad.net/\~saiarcot895/+archive/ubuntu/chromium-beta) or the official [Google Chrome .deb package](https://dl.google.com/linux/direct/google-chrome-stable\_current\_amd64.deb).

### How can I use my Oasis Wallet mnemonics in Ledger?

Starting from Oasis app for Ledger v2.3.1 a standardized key derivation path as defined in [ADR 0008](https://github.com/oasisprotocol/oasis-core/blob/master/docs/adr/0008-standard-account-key-generation.md) is supported. This means that you can copy the mnemonics keyphrase you use in the Oasis Wallet - Web or in the Chrome extension directly to your Ledger device. Ledger will then derive the same Oasis wallet address and can be used to sign transactions and send funds. Similarly, you can export your keyphrase from Ledger and use it the Oasis Wallets.

{% hint style="warning" %}
Versions of Oasis app for Ledger prior to v2.3.1 used a non-standard key derivation path. Mnemonics could be imported to Ledger, but the derived wallet address and the private key would be different compared to the Oasis Wallets.
{% endhint %}

### The wallet gives me _Invalid keyphrase_ error when importing my wallet from mnemonics. How do I solve it?

Please check that:

* All mnemonics were spelled correctly. Oasis Wallets use English mnemonic phrase words as defined in BIP39. You can find a complete list of valid phrase words [here](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt).
* The mnemonics were input in correct order.
* All mnemonics were provided. The keyphrase should be either 12, 15, 18, 21, or 24 words long.

If you checked all of the above and the keyphrase still cannot be imported, please contact Oasis support.

### I imported my wallet with mnemonics. The wallet should contain funds, but the balance is empty. What can I do?

First, check your wallet address. If the address equals the one that you expected your funds on, then the key derivation from mnemonics worked correctly. Make sure you have a working internet connection so that the wallet can fetch the latest balance. Then check that the correct network (Mainnet or Testnet) is selected. These are completely separated networks and although the wallet address can be the same, the transactions and consequently the balances may differ. Finally, there might be a temporary problem with the [Oasis Monitor service](https://oasismonitor.com) itself which observes the network and indexes transactions. Oasis Wallets rely on that service and once it is back up and running, you should be able to see the correct balance.

If your wallet address is different than the one you used to transfer your funds to, then you used one of the wallets that don't implement the standardized key derivation path defined in [ADR 0008](https://github.com/oasisprotocol/oasis-core/blob/master/docs/adr/0008-standard-account-key-generation.md). If you were using the BitPie wallet see [this question](faq.md#how-can-i-export-my-bitpie-wallets-oasis-account-private-key). Ledger hardware wallet users should refer to [this question](faq.md#how-can-i-use-my-oasis-wallet-mnemonics-in-ledger).

If you still cannot access your funds, please contact Oasis support.



### I sent my ROSE to BinanceStaking address.  Are they staked? Are they lost? What can I do?

If you just make a **Send** transaction to BinanceStaking address `oasis1qqekv2ymgzmd8j2s2u7g0hhc7e77e654kvwqtjwm` then your ROSE coins are not staked. They are now owned by BinanceStaking, which means they are not lost but only owned and managed by them. In this case, you should contact Binance via their [Support Center](https://www.binance.com/en/support) or [Submit a request](https://www.binance.com/en/chat).

{% hint style="info" %}
Send transaction is different compared to Escrow**.** With Escrow transaction you lend your ROSE coins to chosen Validator and you are rewarded for that. Send transaction actually sends your ROSE coins to receiving address you enter and only person who owns private key (mnemonics) of that receiving address can access and manage these coins and nobody else.
{% endhint %}



