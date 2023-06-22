# Faucets

The Encointer faucet is a dispenser of arbitrary tokens. Every human is eligible to receive a small amount of tokens for every attendance in the Encointer protocol. This can be seen as an additional benefit of participating in the encointer protocol and as an onboarding route for new tokens. 

Currently, the faucet MVP only supports the native ERT token on our testnet Gesell and will support KSM as soon as deployed on our mainnet. Our final product will support any fungible token which can be transferred via XCM to the Encointer parachain, including stablecoins

## Testnet Usage
### Drip

We assume you have [set up the CLI client previously](./tutorials-cli.md) and you have [bootstrapped your own community](./tutorials-register-community.md) with a few bootstrapper accounts

We assume one of your community member accounts (can be a bootstrapper or reputable) is `5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e`

Let's learn how you can request to receive ERT.

First, let's check what faucets exist on Gesell already:

```bash
> nctr-gsl list-faucets -v
address: 5Dq3XugU1atZM8QGHxg2KfZahm2CzuBUDp8XyxL9wn8Q8Yx3
name: FaucetNumberOne
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 100000000000000
drip amount: 1000000000000
whitelist: None
```

In our example, two faucets exist and they have the following properties:
* an *address* which holds the funds
* a *drip amount* specifying how much ERT you can get for one cycle attendance
* an optional *whitelist* of communities which are eligible for this faucet 

Let's now check our reputation
```
> nctr-gsl reputation 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e | sort
....
1744, e5dvt5mjcem, Reputation::VerifiedUnlinked
```
It looks like we have valid reputation for cycle 1744, so let's drip:

```bash
> nctr-gsl drip-faucet 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e 5Dq3XugU1atZM8QGHxg2KfZahm2CzuBUDp8XyxL9wn8Q8Yx3 1744 --cid e5dvt5mjcem
Faucet dripped to 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e
```

Our account now [has received 1 ERT](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fgesell.encointer.org#/explorer/query/0x33ee5572fa5db7adedc70c0971722dda2d5dc37a411efcd1302058c4868c1bc8). 
If we try to drip again with the same reputation, this will fail:

```
> nctr-gsl drip-faucet 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e 5Dq3XugU1atZM8QGHxg2KfZahm2CzuBUDp8XyxL9wn8Q8Yx3 1744 --cid e5dvt5mjcem
[+] Couldn't execute the extrinsic due to Dispatch(Module(ModuleError { pallet: "EncointerReputationCommitments", error: "AlreadyCommited", description: ["Participant already commited their reputation for this purpose"], error_data: ModuleErrorData { pallet_index: 65, error: [0, 0, 0, 0] } }))
```
Therefore, we need to attend another cycle before we can drip again.

### Replenish a Faucet

If the faucet runs dry, anyone can send ERT to the faucet's address to fill it up again

```
> nctr-gsl transfer //Sponsor 5Dq3XugU1atZM8QGHxg2KfZahm2CzuBUDp8XyxL9wn8Q8Yx3 30000000000000
```

### Create a new faucet

If you want to donate tokens to a specific set of communities, you can create your own faucet with an opional whitelist

```
> nctr-gsl create-faucet 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e FaucetForMyBuddies 100000000000000 5000000000000 e5dvt5mjcem,dpcm5272THU
5CTxhG3NJjhwti8kQRR9FYTT53Jq41We3MHdoJZa4RymAKhq
```

This command creates a faucet with name *FaucetForMyBuddies* and sends funds from to the new faucet account and configures the drip amount. The return value is the faucet's account
If you do not specify a whitelist, every community will be eligible to drip. 

Be aware that registering a faucet requires you to reserve a deposit of 100 ERT. please check `encointerFaucet.reserveAmount` to verify the amount to be reserved. 
The reserved amount will be freed upon closing the faucet. Closing the faucet is only possible once the faucet is dry.

The new faucet will now appear in the global list of faucets:

```
> nctr-gsl list-faucets -v
address: 5CTxhG3NJjhwti8kQRR9FYTT53Jq41We3MHdoJZa4RymAKhq
name: FaucetForMyBuddies
creator: 5HdLw7t5LjjZ9vSeFiYRbcJf6uFX9xqzv3QappFBy9P8pR9e
balance: 100000000000000
drip amount: 5000000000000
whitelist:
e5dvt5mjcem
dpcm5272THU

address: 5Dq3XugU1atZM8QGHxg2KfZahm2CzuBUDp8XyxL9wn8Q8Yx3
name: FaucetNumberOne
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 99000000000000
drip amount: 1000000000000
whitelist: None
```

Now, another reputable member of a whitelisted community (`e5dvt5mjcem` or `dpcm5272THU`) community could drip 5 ERT from the new faucet
