---
description: Send value across chains with Hyperlane's Liquidity Layer.
---

# Liquidity Layer V2

{% hint style="warning" %}
The LiquidityLayer V2 API is in beta. The API is subject to change
{% endhint %}

This tutorial demonstrates how to:

* [Send](../../apis-and-sdks/token-bridge-api.md#send) a simple interchain message alongside with tokens to a pre-deployed [`TestTokenRecipient`](https://github.com/hyperlane-xyz/hyperlane-monorepo/blob/main/solidity/contracts/test/TestTokenRecipient.sol) contract. We are using [Circle's CCTP](https://developers.circle.com/stablecoin/docs/cctp-getting-started) to facilitative the native token transfer of USDC.
* [Pay for interchain gas](../../apis-and-sdks/token-bridge-api.md#paying-for-interchain-gas) to have a [relayer](../../protocol/agents/relayer.md) deliver the message.

### Inputs

* `$LIQUIDITY_LAYER_ROUTER`: The address of the LiquidityLayerRouter which you can find under [here](../../resources/addresses.md)
* `$DESTINATION_DOMAIN`: The domain ID of the destination chain. Domain IDs can be found [here](../../resources/domains.md). Goerli's domain ID is `5`, Fuji is `43113`
* `$RECIPIENT`: The address of the `TestTokenRecipient` contract on the destination chain padded to bytes32, `0x00000000000000000000000085ac1164878e017b67660a74ff1f41f3D05C02Bb` on every chain.
* `$TOKEN_ADDRESS`: The address of the Token you want to transfer. On Goerli, USDC is at `0x07865c6e87b9f70255377e024ace6630c1eaa37f`. On Fuji, USDC is at `0x5425890298aed601595a70ab815c96711a31bc65`. On Ethereum, USDC is at `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48`. On Avalanche, USDC is at `0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E`

### Acquire tokens

For transferring USDC via the CCTP on testnet, you can use [https://usdcfaucet.com/](https://usdcfaucet.com/). For mainnet, acquire USDC on your favorite CEX or DEX.

### Send a message with tokens

Sending a message with tokens is a simple matter of calling `TokenRouter.transferRemote`. This function can be called easily using Etherscan+[Metamask](https://metamask.io/) or [cast](https://book.getfoundry.sh/cast/).

{% tabs %}
{% tab title="Using Metamask" %}
**Approve USDC to the Liquidity Layer V2**

1. Navigate to the USDC token contract page on [Etherscan](https://goerli.etherscan.io/token/0x07865c6e87b9f70255377e024ace6630c1eaa37f#writeProxyContract)
2. Under the `Contract` tab, find the `Write as Proxy` button.
3. Click on the `Connect to Web3` button to connect your Wallet (i.e. Metamask). Make sure that you are on the correct network.
4. Expand the `approve` box.
5. Approve the as the spender with the desirable amount (Note that USDC has 6 decimals vs. the conventional 18)
6. Submit the transaction via Metamask

**Dispatch a Message With Tokens**

1. Navigate to the LiquidityLayerRouter contract page on **TODO** [Etherscan](https://goerli.etherscan.io/address/0x2abe0860D81FB4242C748132bD69D125D88eaE26).
2. Under the `Contract` tab, find the `Write as Proxy` button.
3. Click on the `Connect to Web3` button to connect your Wallet (i.e. Metamask). Make sure that you are on the correct network.
4. Expand the `transferRemote` box.
5. For destination domain, enter `$DESTINATION_DOMAIN`.
6. For the recipient address, enter `$RECIPIENT`.
7. For the token, we only support USDC for Liquidity Layer V2.
8. For the amount, enter the desirable amount
9. Submit the transaction via your wallet/Metamask

**TODO-figure**
    <figure><img src="../../.gitbook/assets/Screen Shot 2022-11-03 at 1.56.04 PM.png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Using Cast" %}
You can call `TokenRouter.transferRemote` directly using `cast`. Make sure that you have a valid RPC URL for the origin chain and a private key with which you can pay for gas.

The final parameters is the amount of the USDC token you wish to send (not accounting for token decimals).

This example shows how to send 1 USDC (USDC has 6 decimals, so `1000000` is one full USDC) via the Circle bridge.

<pre class="language-shell" data-overflow="wrap"><code class="lang-shell"><strong>cast send $LIQUIDITY_LAYER_ROUTER "transferRemote(uint32,bytes32,uint256)" $DESTINATION_DOMAIN $RECIPIENT 1000000
</strong><strong>--private-key $PRIVATE_KEY
</strong></code></pre>
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
For your transfer to be executed on the destination chain, you **must** [manually-pay-for-interchain-gas.md](../guides/manually-pay-for-interchain-gas.md "mention"), using `350000` for the gas amount
{% endhint %}

{% content-ref url="../guides/manually-pay-for-interchain-gas.md" %}
[manually-pay-for-interchain-gas.md](../guides/manually-pay-for-interchain-gas.md)
{% endcontent-ref %}
