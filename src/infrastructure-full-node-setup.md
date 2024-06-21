# Full Node Setup

This document describes how to set up a full Encointer node. A full node is a node that validates the entire blockchain history. Interacting with a full node you run yourself is the most secure and most privacy-preserving way to interact with the Encointer network.

## HW requirements

Full nodes can be run on very affordable HW. The minimum requirements are
* 1TB NVMe SSD
* 16GB RAM (8GB currently works, but is not recommended)
* CPU won't matter much, but go for maximum single-core performance rather than many cores.

## OS requirements

In the following, we'll assume you're using ubuntu 22.04

## Install dependencies

The recommended way to setup a full node is to start with the [ansible setup](https://github.com/w3f/polkadot-validator-setup) provided 
by w3f. 

Should you wish to do it manually, we'll redirect you to the polkadot docs
* [run a validator](https://wiki.polkadot.network/docs/maintain-guides-how-to-validate-polkadot)
* [secure validator](https://wiki.polkadot.network/docs/maintain-guides-secure-validator)

## systemd service

We assume you'd like to use your node for rpc queries from outside and monitor it with prometheus

```
[Unit]
Description=Encointer Kusama Collator

[Service]
User=encointer
Group=collators

ExecStart=/home/encointer/encointer-kusama/encointer-collator \
  --rpc-cors all \
  --name <a name that will appear in telemetry>> \
  --telemetry-url 'wss://telemetry.polkadot.io/submit 1' \
  --base-path <your large volume> \
  --state-pruning archive \
  --blocks-pruning archive \
  --chain=encointer-kusama \
  --rpc-methods=Unsafe \
  --prometheus-external \
  --enable-offchain-indexing true \
    -- \
  --sync=warp \
  --chain kusama \
  --prometheus-external \
  --base-path <your large volume> \

Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
```
