# Perform a Testnet Cycle

This tutorial takes you through the process of performing a cycle using the CLI (without mobile app) on our [testnet Gesell](./testnet-gesell.md). If you'd like to test the same using the app, please see [Perform a cycle meetup (with app)](./app-meetup.md). This tutorial can be performed as a a single person, as we will use bot accounts. The tutorial will take an entire cycle, ~30min on Gesell.

We assume you already have 
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)

Throughout this tutorial, we will use the [Adriana community](https://explorer.encointer.org/?rpc=wss%3A%2F%2Fgesell.encointer.org) with cid `srcq45PYNyD`. Among its bootstrappers, we will work with 3 accounts: `5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw`, `5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7`, `5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR`

Your cid and bootstrapper accounts will differ.


## Import Test Accounts

In order to give the CLI access to your test bootstrapper accounts, please import them into the local keystore (unencrypted! don't use in production).
If this is the first cycle for your community, you can only use bootstrapper accounts. 

```bash
nctr-gsl new-account "<mnemonic 12 words>”
nctr-gsl list-accounts
```

## Register Participants

Registering participants can only happen during [*Registering* phase](./protocol-ceremony-cycle.md). You may have to wait max 20 min for the next Registering phase on Gesell.

```bash
nctr-gsl get-phase

nctr-gsl register-participant 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw --cid srcq45PYNyD
nctr-gsl register-participant 5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7 --cid srcq45PYNyD
nctr-gsl register-participant 5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR --cid srcq45PYNyD
nctr-gsl list-participants --cid srcq45PYNyD
```

The last command should confirm your 3 accounts have been registered. As the testnet is public and participation is unpermissioned, it could happen that someone else has registered as well.

## Check Assignments

As soon as the phase changed to *Assigning*, you can verify that your accounts have been assigned:

```bash
nctr-gsl list-meetups --cid srcq45PYNyD
```

## Perform Cycle Gathering
As soon as the phase changed to *Attesting*, we can move on. This is the moment where we pretend to be 3 different people who gather at the right time in the right location as assigned by the protocol and attest each others' personhood.

```bash
nctr-gsl attest-attendees 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw 5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7 5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR --cid srcq45PYNyD
nctr-gsl attest-attendees 5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7 5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw --cid srcq45PYNyD
nctr-gsl attest-attendees 5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw 5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7 --cid srcq45PYNyD
# verify attestations after next block
nctr-gsl list-attestees --cid srcq45PYNyD
```

## Claim Rewards

If the majority of assigned participants submitted attestations, you can already claim during *Attesting* phase, right after the gathering happened. Should only a minority have attended, you need to wait for the next *Registering* phase before you can claim.

Any participant assigned to the same gathering can trigger reqrd issuance for all attestees

```bash
nctr-gsl claim-reward --signer 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw --cid srcq45PYNyD
```

Has the community income been issued?
```bash
nctr-gsl balance 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw  --cid srcq45PYNyD
```

Verify reputation
```bash
nctr-gsl reputation 5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw
```
This command will list one row per cycle attendance with a few details which are out of scope here.

## Next Steps

Beyond getting income, reputation can now be used to 
* [drip a faucet](./tutorials-faucets.md)
* [govern the community democratically](./tutorials-democracy.md)
