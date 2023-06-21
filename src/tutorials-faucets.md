# Faucets

The Encointer faucet is a dispenser of arbitrary tokens. Every human is eligible to receive a small amount of tokens for every attendance in the Encointer protocol. This can be seen as an additional benefit of participating in the encointer protocol and as an onboarding route for new tokens. 

Currently, the faucet only supports the native ERT token on our testnet Gesell and will support KSM as soon as deployed on our mainnet

## Drip

We assume you have [set up the CLI client previously](./tutorials-cli.md) and you have [bootstrapped your own community](./tutorials-register-community.md) with a few bootstrapper accounts

Let's learn how you can request to receive ERT.

First, let's check what faucets exist on Gesell already:

```bash
> nctr-gsl list-faucets -v
address: 5Fy41AupevjQeURN1vVYyqJCjqXLvqPHCUhoRerqEEfVQQLt
name: Testfaucet2
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 100000000000000
drip amount: 1000000000000
whitelist:
eymbj5rgujf

address: 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm
name: Testfaucet3
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 99000000000000
drip amount: 1000000000000
whitelist: None
```

In our example, two faucets exist and they have the following properties:
* an *address* which holds the funds
* a *drip amount* specifying how much ERT you can get for one cycle attendance
* an optional *whitelist* of communities which are eligible for this faucet 

Let's now assume, `//Bob` has attended cycle 6 in community `sqm1v79dF6b` and hasn't dripped the faucet yet.

```bash
> nctr-gsl drip-faucet //Bob 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm 6 --cid sqm1v79dF6b
Faucet dripped to 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
```
Bob now has received 1 ERT. If we try to drip again with the same reputation, this will fail:

```
> nctr-gsl drip-faucet //Bob 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm 6 --cid sqm1v79dF6b
[+] Couldn't execute the extrinsic due to Dispatch(Module(ModuleError { pallet: "EncointerReputationCommitments", error: "AlreadyCommited", description: ["Participant already commited their reputation for this purpose"], error_data: ModuleErrorData { pallet_index: 65, error: [0, 0, 0, 0] } }))
```
Therefore, Bob needs to attend another cycle before he can drip again. 
## Replenish a Faucet

If the faucet runs dry, anyone can send ERT to the faucet's address to fill it up again

```
> nctr-gsl transfer //Alice 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm 30000000000000
balance for 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm is now 128000000000000
> nctr-gsl list-faucets -v
...
address: 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm
name: Testfaucet3
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 128000000000000
...
```

## Create a new faucet

If you want to donate tokens to a specific set of communities, you can create your own faucet with an opional whitelist

```
> nctr-gsl  create-faucet //Alice Testfaucet4 100000000000000 1000000000000 sqm1v79dF6b
5C8zTwdZtCYdrxTzhxuVEAyL1zQKagfFqHuaPrJ59RUftjvg
```

This command creates a faucet with name *TestFaucet4* and sends funds from Alice to the new faucet account and configures the drip amount. The return value is the faucet's account

If you do not specify a whitelist, every community will be eligible to drip. 

The new faucet will now appear in the global list of faucets:

```
> nctr-gsl list-faucets -v
address: 5C8zTwdZtCYdrxTzhxuVEAyL1zQKagfFqHuaPrJ59RUftjvg
name: Testfaucet4
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 100000000000000
drip amount: 1000000000000
whitelist:
sqm1v79dF6b

address: 5Fy41AupevjQeURN1vVYyqJCjqXLvqPHCUhoRerqEEfVQQLt
name: Testfaucet2
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 100000000000000
drip amount: 1000000000000
whitelist:
eymbj5rgujf

address: 5HkReqcuDYcdZFEJzzy8ACATmx71nbNHunpPT6Vc3q4Y44nm
name: Testfaucet3
creator: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
balance: 128000000000000
drip amount: 1000000000000
whitelist: None

```

