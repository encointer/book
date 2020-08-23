# Testnets

Encointer maintains two testnets: Gesell and Cantillon.

The networks differ in their goals and designs:

## Gesell

Aimed at automated testing of the protocol.

* Time-warping allows to script bot populations and hold ceremonies every 30min.
* Complete transparency of all registries and balances. Everything happens on-chain.

## Cantillon

The main purpose of Cantillon is to test our mobile phone app and privacy features.
Aimed at experimenting with real ceremony meetups, physically meeting people.
Bot communities can still be grown but we expect them to be loctated in off-shore locations in order not to interfere with physical communities. 

* Accelerated ceremony schedule to avoid long wait times. Ceremonies every three days.
* Privacy enhancement through Trusted Execution environments (with enclaves still in development mode, so privacy is not guaranteed before we move to production mode)

Cantillon is planned to become a parachain to [Kusama](https://kusama.network/)

Watch our [demo video on bootstrapping a bot community](https://youtu.be/X1Zb68Z1fac)

## Outlook on Mainnet

The mainnet is planned to become a parachain of [Polkadot](https://polkadot.network/). The security will depend on polkadot relay chain. 

## Time Warping for Testnets

In order to understand the different timing on our networks, we offer the following figure:

![Phase Timing](./fig/phase-timing.svg)

## Testing Cantillon's Teeproxy System Locally 

You can run an entire Demo locally on any properly set up SGX machine. This is for advanced users or developers. The instructions assume that you are able to build substrate blockchains.

Build client and worker along the [substraTEE-worker instructions](https://www.substratee.com/howto_worker.html). With the following differences:
```console 
git clone https://github.com/encointer/encointer-worker.git
cd encointer-worker
./ci/install-rust.sh
make
```

Build node along the [substraTEE-node instructions](https://www.substratee.com/howto_node.html#build). With the following differences:

```console
git clone https://github.com/encointer/encointer-node.git
cd encointer-node
git checkout sgx-master
cargo build --release
```

Run dev node locally

```console
..encointer-node# ./target/release/encointer-node-teeproxy --dev --ws-port 9979
```

Run dev worker with a few insightful logs locally
```bash
cd encointer-worker/bin
./encointer-worker init-shard
./encointer-worker shielding-key
./encointer-worker signing-key
export RUST_LOG=info,substrate_api_client=warn,sp_io=warn,ws=warn,encointer_worker=info,substratee_worker_enclave=debug,sp_io::misc=debug
./encointer-worker -p 9979 run
```
