---
description: Required setup instructions
---

# Guide

## Configuration

The relayer is responsible for watching for new messages on the origin chain(s) and delivering them to their destination chains. This involves being able to submit transactions to many destination chains, and therefore requires access to a key for signing transactions. There are two supported key types: hexadecimal private keys (for in-memory signing), and AWS KMS based keys (best practice for production environments).

### Hexadecimal keys

A hexadecimal private key used for in-memory signing can be used by your relayer to sign transactions. This is the recommended setup for testing or development purposes.

{% content-ref url="../agent-keys/hexadecimal-key-setup.md" %}
[hexadecimal-key-setup.md](../agent-keys/hexadecimal-key-setup.md)
{% endcontent-ref %}

### AWS KMS keys

An AWS KMS key can be used by your relayer to sign transactions. This is the recommended setup for production relayers.

{% content-ref url="../agent-keys/aws-setup.md" %}
[aws-setup.md](../agent-keys/aws-setup.md)
{% endcontent-ref %}

### Reference

Also take a look at the [agent-configuration](../agent-configuration/ "mention") page and the [configuration-reference.md](../agent-configuration/configuration-reference.md "mention") for a full list of configuration possibilities. **The list below is not complete,** however it should be enough to get started.

Your relayer takes as configuration the following:

| Argument                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--relayChains`                        | <p>Comma separated names of the origin and destination chains to relay messages between.<br><em>Example:</em> <code>ethereum,polygon,avalanche</code><br><br>(See also the <a data-mention href="../agent-configuration/configuration-reference.md">configuration-reference.md</a> for how to specify origin and destination chains indipendently)</p>                                                                                                                 |
| `--chains.[chain_name].connection.url` | <p>An RPC url for <code>chain_name</code>.<br><em>Example:</em> <code>--chains.ethereum.connection.url='http://localhost:8545'</code><br><br><strong>Relayers must set multiple connection URLs, one for each chain it interacts with.</strong></p>                                                                                                                                                                                                                    |
| `--whitelist`                          | <p>An optional whitelist. The relayer will only relay messages that match this whitelist.<br><br>See <a data-mention href="message-filtering.md">message-filtering.md</a>for more info.</p>                                                                                                                                                                                                                                                                            |
| `--blacklist`                          | <p>An optional blacklist. The relayer will not relay messages that match this blacklist.<br><br>See <a data-mention href="message-filtering.md">message-filtering.md</a>for more info.</p>                                                                                                                                                                                                                                                                             |
| `--db`                                 | <p>The path to where the validator should write persistent data to disk. Ensure this path to be persistent when using cloud setups. When using Docker, make sure to mount the persistent path/volume into the container.<br><br>See <a data-mention href="../agent-configuration/configuration-reference.md">configuration-reference.md</a>for more info</p>                                                                                                           |
| `--allowLocalCheckpointSyncers`        | <p>If <code>true</code>, this will allow the relayer to look for validator signatures on the relayer's local filesystem.<br>In a production environment, this should be <code>false</code>.<br>If you're running a validator on the same machine by following the validator <a data-mention href="../validators/setup.md#local-setup">#local-setup</a> instructions, set this to <code>true</code> so that your relayer can access the local validator signatures.</p> |
|                                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |

| Environment variable | Description                                                                                                                                                                                                                                                                                                                                                                               |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CONFIG_FILES`       | If you want to add additional configuration files you can add additional paths here as a comma separated list. These files must be accessible within the filesystem your agent has access to. If you're running in Docker, see [#config-files-with-docker](../agent-configuration/#config-files-with-docker "mention") for tips on mounting your config files into your Docker container. |

### Setup-specific configuration

These configurations requirements differ depending on which [setup.md](setup.md "mention") instructions you followed.

{% tabs %}
{% tab title="Hexadecimal keys" %}
If you created [hexadecimal-key-setup.md](../agent-keys/hexadecimal-key-setup.md "mention"), use these configs.

| Argument              | Description                                                                                                                                                 |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--defaultSigner.key` | <p>A hexadecimal private key used to sign transactions for <code>chain_name</code>.<br><br><em>Example:</em> <code>--defaultSigner.key=123...def</code></p> |
{% endtab %}

{% tab title="AWS KMS keys" %}
If you created [aws-setup.md](../agent-keys/aws-setup.md "mention"), use these configs.

| Argument                 | Description                                                                                                                                                                               |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--defaultSigner.type`   | <p>Set to <code>aws</code>.</p><p><br><em>Example:</em> <code>--defaultSigner.type=aws</code></p>                                                                                         |
| `--defaultSigner.id`     | <p>The alias of your validator's AWS KMS key, prefixed with <code>alias/</code>.</p><p><br><em>Example:</em> <code>--defaultSigner.id=alias/hyperlane-validator-signer-polygon</code></p> |
| `--defaultSigner.region` | <p>The region of your AWS KMS key.<br><br><code>--defaultSigner.region=us-east-1</code></p>                                                                                               |

