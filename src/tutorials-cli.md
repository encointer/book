# Basic CLI usage

The Encointer CLI is a low-level tool to interact with an Encointer chain. It allows to query state and to send extrinsics. 

## Setup

We suggest you run the following in an Ubuntu 20.04 environment

Download the CLI for our latest release:

```
wget https://github.com/encointer/encointer-node/releases/latest/download/encointer-client-notee
chmod +x encointer-client-notee

# Testnet Gesell node endpoint
NURL=wss://gesell.encointer.org
NPORT=443
alias nctr-gsl="./encointer-client-notee -u $NURL -p $NPORT"

# Testnet Lietaer (on Rococo) node endpoint
NURL=wss://rococo.api.encointer.org
NPORT=443
alias nctr-r="./encointer-client-notee -u $NURL -p $NPORT"

# Encointer Mainnet (on Kusama) endpoint
NURL=wss://kusama.api.encointer.org
NPORT=443
alias nctr-k="./encointer-client-notee -u $NURL -p $NPORT"

# local dev node
alias nctr-dev="./encointer-client-notee"
```

In the following, we will show usage with testnet Gesell, using our alias `nctr-gsl`. But You can use any of the aliases above to interact with the other chains in the same way (caveat: deployed versions of the CLI API can vary)

### Get Ceremony Phase

```
nctr-gsl get-phase
```

This will return any of 
* `Registering`: you can register participants, communities, locations 
* `Assigning`: ceremony meetup assignments can be queried
* `Attesting`: ceremony meetups can be performed

### List Communities

```
nctr-gsl list-communities
```
will yield something like 

```
number of communities:  5
e5dvt5mjcem: bot-tugs locations: 9
u0qj94fxxJ6: EdisonPaula locations: 3
srcq45PYNyD: Adriana locations: 5
u33e0719fDB: Decoded Berlin locations: 3
69y7j4ZEXmy: Decoded Buenos Aires locations: 8
```
Each community is shown with  
* its community identifier (cid) which is a 11-character string
* its Name, given by community lead
* its number of ceremony meetup locations

You can also explore communities for our different networks using our [explorer](./explorer)

### Manage Account Keystore

The CLI offers very basic wallet functionality, managing a keystore in a local subfolder `my_keystore` where account secrets are stored in plaintext. Do not use in production!

#### Create a New Account

```
nctr-gsl new-account
```
The printed result will be your new account address 

#### List Accounts in Keystore

```
nctr-gsl list-accounts
```

### Query Account Balances

```
nctr-gsl balance 5CSLXnYZQeVDvNmanYEJn4YXXhgFLKYwp2f216NsDehR8mVU
```

you can add a cid to query the balance for a specific community

```
nctr-gsl balance 5ChwkE8kd2qagyiCikP2Ns2T6vWh7dbURx54gXcPKw8NotNp --cid srcq45PYNyD
```

or get all balances (first, the native token balance, then all community balances)

```
nctr-gsl balance 5ChwkE8kd2qagyiCikP2Ns2T6vWh7dbURx54gXcPKw8NotNp --all
```

### Faucet

Testnet Gesell features a faucet, so you can pre-fund your [new account](#create-a-new-account)

```
nctr-gsl faucet <Account>
```

