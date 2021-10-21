# Gesell local setup (non-docker)

## Launch Gesell node

### Build node 
```console
git clone https://github.com/encointer/encointer-node.git
cd encointer-node
cargo build --release
```

Run dev node locally

```console
./target/release/encointer-node-notee --dev --tmp --enable-offchain-indexing true --ws-port 9945 --rpc-methods unsafe
```
`--dev` sets up a developer node [chain specification](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec) </br>
`--tmp` saves all active data for the node (keys, blockchain database, networking info, ...) and is deleted as soon as you properly terminate your node. </br>
`--offchain-indexing` is needed for the custom rpc call `communities_getAll` </br>
`--rpc-methods unsafe` is needed for the bazaar's business and offering aggregation rpcs. </br>
The node is by default listening on port 9944, but you can add the option `--ws-port xxxx` to configure the node to listen to port xxxx.

## Initialize and populate bot-community
First, go to the client folder and install all requirements:
```console
cd client
apt update
pip3 install --upgrade pip
pip3 install randomwords geojson pyproj flask
```
Then, insure that there are sufficient funds in the //Alice account by running the faucet service:
```console
./faucet.py
```
Finally, run the following script to initialize a community and let the community grow:
./bot-community.py --port 9945 init
./bot-community.py --port 9945 benchmark
```
When benchmark is running, you can change the phase by running the ./phase.py
script in another shell.
Be sure to pass the same port to the python script that was configured for the node to listen to. If no port configured, than you can omit the port and the default port 9944 is used.

To check, if a community is registered on the node, you can use the client with the command "list-communities": 
```console
./target/release/encointer-node-notee list-communities
```
You can see all available commands using the "--help" argument
## Create Business and Save on IPFS local node
After the bot-community is initialized and the benchmark is running, run the following script to create some random businesses and offerings:
```console
./register-businesses.py 
```
The businesses and offerings are stored in:
'./test-data/bazaar/businesses' 
'./test-data/bazaar/offerings'

You can check the registered businesses using the client: 
```console
./target/release/encointer-node-notee list-businesses --cid COMMUNITY_IDENTIFIER 
```
The business offerings can be obtained with the option `--cid` and arg `Account`, in this case we use the account `//Alice`: </br>
```console
./target/release/encointer-node-notee list-business-offerings --cid COMMUNITY_IDENTIFIER //Alice
```
Note that the community identifier recieved from the client is in hex format but the cid option needs to be entered in the base58 format. </br>

## Watch activity in explorer
Checkout the [explorer repository](https://github.com/encointer/explorer). <br>
In the terminal, go to the the root directory of the repo and enter: <br>
```console
yarn install
yarn start
```
Then you should be able to view the explorer in your browser at http://localhost:8000/. <br>
At the bottom, the registered node is displayed. You can click on it and set the node to be the local node.
You can also set the rpc address via the query paramter, for example: <br>
`localhost:8000?rpc=ws://127.0.0.1:9945` will connect to the node on localhost with port 9945. <br>
The registered community should be visible in the explorer.

Alternatively, you can visit explorer.encointer.org and configure the node to be your local gesell node with the predefined port.