| Environment variable    | Description                                           |
| ----------------------- | ----------------------------------------------------- |
| `AWS_ACCESS_KEY_ID`     | The access key ID of your relayer's AWS IAM user.     |
| `AWS_SECRET_ACCESS_KEY` | The secret access key of your relayer's AWS IAM user. |
{% endtab %}
{% endtabs %}

For chain-specific signers take a look at the [configuration-reference.md](../agent-configuration/configuration-reference.md "mention")

## Installation

The recommended installation method for a production environment is using a Docker image.

{% tabs %}
{% tab title="Docker" %}
### Docker image

To download the docker image, run:

{% code overflow="wrap" %}
```bash
docker pull gcr.io/abacus-labs-dev/hyperlane-agent:1fff74e-20230614-005705
```
{% endcode %}
{% endtab %}

{% tab title="Source" %}
### Building from source

First, clone the repo

```sh
git clone git@github.com:hyperlane-xyz/hyperlane-monorepo.git
```

And then follow the [setup instructions](https://github.com/hyperlane-xyz/hyperlane-monorepo/blob/main/rust/README.md#setup) in the `rust` directory
{% endtab %}
{% endtabs %}

## Start Relaying

### Running the binary

Refer to the [#installation](setup.md#installation "mention") instructions to access the relayer binary.

[#configuration](setup.md#configuration "mention") can be placed in a `relayer.env` file, for example:

{% code title="relayer.env" overflow="wrap" %}
```sh
# These are example values to roughly illustrate
# what a .env file should look like

HYP_BASE_RELAYCHAINS=ethereum,polygon,avalanche
HYP_BASE_DB="/hyperlane_db"
# ...
# ...
```
{% endcode %}

To run the relayer binary with the environment variables specified in `relayer.env`:

{% tabs %}
{% tab title="Using Docker" %}
Find the latest [#docker-image](setup.md#docker-image "mention") and set it to the environment variable `$DOCKER_IMAGE`.

Using the `--env-file` flag, we can pass in the environment variables to the relayer:

{% code overflow="wrap" %}
```sh
docker run \
  -it \
  --env-file relayer.env \
  --mount type=bind,source="$(pwd)"/hyperlane_db,target=/hyperlane_db \
  $DOCKER_IMAGE \
  ./relayer
```
{% endcode %}

{% hint style="info" %}
If you have followed the instructions to [deploy-hyperlane.md](../../deploy/deploy-hyperlane.md "mention") and are specifying a path to your own config file in the `CONFIG_FILES` environment variable, check out [#config-files-with-docker](../agent-configuration/#config-files-with-docker "mention").
{% endhint %}

{% hint style="info" %}
If you're running validator with [#local-setup](../validators/setup.md#local-setup "mention") on the same machine and want the relayer to access these validator signatures, be sure to [mount](https://docs.docker.com/storage/bind-mounts/) your local validator's signature directory into your relayer at the same path that you used when [#announcing-your-validator](../validators/setup.md#announcing-your-validator "mention")

For example, if your local validator is writing signatures to `/tmp/hyperlane-validator-signatures-ethereum`, you should mount a directory for the Docker container:

{% code overflow="wrap" %}
```sh
docker run \
  -it \
  --env-file relayer.env \
  --mount type=bind,source="$(pwd)"/hyperlane-validator-signatures-ethereum,target=/tmp/hyperlane-validator-signatures-ethereum,readonly \
  --mount type=bind,source="$(pwd)"/hyperlane_db,target=/hyperlane_db \
  $DOCKER_IMAGE \
  ./relayer
```
{% endcode %}
{% endhint %}
{% endtab %}

{% tab title="Without Docker" %}
See these instructions for [#building-from-source](setup.md#building-from-source "mention").

We can run the built binary from within the `hyperlane-monorepo/rust` directory with the environment variables found in `relayer.env`:

{% code overflow="wrap" %}
```sh
env $(cat validator.env | grep -v "#" | xargs) ./target/release/relayer
```
{% endcode %}
{% endtab %}
{% endtabs %}

Relayers needs to index all historic messages for the origin chain(s). This information is stored in a local database on disk (set with `db` in the config). This means running a relayer for the first time may take some extra time to catch up with the current state.
