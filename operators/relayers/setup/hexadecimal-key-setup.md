---
description: Generating your relayer's key
---

# Hexadecimal key setup

While the best practice for a production environment is to use [AWS KMS keys](aws-setup.md), hexadecimal keys can be used with the relayer.

If you're just running a relayer locally for testing or development purposes, this is the recommended method of key generation.

### Generate a hexadecimal key&#x20;

{% tabs %}
{% tab title="Cast CLI" %}
Using [Foundry's cast](https://book.getfoundry.sh/cast/), you can create a new key pair:

{% code overflow="wrap" %}
```sh
cast wallet new
```
{% endcode %}

The output will look something like this. Keep track of this address and private key for use in a later step.

{% code overflow="wrap" %}
```
Successfully created new keypair.
Address: 0x32e6d32E7b1C8691Ef4ad3841003371214a0eebC
Private Key: 0x2958f0eb2ab71bbfb5ea1422835e20e488778b61e3c107f369572e2b53b578f9
```
{% endcode %}
{% endtab %}

{% tab title="Using privatekeys.pw" %}
You can visit [https://privatekeys.pw/keys/ethereum/random](https://privatekeys.pw/keys/ethereum/random) and take note of an address & private key for use in future steps as the validator key.

{% hint style="warning" %}
Please note that because these are generated by a website, these private keys should be considered insecure and should not be used for anything other than testing purposes!
{% endhint %}
{% endtab %}
{% endtabs %}

### Next

Continue to the [Environment variables](../environment-variables.md) section.

{% content-ref url="../environment-variables.md" %}
[environment-variables.md](../environment-variables.md)
{% endcontent-ref %}