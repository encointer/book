# Bot Communities

Bot communities are helpful to test the Encointer protocol and all the surrounding applications, apps and UI's

To simplify the registration of a community, a python script was written to wrap up the chain client commands and automate the bootstrapping.
We will go through the process step by step.

If you'd rather learn how to register communities manually, visit [this section](#how-to-register-an-encointer-community-manually)

## Setup

install all requirements for the python scripts (you'll need pip3 for that):
```console
apt update
pip3 install --upgrade pip
pip3 install randomwords geojson pyproj flask
```

### Local Testing

Setup the chain and client locally according to the [local setup](developers-notee-local-setup.md). 

Run a faucet. Go to the client folder and run the faucet service in a seperate terminal which will be used to drip funds to the bootstrapping accounts later on:

```console
cd client
./faucet.py
```
The faucet service is a flask app which runs on localhost and listens on port 5000 to incoming faucet requests.

In a local setup, we don't want to wait for the ceremony phase to change in fixed intervals. Instead, we use the `phase` service to watch extrinsic activity and forward the ceremony phase when the chain blocks are empty for a while indicating idleness.

```console
cd client
./phase.py
```

By default, the scripts register your community on the local node listening on websocket port 9944. you can change that by adding the `--port 9945` argument
```
./bot-community.py init
./bot-community.py benchmark
``` 
The benchmark command will watch for ceremony phase to change and act upon the new phase automatically. This command will run forever.

#### Branding on IPFS

Community branding information is stored on IPFS. You can save the community icons to a IPFS node by passing the `-l` or `--ipfs-local` flag to the init function.

```
ipfs init
ipfs daemon
./bot-community.py --port 9945 init -l
```

#### Create Dummy Bazaar Businesses and Offerings and Save the data on an IPFS node

After a community is initialized, we can register businesses with offerings on the chain. Run the following script to create and register some random businesses and offerings:

```console
./register-businesses.py -l
```
You can save the businesses and offerings descriptions on the local IPFS node by passing the -l or --ipfs-local flag to the script. Otherwise, the data will be saved on a remote IPFS node.

The businesses and offerings are stored in:
* ./client/test-data/bazaar/businesses*
* ./client/test-data/bazaar/offerings*

You can check the registered businesses and offerings using the client:

```console
./target/release/encointer-client-notee list-businesses --cid COMMUNITY_IDENTIFIER 
./target/release/encointer-client-notee list-offerings --cid COMMUNITY_IDENTIFIER 
```

The business offerings can be obtained with the option `--cid` and arg `Account`, which is the owner of the business. In this case we used the account `//Alice`:

```console
./target/release/encointer-node-notee list-business-offerings --cid COMMUNITY_IDENTIFIER //Alice
```
Note: the cid option needs to be entered in the base58 format.

### Testnet Gesell Bot Communities

As community branding must be stored on IPFS, you'll need to specify credentials for a public IPFS gateway to be used to deploy content. For [Infura](https://infura.io), use:

```
export IPFS_ADD_URL=https://ipfs.infura.io:5001/api/v0/add
export IPFS_API_KEY=<project-id:password>
```

To work with testnet Gesell you need to specify the node-url as follows:
```
./bot-community.py --node-url wss://gesell.encointer.org init
``` 
*Note: the port 443 is automatically set when working with a remote chain using `wss://`*

On Gesell, ceremony phases change every 10min. In order to continuously grow a bot community, 
run the following command every 10min

```
./bot-community.py --node-url wss://gesell.encointer.org run
```

## Explanations

Let's look at what is happening in the init function:

1. Bootstrapper accounts are created. They can be seen in the ./my_keystore folder.
2. Funds are transfered to the bootstrappers via the faucet. As with other blockchains, funds are necessary in order to pay fees.
3. A specfile is created with a community name, a symbol, the icon of the community, the list of bootstrappers and several random meetup locations around a specified point.
   You can see the community specfile in the client folder. It is a geojson file with the name of the community as filename.
   The specfile is needed to register a community.
4. A new community is registered by passing the specfile and one of the bootstrappers (with funds) to the "new_community" function.

Note:
- Make sure, the faucet script is running in the background while running the bot-community script to enable the faucet.
- As you can read in our whitepaper, we'll avoid the faucet as an entry barrier in the future.
- Should the faucet be exhausted, please post a message to our [element channel](https://app.element.io/#/room/#encointer:matrix.org) and friendly request some topup. Please be patient.

To check if a community is registered on the chain, you can use the client with the command "list-communities":

```console
./target/release/encointer-node-notee list-communities
```

The benchmark function calls the run function in an infinite loop, where the run function is responsible to handle the specific task depending on the phase.

At REGISTERING, it registers participants on the chain.
Additionally to registering participants, bootstrappers can endorse other accounts to speed up registration.
After participants are registered for a ceremony, the participatns can be verified in a seperate shell by calling:
```bash
./target/release/encointer-node-notee list-participants
```
At ASSIGNING, the blockchain assigns all participants to randomized groups that will have to meet at a random meetup location at a specific time.
Participants can learn their assignment with:
```bash
./target/release/encointer-node-notee list-meetups
```
At ATTESTING, the run function performs the meetup by getting each participants claims and attesting eachother.

To know in what phase the chain is, you can call:
```bash
./target/release/encointer-node-notee get-phase
```
You should see either REGISTERING, ASSIGNING or ATTESTING.

A phase is ~10 min long so one whole cycle is 30 min. You can jump to the next phase on the local chain by running:
```bash
./target/release/encointer-node-notee next-phase
```
Another way is to run the phase script in a seperate shell which switches phase every 10 blocks. You can add the option: --node_url wss://gesell.encointer.org to the phase script if you are working with the remote chain.

To define in what region the community shall be issued, we use the geojson standard to define a set of meetup places and add some meta-information about the community. You can use geojson.io to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered communities.

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12.

Every local community needs a trusted setup. A trustworthy group of 3-12 local people will hold the bootstrapping ceremony publicly. These bootstrappers need to be defined in the metadata block of the specfile.

At every subsequent ceremony you can add a few participants more but it is important to maintain reputation. At least 3/4 of all participants need to have attended the previous ceremony. So you can only grow your population at a pace that allows to build reputation.

An encointer ceremony happens at high sun on the same day all over the world. This way, no single person can attend more than one meetup. At each meetup, participants attest each others personhood. For this testnet, however, we don't care about real time or physical presence as we're testing with bot communities. See [Time Warping](./deployments.md#time-warping-for-testnets) to learn how the timing maps between mainnet and Gesell.

Your new community has a very special property called [demurrage](./economics-demurrage.md). This means that the nominal value of your holdings decreases over time. Currently it is halving every year. You can observe this by waiting for a few blocks and checking your balance again. Think of this demurrage like a solidarity fee that you pay to the decentralized "state" that takes care of redistributing wealth among the local population at every ceremony as newly issued basic income.




## See Activity in Explorer

Visit explorer.encointer.org to observe the growth of the community or run the explorer locally according to the [explorer](./explorer) section.

## Browse Bazaar Businesses and Offernings 

See [Bazaar-web](https://github.com/encointer/bazaar-web) repo and follow README to launch a web service that lists all businesses and offerings for a specified community
