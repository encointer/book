# Gesell local setup (non-docker)

## Launch Gesell node

### Build node 

along the [substraTEE-node instructions](https://www.substratee.com/howto_node.html#build). With the following differences:

```console
git clone https://github.com/encointer/encointer-node.git
cd encointer-node
cargo build --release
```

Run dev node locally

```console
..encointer-node# ./target/release/encointer-node-teeproxy --dev --ws-port 9979 --tmp --enable-offchain-indexing true --rpc-methods unsafe
```
note that --tmp is responsible to erase all previous data and start node at block 0, offchain-indexing is needed for the custom rpc `communities_getAll`. If you don't want it, omit the flag.
`--rpc-methods unsafe` is needed for the bazaar's business and offering aggregation rpcs.

