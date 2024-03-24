# Democracy Tutorial

Encointer is about distribution of power - both economically and in terms of governance. Therefore, communities can govern their own protocol parameters with an approximation of universal suffrance. This tutorial takes you through the process of proposing a change and voting on proposals.

We assume you already have 
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)
* [performed a virtual cycle gathering](./tutorials-perform-cycle.md)

## What can you vote on?

Please read our [democracy documentation](./protocol-democracy.md) to learn the details. In this tutorial, we will focus on [local community governance](./protocol-democracy.md#community-actions), like changinging the community income amount issued per cycle.

## Who can vote

Every reputable can vote on matters in the community where they have repuation. The current implementation grants reputables one vote per cycle attendance. This is a sybil-resilient approximation of universal suffrance and it gives those more voting power who engage more in cycles. 

In the following, we will assume that one person only maintains one account. The protocol is sybil-resilient even if that's not the case, but the tutorial will be easier to follow with this assumption.

### Quorum

Let's find out how many votes can maximally be cast for our community

First, how many different accounts have reputation?

```bash
nctr-gsl list-reputables

Listing the number of attested attendees for each community and ceremony for cycles [4607:4943]
...
Community ID: srcq45PYNyD
...
Cycle ID 4942: Total attested attendees: 3 (noshows: 0)
Cycle ID 4943: Total attested attendees: 3 (noshows: 1)
Reputables in srcq45PYNyD (unique accounts with at least one attendance) 4
```
Our test community has 4 reputable members who are eligible to vote. The quorum of votes will be the sum of attested attendances, in this case 6. Two accounts will have a voting power of 2, because they attended both cycles 4942 and 4943. Another two accounts have a voting power of 1 

Reputation becomes eligible for voting with 1 cycle delay. If you came here straight after the previous tutorial, you may need to wait for one cycle (30min on Gesell) for your fresh reputation to become eligible.

Reputation loses its voting power after [ReputationLifetime](./protocol-reputation.md#reputation-lifetime)

For later reference, we list the 4 reputables here with their eligible votes:
* `5F77sGnUhpjdFnzruhurGZgqPFtvdECXTGgX4Bgy2zGavbEw` (bootstrapper with 1 vote) 
* `5FyWbcwN1TGPdyzRzoEeem3MUcc7jXRs7ZoftZkAQLV47nS7` (bootstrapper with 2 votes)
* `5CMVLJCC4Jn7QmLsFRkLWkm2w4LJswpZo1V2nd1tp64iVnCR` (bootstrapper with 2 votes)
* `5FH44YdjmxbXJCAn9DuwpXuz5h2S8zLn752Vn5CyDa3quwEs` (reputable with 1 vote)


## Submit Proposals

Anyone can submit a proposal anytime. See the [docs](./protocol-democracy.md#proposals) to learn about conflict resolution among proposals.

```bash
nctr-gsl submit-update-nominal-income-proposal 5FH44YdjmxbXJCAn9DuwpXuz5h2S8zLn752Vn5CyDa3quwEs 3.14 --cid srcq45PYNyD
nctr-gsl list-proposals
```
Let's inspect the details of our proposal
```
id: 3
action: ProposalAction::UpdateNominalIncome(srcq45PYNyD, 3.14000000000000012434)
started at: 2024-03-24 15:58:48 UTC
ends after: 2024-03-24 16:18:48 UTC
start cindex: 4954
state: ProposalState::Ongoing
```
So now we have at most 30min time to vote. 

## Vote

Let's check our voting power:
```bash
nctr-gsl reputation 5FH44YdjmxbXJCAn9DuwpXuz5h2S8zLn752Vn5CyDa3quwEs
4943, srcq45PYNyD, Reputation::VerifiedUnlinked
```
We have one vote in community srcq45PYNyD with our reputation from cindex 4943. We need this information to cast our vote:

```bash
nctr-gsl vote 5FH44YdjmxbXJCAn9DuwpXuz5h2S8zLn752Vn5CyDa3quwEs 3 aye srcq45PYNyD_4943
nctr-gsl list-proposals
```

The proposal will immediately enter the confirming phase which lasts 5min

```
id: 3
action: ProposalAction::UpdateNominalIncome(srcq45PYNyD, 3.14000000000000012434)
started at: 2024-03-24 15:58:48 UTC
ends after: 2024-03-24 16:18:48 UTC
start cindex: 4954
state: ProposalState::Confirming { since: 1711296000000 }
confirming since: 2024-03-24 16:00:00 UTC until 2024-03-24 16:00:00 UTC
```
If no one else votes, it will be approved because in this case our vote is already more than 5% of the electorate. Please check [our docs on adaptive quorum biasing](./protocol-democracy.md#adaptive-quorum-biasing-aqb-and-minimum-approval)

Proposals are lazily evaluated. after the end of the confirming phase you can call

```bash
nctr-gsl update-proposal-state 5FH44YdjmxbXJCAn9DuwpXuz5h2S8zLn752Vn5CyDa3quwEs 3
nctr-gsl list-proposals
```

```
id: 3
action: ProposalAction::UpdateNominalIncome(srcq45PYNyD, 3.14000000000000012434)
started at: 2024-03-24 15:58:48 UTC
ends after: 2024-03-24 16:18:48 UTC
start cindex: 4954
state: ProposalState::Approved
```

All approved proposals will be enacted automatically at the start of the next *Registering* phase. Let's check the enactment queue:

```bash
nctr-gsl list-enactment-queue
3
```

This returns all proposal id's which will be enacted. Please be aware that even approved proposals can be cancelled before enactment if another proposal for the same action passes before enactment.

After the start of the next *Registering* phase, let's verify the enactment:
```bash

```
