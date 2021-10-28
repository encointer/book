# Gesell local setup
Note: You can find more detailed instructions on how to build and run the node on the [encointer-node repository](https://github.com/encointer/encointer-node).

### Build node 
Clone the [encointer-node repository](https://github.com/encointer/encointer-node) and cd into it:
```console
git clone https://github.com/encointer/encointer-node.git
cd encointer-node
```
Install Rust: 
```console
curl https://sh.rustup.rs -sSf | sh
```
Install required tools:
```console
./scripts/init.sh
```
Build the node:
```console
cargo build --release
```
## Launch Gesell node
Run dev node locally

```console
./target/release/encointer-node-notee --dev --tmp --enable-offchain-indexing true --ws-port 9945 --rpc-methods unsafe
```
`--dev` sets up a developer node [chain specification](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec) </br>
`--tmp` saves all active data for the node (keys, blockchain database, networking info, ...) and is deleted as soon as you properly terminate your node. </br>
`--offchain-indexing` is needed for the custom rpc call `communities_getAll` </br>
`--rpc-methods unsafe` is needed for the bazaar's business and offering aggregation rpcs. </br>
The node is by default listening on port 9944, but you can add the option `--ws-port xxxx` to configure the node to listen to port xxxx.