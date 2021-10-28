# Testnet Gesell Tutorial

## Connect!

You may watch Gesell working by navigating to [polkadot.js.org/apps](https://polkadot.js.org/apps//?rpc=wss://gesell.encointer.org#/explorer). 

Gesell features regular ceremonies every 30min (Whereas the vision for mainnet is every 41 days). You can query the current ceremony phase below the menu *Developer -> Chain State*

![query phase](./fig/polkadot-js-apps-query-phase.png)

Registering means that participants can now register for the next ceremony

## How to Bootstrap an Encointer Community (automated)
To simplify the creation of a community, a python script was written to wrap up the chain client commands and automate the bootstrapping. 
We will go through the process step by step. <br>
First, setup the chain and client locally according to [gesell local setup](gesell-local-setup). <br>
After the setup, make sure the chain is running and try out the client (from the root folder of the encointer-node repository):
```console
./target/release/encointer-client-notee -h
```
This should output all possible client commands. Some of them will be used within the python scripts. Be sure to specify the correct port, if the local chain is listening to a port other than 9944. 
If you want to use the client to communicate with the remote chain, add the chain url and specify the port. The following command will get the current phase from the remote chain.
```console
./target/release/encointer-client-notee -u wss://gesell.encointer.org -p 443 get-phase
```
After setting up the node and client, install all requirements for the python scripts (you'll need pip3 for that):
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

You can decide, if you want to register your community on the local chain by running the chain locally as described in [Gesell local setup](gesell-local-setup), or on the remote chain. <br>
To work with the remote chain, you need to specify the node-url (see below). <br> 
Run the following functions to register a community and let it grow on a remote chain:
```
./bot-community.py node-url wss://gesell.encointer.org init
./bot-community.py node-url wss://gesell.encointer.org benchmark
``` 
Note: the port 443 is automatically set when working with a remote chain.<br>
If you want to work with the local chain, remove the node-url option and specify a port, if necessary:
```
./bot-community.py --port 9945 init
./bot-community.py --port 9945 benchmark
``` 
Be sure to use the port which the local chain is listening to. If no port was configured for the local chain, you can omit the port option and the default port 9944 will be used. <br>

You can save the community icons on the local IPFS node by passing the -l or --ipfs-local flag to the init function. Otherwise, the icons will be saved on a remote IPFS node:
```
./bot-community.py --port 9945 init -l
```
## Let's look at what is happening in the init function:
1. Bootstrapper accounts are created. They can be seen in the ./my_keystore folder.
2. Funds are transfered to the bootstrappers via the faucet. As with other blockchains, funds are necessary in order to pay fees.
3. A specfile is created with a community name, a symbol, the icon of the community, the list of bootstrappers and several random meetup locations around a specified point.
You can see the community specfile in the client folder. It is a json file with the name of the community as filename. 
The specfile is needed to register a community. 
4. A new community is registered by passing the specfile and one of the bootstrappers (with funds). 

Note: 
- Make sure, the faucet.py script is running in the background while running the bot-community.py script to enable the faucet.
- As you can read in our whitepaper, we'll avoid the faucet as an entry barrier in the future. 
- Should the faucet be exhausted, please post a message to our [element channel](https://app.element.io/#/room/#encointer:matrix.org) and friendly request some topup. Please be patient. 

To check, if a community is registered on the chain, you can use the client with the command "list-communities":
```console
./target/release/encointer-node-notee list-communities
```

The benchmark function calls the run function in an infinite loop, where the run function is responsible to handle the specific task depending on the phase. At REGISTERING, it registers participants. At ASSIGNING it gets the meetup location, time and participants.
At Attesting it performs the meetup by getting each participants claims and attesting eachother. 

Alternatively, you can do everything within the command line in [this section](#bootstrapping-a-community-in-the-cli)

## Create Businesses and Offerings and Save the data on an IPFS node
After a community is initialized, run the following script to create some random businesses and offerings:
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
## Watch activity in explorer
Checkout the [explorer repository](https://github.com/encointer/explorer). <br>
In the terminal, go to the the root directory of the repo and enter: <br>
```console
yarn install
yarn start
```
Then you should be able to view the explorer in your browser at http://localhost:8000/. <br>
At the bottom, the registered chain is displayed. You can click on it and set the chain to be the local chain.
You can also set the rpc address via the query paramter, for example: <br>
`localhost:8000?rpc=ws://127.0.0.1:9945` will connect to the chain on localhost with port 9945. <br>
The registered community should be visible in the explorer.

Alternatively, you can visit explorer.encointer.org and configure the chain to be your local gesell chain with the predefined port.
<br>
<br>
<br>
<br>
<br>
<br>

## ALAIN CODE

## Bootstrapping a Community in the CLI
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

To register a community, you need to pass a specfile.json containing details about the community and an account with funds to the chain client to. 

Define Meetup Locations

We need to define in what region the currency shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the currency. You can use geojson.io to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered communities. 

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
Run the phase.py script in a seperate shell to see in what phase you are and how many blocks until the next phase (every 10 blocks the phase is changing). You can add the --node_url wss://gesell.encointer.org 
option if you are working with the remote chain.


If phase is REGISTERING, then:
```bash
# ok, let's register, but first we will define a few variables and a new alias
alias nctr="./target/release/encointer-client-notee -u $NURL -p $NPORT --cid A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D"
account1=5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
account2=5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM
account3=5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
nctr register-participant $account1
nctr register-participant $account2
nctr register-participant $account3
# if everything goes well, you should find your registrations here:
nctr list-participants
```
giving us

```console
listing participants for cid A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D and ceremony nr 2
number of participants assigned:  3
ParticipantRegistry[2, 1] = 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
ParticipantRegistry[2, 2] = 5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM
ParticipantRegistry[2, 3] = 5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```

Now you'll have to wait ~10min until the ceremony phase turns to ASSIGNING. The blockchain then assigns all participants to randomized groups that will have to meet at a random meetup location at a specific time. Participants can learn their assignment with:

```bash
> nctr list-meetups
listing meetups for cid A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D and ceremony nr 14
number of meetups assigned:  1
MeetupRegistry[14, 1] location is Some(Location { lat: 40.03182061333686903026, lon: 11.25 })
MeetupRegistry[14, 1] meeting time is Some(1635419700000)
MeetupRegistry[14, 1] participants are:
   5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
   5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM
   5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```

The ceremony phase will change to ATTESTING before the date of the ceremony. 

An encointer ceremony happens at high sun on the same day all over the world. This way, no single person can attend more than one meetup. At each meetup, participants attest each others personhood. For this testnet, however, we don't care about real time or physical presence as we're testing with bot communities. See [Time Warping](./testnets.html#time-warping-for-testnets) to learn how the timing maps between mainnet and Gesell.

Our bot communities can perform meetups simply with the following lines. In later networks, a mobile app will be used (similar to what we demonstrated in PoC1).

```bash
# each participant generates a claim of attendance including her vote on how many people N are actually physically present at that moment
claim1=$(nctr new-claim $account1 3)
claim2=$(nctr new-claim $account2 3)
claim3=$(nctr new-claim $account3 3)
# this claim is then sent to all other participants who will verify them and sign an attestation 
witness1_2=$(nctr sign-claim $account1 $claim2)
witness1_3=$(nctr sign-claim $account1 $claim3)
witness2_1=$(nctr sign-claim $account2 $claim1)
witness2_3=$(nctr sign-claim $account2 $claim3)
witness3_1=$(nctr sign-claim $account3 $claim1)
witness3_2=$(nctr sign-claim $account3 $claim2)
# and send that attestation back to the claimant who assembles all attestations and sends them to the chain
nctr register-attestations $account1 $witness2_1 $witness3_1
nctr register-attestations $account2 $witness1_2 $witness3_2
nctr register-attestations $account3 $witness1_3 $witness2_3
```

Now you have to wait for the ceremony phase to become REGISTERING. Then we can verify that our bootstrapping was successful and our bootstrappers have received their basic income issue on their accounts in units of the new currency (beware that we still use the alias including our cid. This means we're not querying ERT token balance, but the balance in your new local currency). 

```bash
> nctr balance $account1
NCTR balance for 5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex is 0.99999932394375560185 in currency HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
```

Your new currency has a very special property called [demurrage](./economics-demurrage.md). This means that the nominal value of your holdings decreases over time. Currently it is halving every year. You can observe this by waiting for a few blocks and checking your balance again. Think of this demurrage like a solidarity fee that you pay to the decentralized "state" that takes care of redistributing wealth among the local population at every ceremony as newly issued basic income.

## What's next?

Now that you bootstrapped your community currency, you should grow your population. At every subsequent ceremony you can add a few participants more but it is important to maintain reputation. At least 3/4 of all participants need to have attended the previous ceremony. So you can only grow your population at a pace that allows to build reputation.
