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

## Mainnet Faucets

You can use the same tools and commands as explained above, but here we'll describe more accessible ways as an altrernative

### create faucet

Create a faucet called "PioneerPot" with 10 KSM initial balance and 100mKSM drip per cycle attendance. Another 10 KSM will be reserved as a deposit for the faucet and will be returned upon dissolving the faucet once it's empty

[create faucet using js/apps](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fkusama.api.encointer.org#/extrinsics/decode/0x42002850696f6e656572506f7400a0724e1809000000000000000000000000e87648170000000000000000000000)

This will yield an Event `encointerFaucet.FaucetCreated` which also mentions the faucet's account holding the funds: 

PioneerPot: `GDcRrT5id2uFsYaE8NgfpnUkNKJpBWU4C2f5EFZQZiVZb9c`

### Drip Faucet

Use our Encointer Wallet app to drip. 

1. Go to profile -> your account
2. find *PioneerPot* below *benefits*
3. If you have personhood reputation and unclaimed allowance, the *claim* button will be enabled. tap it
4. 100 mKSM will be dripped to your account
5. the wallet will show your new KSM balance

The Encointer Wallet does not provide functionality to use KSM for other purposes than paying fees. If you'd like to use your KSM, please export your account and import it into one oif the popular wallets out there:

1. See [Backing up and restoring account](https://encointer.org/encointer-app/)
2. Import into a [Polkadot wallet](https://wiki.polkadot.network/docs/wallets-and-extensions)
3. Find your KSM balance and use your dripped KSM!

### Refill

Simply send KSM to the faucet's address.

### Monitor Usage

Of course, we'd like to know how many people are using the faucets and from which communities. You can fetch this information as follows:

First, we need to look up the *purpose id* of the faucet we're interested in

```bash
nctr-k list-purposes
# 0: ectrfct0PioneerPot
```
On mainnet, there's a single faucet right now with index `0`

```bash
nctr-k list-commitments 0 --cid u0qj944rhWE
# u0qj944rhWE, 68, 0, 5EZHGkM4NZhbR9AxnWsc9tmzNNMLQJntx1874Y7RSXKw6crw, None
# u0qj944rhWE, 68, 0, 5FLbYHux2SUmo1EB3qtmLMvdicDff4yWDZPgriJU8qqyNWVR, None
# u0qj944rhWE, 69, 0, 5Cm38saAbnwrxvnGCp4Pqff5K3rKy4awYQnrdqDeHfvW2ZUM, None
# u0qj944rhWE, 69, 0, 5EZHGkM4NZhbR9AxnWsc9tmzNNMLQJntx1874Y7RSXKw6crw, None
# u0qj944rhWE, 71, 0, 5C7ru3m9eWEEZgcYghYa5uRhUrG5EiWoRgcR2JuBLmUnKr4w, None
# u0qj944rhWE, 71, 0, 5CV1NRrFvpoma3NRDG8iZCYnTcTCuAJSUnT4ym5Wc8fL86aD, None
# u0qj944rhWE, 72, 0, 5FLbYHux2SUmo1EB3qtmLMvdicDff4yWDZPgriJU8qqyNWVR, None
```

this will only list the drips which happened withing the current reputation lifetime. To query older drips, add the `--at <block hash>` argument. When interpreting the results, be aware that drips can happen up to reputation lifetime later than the reputation being committed. 

If you want to know, how many accounts have use the faucet how many times, use this:

```bash
nctr-k list-commitments 0 --cid kygch5kVGq7 | awk -F',' '{print $4}' | sort | uniq -c | sort -nr | tee >(wc -l | awk '{print "Total unique accounts:", $1}')
#      1  5CXuNeCvhb3qbPw2TjcKYdghamJcfkq6SavKmN5fQ1beUDH5
#      2  5DceiYnUBdRx2bPjPFN2EjYm7VvaVnCB6Sp4qSjuYP4Qbd65
#      1  5Dhb315DK5T5f5Y59CsfCApv5HBb1CFzCv4zPMfsvW1t52JF
#      2  5EhLWoCguV193dDQYnTYWG21nAnYZA5R1Heedd7ejGoStBgr
#      3  5Eqm2XfZ5F18tjbDM523kTUhLrqJiXTaLsDGjKgSkrQeaU7M
#      1  5EU4mJ2tKfADXutnoJ3FsEBFsf3XxjmxLXM1xhvyGWAXh94F
#      4  5F6amYgeXNWQ7QYjvAjdiFsn2dCZ77XLp5MAFoH4GF9QacWN
#      1  5FcVT8yqHcsR59kq6fLREm4HV1Qv9hNTvDMPaWPeSJ4bCE3A
#      2  5FgcVajvdogedL4kiq4iFZ9x7Cx3ZcdCtf61qWB8fV7UP8qN
#      1  5FhB9k35bqpjc8dYrkKnMtkTVfBg9nKjgPirZWGZEZA31jQw
#      2  5FHEfaNVa82BdMDXm7NUKecy3VPThCPHycSCTqBBK9HG5nNT
#      3  5FnxT7z3mCzcNV95fEi73y6pduZzfMQ1dbBRamvWP8NfcHMi
#      4  5G3dA2sc4ytrW2NE7FLz1JTdzveXAmXii4ryiYQhTVyBAuuf
#      5  5G7AsczRMrV1kSv2btw9FSh7CwoRzaYdjaadHHZU4cFKTcfN
#      4  5GhKkMhoTHtsoxD3muqhug795znXCsiu4Tq4iBiWfpKX6e4g
#      1  5Hmi6Xr4kPeZMjRWz2aeCawdZ7riJ4Lf46VRxv8iqAwcHdNm
# Total unique accounts: 16
```
