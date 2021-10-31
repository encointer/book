# Testnet Gesell Tutorial

## Connect!

You may watch Gesell working by navigating to [polkadot.js.org/apps](https://polkadot.js.org/apps//?rpc=wss://gesell.encointer.org#/explorer). 

Gesell features regular ceremonies every 30min (Whereas the vision for mainnet is every 41 days). You can query the current ceremony phase below the menu *Developer -> Chain State*

![query phase](./fig/polkadot-js-apps-query-phase.png)

Registering means that participants can now register for the next ceremony

## How to Bootstrap an Encointer Community (automated)
To simplify the registration of a community, a python script was written to wrap up the chain client commands and automate the bootstrapping. 
We will go through the process step by step. <br>
First, setup the chain and client locally according to the [gesell local setup](gesell-local-setup). <br>

Then, install all requirements for the python scripts (you'll need pip3 for that):
```console
apt update
pip3 install --upgrade pip
pip3 install randomwords geojson pyproj flask
```
Go to the client folder and run the faucet service in a seperate terminal which will be used to insure funds for the bootstrapping accounts later on:
```console
cd client
./faucet.py
```
The faucet service is a flask app which listens on port 5000 to incoming faucet requests.

You can decide, if you want to register your community on the local chain or on the remote chain. <br>
To work with the remote chain, you need to specify the node-url (see below). <br> 
Run the following functions to register a community and let it grow on a remote chain:
```
./bot-community.py node-url wss://gesell.encointer.org init
./bot-community.py node-url wss://gesell.encointer.org benchmark
``` 
Note: the port 443 is automatically set when working with a remote chain.<br>
Equivalently, if you want to work with the local chain, remove the node-url option and specify a port, if necessary:
```
./bot-community.py --port 9945 init
./bot-community.py --port 9945 benchmark
``` 
Be sure to use the port which the local chain is listening to. If no port was configured for the local chain, you can omit the port option and the default port 9944 will be used. <br>

You can save the community icons on the local IPFS node by passing the -l or --ipfs-local flag to the init function. Otherwise, the icons will be saved on a remote IPFS node:
```
./bot-community.py --port 9945 init -l
```
Alternatively, you can manually create accounts, fund them and register a community [in this section](#how-to-register-an-encointer-community-manually)

## Let's look at what is happening in the init function:
1. Bootstrapper accounts are created. They can be seen in the ./my_keystore folder.
2. Funds are transfered to the bootstrappers via the faucet. As with other blockchains, funds are necessary in order to pay fees.
3. A specfile is created with a community name, a symbol, the icon of the community, the list of bootstrappers and several random meetup locations around a specified point.
You can see the community specfile in the client folder. It is a json file with the name of the community as filename. 
The specfile is needed to register a community. 
4. A new community is registered by passing the specfile and one of the bootstrappers (with funds) to the "new_community" function. 

Note: 
- Make sure, the faucet script is running in the background while running the bot-community script to enable the faucet.
- As you can read in our whitepaper, we'll avoid the faucet as an entry barrier in the future. 
- Should the faucet be exhausted, please post a message to our [element channel](https://app.element.io/#/room/#encointer:matrix.org) and friendly request some topup. Please be patient. 

To check, if a community is registered on the chain, you can use the client with the command "list-communities":
```console
./target/release/encointer-node-notee list-communities
```

The benchmark function calls the run function in an infinite loop, where the run function is responsible to handle the specific task depending on the phase. 

At REGISTERING, it registers participants on the chain. 
Additionally to registering participants, bootstrappers can endorse other accounts to speed up registration.
After participants are registered for a ceremony, it can be checked in a seperate shell by calling:
```bash
./target/release/encointer-node-notee list-participants
```
At ASSIGNING, the blockchain assigns all participants to randomized groups that will have to meet at a random meetup location at a specific time. Participants can learn their assignment with: 
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

Now you'll have to wait ~10min until the ceremony phase turns to ASSIGNING. The blockchain then assigns all participants to randomized groups that will have to meet at a random meetup location at a specific time. Participants can learn their assignment with:

An encointer ceremony happens at high sun on the same day all over the world. This way, no single person can attend more than one meetup. At each meetup, participants attest each others personhood. For this testnet, however, we don't care about real time or physical presence as we're testing with bot communities. See [Time Warping](./testnets.md#time-warping-for-testnets) to learn how the timing maps between mainnet and Gesell.

## See Activity in Explorer
Visit explorer.encointer.org to observe the growth of the community or set it up locally according to the [explorer](./explorer) section.
## Create Businesses and Offerings and Save the data on an IPFS node
After a community is initialized, we can register businesses with offerings on the chain. Run the following script to create and register some random businesses and offerings:
```console
./register-businesses.py 
```
You can save the businesses and offerings descriptions on the local IPFS node by passing the -l or --ipfs-local flag to the script. Otherwise, the data will be saved on a remote IPFS node.

The businesses and offerings are stored in: <br>
*./client/test-data/bazaar/businesses* <br>
and <br>
*./client/test-data/bazaar/offerings* <br>
respectively.

You can check the registered businesses using the client: 
```console
./target/release/encointer-node-notee list-businesses --cid COMMUNITY_IDENTIFIER 
```
The business offerings can be obtained with the option `--cid` and arg `Account`, which is the owner of the business. In this case we used the account `//Alice`: </br>
```console
./target/release/encointer-node-notee list-business-offerings --cid COMMUNITY_IDENTIFIER //Alice
```
Note: the cid option needs to be entered in the base58 format. </br>
## How to Register an Encointer Community (manually)
For simplicity, we'll create an alias for the chain client
```console
# Gesell node endpoint
NURL=wss://gesell.encointer.org
NPORT=443
alias nctr="./target/release/encointer-client-notee -u $NURL -p $NPORT"
```
For the local chain, remove the NURL and modify the NPORT accordingly. 
Then create some accounts (at least 3): 
```
> nctr new-account
5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
> nctr new-account
5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM
> nctr new-account
5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```
Note: the displayed account addresses will differ from yours when you follow the tutorial. They are just provided for readabiltiy. Use your account addresses instead of the ones used in the tutorial. 

Secure funds for the accounts with the faucet:
```
> nctr faucet 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG \ 5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM \ 5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```

To register a community, you need to pass a specfile.json containing details about the community and an account with funds to the chain client. 

## Meetup Locations

We need to define in what region the community shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the community. You can use geojson.io to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered communities. 

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12. 

You can list all registered communities with:

```bash
> nctr list-communities
```
Our [explorer](https://explorer.encointer.org) allows you to browse all regsitered communities and their attributes on a map.
## Trusted Setup

Every local community needs a trusted setup. A trustworthy group of 3-12 local people will hold the bootstrapping ceremony publicly. These bootstrappers need to be defined in the metadata block. 

An example of the specfile.json is shown below with one meetup locations:  
```json
{
  "type": "FeatureCollection",
  "community": {
    "meta": {
      "name": "Mediterranea",
      "symbol": "MTA",
      "icons": "QmVmew4gZHyCK2Fv4UBgsvfLdf1Q6UiF9MD6wsfPCuNVQp"
    },
    "bootstrappers": [
      "5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG",
      "5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM",
      "5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t"
    ]
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          -28.102619,
          31.141935
        ]
      },
      "properties": {}
    }
  ]
}
```
Note: replace the account addresses in "bootstrappers" with the ones you have created and add additional meetup locations.

Use one of the bootstrappers to register your new community with the specfile.json:
```bash
> nctr new-community specfile.json 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D
```
Your community has been registered and the return value is your community-identifier (cid). Let's check the registry again:

```bash
> nctr list-communities
number of communities:  1 
A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D: Mediterranea locations: 1
```

In order to bootstrap your bot community, You'll need to fund all bootstrappers and register all of them. <br>
First, check if phase is REGISTERING:

```bash
nctr get-phase
```
You should see either REGISTERING, ASSIGNING or ATTESTING. 
A phase is ~10 min long so one whole cycle is 30 min. You can jump to the next phase on the local chain by running: 
```bash
>nctr next-phase
```
Another way is to run the phase script in a seperate shell which switches phase every 10 blocks. You can add the option: --node_url wss://gesell.encointer.org to the phase script
if you are working with the remote chain.

## During Attesting

Now you have to wait for the ceremony phase to become REGISTERING. Then we can verify that our bootstrapping was successful and our bootstrappers have received their basic income issue on their accounts in units of the new currency (beware that we still use the alias including our cid. This means we're not querying ERT token balance, but the balance in your new local currency). 

```bash
> nctr balance $account1
NCTR balance for 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG is 0.99999932394375560185 in community A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D
```

Your new community has a very special property called [demurrage](./economics-demurrage.md). This means that the nominal value of your holdings decreases over time. Currently it is halving every year. You can observe this by waiting for a few blocks and checking your balance again. Think of this demurrage like a solidarity fee that you pay to the decentralized "state" that takes care of redistributing wealth among the local population at every ceremony as newly issued basic income.
## What's next?
Now that you bootstrapped your community currency, you should grow your population. At every subsequent ceremony you can add a few participants more but it is important to maintain reputation. At least 3/4 of all participants need to have attended the previous ceremony. So you can only grow your population at a pace that allows to build reputation.
