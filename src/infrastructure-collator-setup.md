# Collator Setup

Collators are parachain [full nodes](./infrastructure-full-node-setup.md) which additionally collect pending transactions, order them and propose new blocks on Encointer Network. Their operation is critical for availability, but not for security. The latter is provided by the validators of the Kusama relay chain. 

### Who can run collators?

Encointer’s aura consensus is based on a quasi-static set of authorities which can be mutated every 6 hours. This collator set consists of permanent, *invulnerable* nodes and a dynamic, unpermissioned set of nodes. Invulnerables are currently run by the Encointer Association and partners. Anyone can join the set of unpermissioned collators by submitting a candidacy and bonding some stake in KSM. Candidates are selected based on their stake until the desired set is full.   

#### Requirements

* a full node running with high availability
* enough KSM to bond (more than the lowest active bid by other collators). For [invulnerables](infrastructure-invulnerable-collators.md) such a bond is not necessary.

#### Incentives

As Encointer is a systemchain on Kusama, collator operators can request compensation from [Kusama Bounty 20](https://kusama.subsquare.io/treasury/bounties/20) and get rewarded from the Kusama Treasury, subject to multiple conditions.

Moreover, collators collect extrinsic fees on the network. On Encointer, fees are paid in either KSM or any of the community currencies. As a collator you will, therefore, also receive community currencies from all around the world.

We encourage members of Encointer communities to run their own collators to ensure the network is decentralized and censorship-resistant. Communities who don't run their own collators rely on others to do so who could in theory de-prioritize, delay or even censor transactions for a particular community. Collecting fees in their own community currency is added benefit.

### Run a Collator Node

See the above chapter on how to [run a full node](./infrastructure-full-node-setup.md). There is only one difference: The parachain CLI (the part before the `--`) needs the `--collator` flag.

### Generate an Authority Key

Collators need to manage two types of keys: 
* A *session key* which is used to sign blocks. this key must always be online and is therefore not considered highly secure. It should be rotated regularly.
* An *authority key* which used to submit candidacy, bond funds and change session keys. This key isn't needed very often and should be kept secure.  

To generate an authority key, please choose a [polkadot wallet](https://polkadot.network/ecosystem/wallets/) and follow the steps to generate and secure a new account. In the following, we assume that you use a browser extension to keep your authority key safe.

Caveats:
* Do not use pure proxies as authorities if you want to claim compensation from the collator bounty [Kusama Bounty 20](https://kusama.subsquare.io/treasury/bounties/20) because payouts will go to asset hub, where your pure proxy won't exist.

### Set Onchain Identity

It is good practice to identify yourself on-chain, at least with some recognizable pseudonym. If you aspire for an invulnerable slot, this is mandatory. Encointer community collators must identify themselves with the community name. Moreover, we expect invulnerables to provide a [matrix handle](https://matrix.org/) as a way to contact you.

Use [Kusama People Identity](https://wiki.polkadot.com/learn/learn-identity/) for this. You're free to use sub-identities for your collator authorities if you prefer. We do not require judgement for collator identities. You'll need at least 0.1 KSM on your authority account on Kusama People Chain to pay for the fees and deposits. Learn how to [teleport KSM](https://wiki.polkadot.com/learn/learn-teleport/) to Kusama People.

### Rotate Session Key

The best way to rotate session keys is to let the collator itself do this. The `author_rotateKeys` rpc call will generate a new key and print the public key. On your collator machine, run the following command:

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9944
```

Take note of the fresh public key in hex format which is returned returned as json response.

### Register Session Key

Use the [session.setKeys](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fsys.ibp.network%2Fencointer-kusama#/extrinsics/decode/0x1600000000000000000000000000000000000000000000000000000000000000000000 ) call to register your key, signing with your authority key.
* `keys`: your session key in hex format with `0x` prefix
* `proof`: not needed. just put `0x` 

Your authority account should have at least a balance of 0.02 KSM on Encointer Network to pay for the transaction fees. Learn how to [teleport KSM](https://wiki.polkadot.com/learn/learn-teleport/) to Encointer Network.

### Submit Candidacy

Use your authority key to call [collatorSelection.registerAsCandidate](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fsys.ibp.network%2Fencointer-kusama#/extrinsics/decode/0x1503). This will bond 5 KSM. Please make sure your free balance is higher than that.

Your collator should appear on the [collator dashboard](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fsys.ibp.network%2Fencointer-kusama#/collators). In any case, you'll need to wait for the next session (up to 6 hours)

When there are more candidates than desired nodes, preference will be given to the candidates with highest bonds. If you wish to update your bond, please call [collatorSelection.updateBond](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fsys.ibp.network%2Fencointer-kusama#/extrinsics/decode/0x150700000000000000000000000000000000)

Should you be selected into the set of active collators, any downtime can lead to degraded block times. Should your node fail to propose blocks, you may be removed for the upcoming session.


