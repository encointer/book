# Community Treasury Spending Tutorial

Encointer Communities can manage common funds in community treasuries and decide on spending proposals democratically.

We assume you already have
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)
* [performed a virtual cycle gathering](./tutorials-perform-cycle.md)
* [submitted and voted on democracy proposals](./tutorials-democracy.md)

## What is a Community Treasury?

A community treasury is a technical account which isn't controlled by a private key held by a person or entity. Every Encointer community has their own treasury account and can decide on spending proposals democratically - the electorate being the reputables of that community.

### Where does the money come from?

In the future, community treasuries will receive a share of network fees and [faucet](./tutorials-faucets.md) drips. However, it will take quite large populations to generate significant income from these sources.

Initially, treasuries are intended for grants and donations. Donors can transfer funds directly to Encointer communities and rest assured that the funds will be spent according to the community's democratic decision without trusting any intermediaries.

## Testnet Gesell Tutorial

### fund the treasury

First, we need to find out the treasury account address of our community. This is the account that will receive the funds. Please replace the CID with your community's CID.

```bash
nctr-gsl get-treasury --cid sqm1v79dF6b
# 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem
nctr-gsl transfer //Alice 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem 1000000000000
nctr-gsl balance 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem
# 1000000000000
```

### submit a spending proposal

We will let Alice propose a spend of 0.123 ERT to Bob. 
Replace Alice and Bob with accounts you control. 

```bash
nctr-dev submit-spend-native-proposal //Alice //Bob 123456789012 --cid sqm1v79dF6b
nctr-dev list-proposals
```

Verify that your proposal is listed and the electorate is correct. Remember that reputation is only counted for the electorate after one full cycle has passed.

Please remember your proposal ID for later reference.

### vote on a spending proposal

This is best done with the [mobile app](./tutorials-democracy.md#vote-using-the-mobile-app) because it is much easier. For cli enthusiasts, here's how to vote with the cli:

Alice will vote *Aye* with her reputation from cycle index 1 attendance in community sqm1v79dF6b.

```bash
nctr-gsl vote //Alice 1 aye sqm1v79dF6b_1
```
For more details how to obtain your reputation information, see the [democracy tutorial](./tutorials-democracy.md#vote).

### update the proposal

As Encointer democracy is evaluated lazily, we need to trigger vote tallying manually. This is done by updating the proposal.

```bash
nctr-gsl update-proposal-state //Alice 1
nctr-gsl list-proposals
```

If the proposal has been accepted, the enactment will be scheduled for the upcoming [assigning phase](./protocol-ceremony-cycle.md#assigning).

### verify enactment of the proposal

After the proposal has been enacted, the funds will be transferred to Bob's account. 

The treasury balance should have gone down and the beneficaries account should have gone up

```bash
nctr-gsl balance 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem
nctr-gsl balance //Bob
```



