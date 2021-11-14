# Local TEEproxy setup

TEEproxy is the variant of Encointer that protects privacy. It has been deployed to [testnet Cantillon](testnet-cantillon.md)

You can run an entire Demo locally on any properly set up SGX machine. This is for advanced users or developers. The instructions assume that you are able to build substrate blockchains.
If you do not have an SGX machine to test, please check our [dockerized demo in SW mode](#docker-demo)

### Build client and worker

along the [substraTEE-worker instructions](https://www.substratee.com/howto_worker.html). With the following differences:
```console 
git clone https://github.com/encointer/encointer-worker.git
cd encointer-worker
./ci/install-rust.sh
make
```
Because the enclave cannot yet be built deterministically, you'll have to use our build if you intend to serve the same shards that we do (feel free to start new currencies on your own shard with different MRENCLAVE, but you won't be able to process the state of our/other shards):

```
cd bin
wget https://github.com/encointer/encointer-worker/releases/download/v0.6.12-sub2.0.0/enclave-0.6.12.signed.so
rm enclave.signed.so
ln -s enclave-0.6.12-devsgx02.signed.so enclave.signed.so
```
Moreover, you will need to provision secrets (the *shielding key* and the *state encryption key*) to the enclave. In the future, this will be done by workers automatically mutually, as demonstrated in [SubstraTEE M3](https://www.substratee.com/design.html#redundancy-m3-onwards).

```
# still inside ./bin
# get our symmetric state encryption key
wget https://github.com/encointer/encointer-worker/releases/download/v0.6.10-sub2.0.0-alpha.7/aes_key_sealed.bin
# get our RSA shielding key
wget https://github.com/encointer/encointer-worker/releases/download/v0.6.10-sub2.0.0-alpha.7/rsa3072_key_sealed.bin
```

### Build node

along the [substraTEE-node instructions](https://www.substratee.com/howto_node.html#build). With the following differences:

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
export RUST_LOG=info,substrate_api_client=warn,sp_io=warn,ws=warn,encointer_worker=info,substratee_worker_enclave=debug,sp_io::misc=debug,runtime=debug,substratee_worker_enclave::state=warn,substratee_stf::sgx=debug
./encointer-worker -p 9979 run
```

## Docker Demo

To avoid the need for an SGX machine, we provide a dockerized demo in SW mode as well (with emulated SGX)
```
mkdir test
cd test
docker pull scssubstratee/substratee_dev:1804-2.12-1.1.3-001

docker run -it -v $(pwd):/root/work -p 9979:9944 -p 2079:2000 -p 3079:3443 scssubstratee/substratee_dev:1804-2.12-1.1.3-001 /bin/bash

cd work
```

Please observe that we are mapping the api ports to the host system. this way, you can expose the encointer demo to your home network and access it with our [mobile app](./app.md) too.

We suggest to use tmux in docker to split your docker bash into 3 terminals. minimal cheatsheet:
* `Ctrl-B "` to split into one more terminal
* `Ctrl-B <arrows>` to switch focus to another terminal
* `Ctrl-B d` detatch session. re-attach with `tmux a`

### building

in terminal 1 do
```
git clone https://github.com/encointer/encointer-node.git
cd encointer-node
git checkout sgx-master
cargo build --release
export RUST_LOG=INFO,parity_ws=WARN,encointer=debug
./target/release/encointer-node-teeproxy --dev --ws-external -lencointer=debug,runtime=debug
```
Your chain should now start producing blocks.

in terminal 2 do
```
git clone https://github.com/encointer/encointer-worker.git
cd encointer-worker
SGX_MODE=SW make
cd bin
./encointer-worker signing-key
./encointer-worker shielding-key
./encointer-worker init-shard
./encointer-worker mrenclave > ~/mrenclave.b58
export RUST_LOG=debug,substrate_api_client=warn,sp_io=warn,ws=warn,encointer_worker=info,substratee_worker_enclave=info,sp_io::misc=debug,runtime=debug,substratee_worker_enclave::state=warn,substratee_stf::sgx=info,chain_relay=warn,rustls=warn,encointer=debug
touch spid.txt key.txt
./encointer-worker --ws-external run --skip-ra 
```
Your worker should sync blocks now.

Now you have a running local Encointer system.

### run a bot community

in terminal 3 do
```console
cd encointer-worker/client/
MRENCLAVE=$(cat ~/mrenclave.b58)
nano bot-community.py
```
now edit the following lines to match your setup

```
cli = ["./encointer-client"]
...
MRENCLAVE = "<your mrenclave here>"
```
save and exit with Ctrl-X

```console
apt update
apt install python3-geojson python3-pyproj
./bot-community.py init
./bot-community.py benchmark
```
now you can see how your bots register for ceremonies and get a UBI. More and more bots join the community for every ceremony.
