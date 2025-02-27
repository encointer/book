# Full Node Setup

This document describes how to set up a full Encointer node. A full node is a node that validates the entire blockchain history. Interacting with a full node you run yourself is the most secure and most privacy-preserving way to interact with the Encointer network.

## Requirements

### HW Requirements

Full nodes can be run on very affordable HW. The minimum requirements are
* 1TB NVMe SSD. 
   * seq. write performance should be > 1000 MB/s. Consider that SSD's get slower the more full they get. Also, they wear out with intense usage)
   * if you want to run the collator in archive mode for indexing and bootstrapping, you'll currently need at least a 2 TB SSD
* 16GB RAM (8GB currently works, but is not recommended)
* CPU won't matter much, but go for maximum single-core performance rather than many cores.

### OS Requirements

In the following, we'll assume you're using ubuntu 22.04

### Internet Connection Requirements

If you run your node in a datacenter, you can skip this section. If you run consumer HW and "fair-use" internet connections like cellular, this is for you:
  
The initial sync of the chain will cause significant data volume of several 100 GB. If you run on a metered internet connection you may want to check your tariffs.

Once your chain is synced you should expect a constant data bandwidth in the order of 0.2-1.0 MB/s up and down. If you need to throttle that you may want to look into restricting the number of peers `--in-peers`, `--in-peers-light`, `--out-peers` .

You should also check your networking latency with `ping telemetry.polkadot.io`. The result depends on your geographical location, obviously. If your ping time exceeds 500ms you may have networking issues. If you want to run a collator, latency is critical. 

## Install Dependencies

The recommended way to setup a full node is to start with the [ansible setup](https://github.com/w3f/polkadot-validator-setup) provided 
by w3f. 

Should you wish to do it manually, we'll redirect you to the polkadot docs
* [run a validator](https://wiki.polkadot.network/docs/maintain-guides-how-to-validate-polkadot)
* [secure validator](https://wiki.polkadot.network/docs/maintain-guides-secure-validator)

## Systemd Service

We assume you'd like to use your node for rpc queries from outside and monitor it with prometheus

The settings are optimized to minimize resource consumption

```
[Unit]
Description=Encointer Kusama Collator

[Service]
User=encointer
Group=collators

ExecStart=/home/encointer/encointer-kusama/encointer-collator \
  --chain=encointer-kusama \
  --base-path <your large volume> \
  --sync=full \
  --database paritydb \
  --state-pruning 256 \
  --blocks-pruning 256 \
  --db-cache 512 \
  --name <a name that will appear in telemetry>> \
  --telemetry-url 'wss://telemetry.polkadot.io/submit 1' \
  --prometheus-external \
  --rpc-cors all \
  --rpc-methods=Unsafe \
  --enable-offchain-indexing true \
    -- \
  --chain kusama \
  --base-path <your large volume> \
  --sync=warp \
  --database paritydb \
  --state-pruning 256 \
  --blocks-pruning 256 \
  --db-cache 512 \
  --prometheus-external \

Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
```
