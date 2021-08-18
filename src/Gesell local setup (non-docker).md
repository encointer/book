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
./target/release/encointer-node-notee --dev --tmp --enable-offchain-indexing true --ws-port 9945 --rpc-methods unsafe
```
note that --tmp is responsible for erasing all previous data and start node at block 0, offchain-indexing is needed for the custom rpc `communities_getAll` and
`--rpc-methods unsafe` is needed for the bazaar's business and offering aggregation rpcs.
The node is by default listening on port 9944, but you can add the option --ws-port xxxx to configure the node to listen to port xxxx.

## Initialize and populate bot-community
```console
cd client
apt update
pip3 install --upgrade pip
pip3 install randomwords geojson pyproj
./bot-community.py --port 9945 init
./bot-community.py --port 9945 benchmark
```

Be sure to pass the same port to the python script that was configured for the node to listen to. If no port configured, than you can ammend the port and the default port 9944 is used.

To check, if a community is registered on the node, you can use the client with the command "list-communities": 
./target/release/encointer-node-notee list-communities

You can see all available commands using the "--help" argument
## Create Business and Save on IPFS local node
After the bot-community is initialized and the benchmark is running, run the following script to create some random businesses and offerings:
```console
./register-businesses.py 
```
the businesses and offerings are stored in:
'./test-data/bazaar/businesses' 
'./test-data/bazaar/offerings'

You can check the registered businesses using the client: 
./target/release/encointer-node-notee list-businesses --cid COMMUNITY_IDENTIFIER
And the business-offerings with the option --cid and arg Account: 
./target/release/encointer-node-notee list-businesses --cid COMMUNITY_IDENTIFIER //Alice
Note that the community identifier recieved from the client is in hex format but the cid option needs to be entered in the base58 format.

## Watch activity in explorer
