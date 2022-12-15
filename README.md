## pyk's home labs

This is my home labs running the following services:

1. Ethereum Archive Node (Erigon+Lighthouse)
2. Gnosis Chain Validators (Nethermind+Lighthouse)
3. Monitoring tools (Prometheus+Grafana)

### Setup

Create the `jwtsecret`:

```sh
openssl rand -hex 32 > jwtsecret
```

### Gnosis Chain Validators Setup

Here is step by step to setup gnosis chain validators:

1. Download [Gnosis Wagyu Keygen](https://docs.gnosischain.com/node/guide/validator/generate-keys/wagyu).
2. Disconnect the internet access.
3. Run the app.
4. Import or create new mnemonic recovery phrase.
5. Create new keys (input amount of existing keys, if any) with a password.

After create new keys, you will have the following files:

```
deposit_data-[...].json
keystore-m_[...].json
```

Copy the files `keystore*` files to [./gnosis/validator/](./gnosis/validator).

Add your password that you use to generate the keys to `./gnosis/validator/password.txt`.

Then import the keys using the following command:

```sh
docker run \
  --rm \
  --volume /home/pyk/labs/gnosis/validator:/keystores \
  --volume /home/pyk/labs/gnosis/consensus:/data sigp/lighthouse:latest-modern lighthouse account validator import \
  --network gnosis \
  --password-file /keystores/password.txt \
  --reuse-password \
  --directory /keystores \
  --datadir /data
```

You may need to stop the existing validators service first:

```
docker compose stop gnosis-validators
```

Rerun the services:

```
docker compose up -d
```

Check logs:

```sh
docker compose logs -f --tail 10 gnosis-execution
docker compose logs -f --tail 10 gnosis-beacon
docker compose logs -f --tail 10 gnosis-validators
```

Wait the chain sync for ~24hours, then follow the [Validator Deposit](https://docs.gnosischain.com/node/guide/validator/deposit) guide.

Reminder to my futureself: Currently I have 32 validators running in Gnosis Chain.


### Lesson Learned

- Wait Execution & Consensus nodes to 100% sync before starting a validator. Otherwise you will miss a lot of attestations.