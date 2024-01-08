### Democracy Tutorial

In this tutorial, we show a basic flow for two proposals in the democracy module using the CLI. On proposal will be approved and the other one will be rejected.

Go to the encointer-node repo and open 2 terminals:

Terminal 1:

```
git clone https://github.com/encointer/encointer-node.git
cd encointer-node && git fetch && git checkout origin/personhood-democracy-1-delivery
cd ..
git clone https://github.com/encointer/pallets.git
mv pallets/ encointer-pallets
cd encointer-pallets && git fetch && git checkout origin/polkadot-v1.0.0-pallets-v1.3.0-democracy
cd ../encointer-node

cargo build --release
./target/release/encointer-node-notee --dev --enable-offchain-indexing true -lencointer=debug,parity_ws=warn
```

Terminal 2:

```
cd client
python -m venv env
source env/bin.activate
pip install requirements.txt
python bootstrap_demo_community.py
```

Continuing in Terminal 2, we first move to the start of the next cycle, in order for the reputations of the last cycle to become eligible for voting:

```
../target/release/encointer-client-notee next-phase
../target/release/encointer-client-notee next-phase
../target/release/encointer-client-notee next-phase
../target/release/encointer-client-notee listen -b 1
```

After that, we submit two porposals for voting:

```
# Submitting proposal id 1, SetInactivityTimeout(8)
../target/release/encointer-client-notee submit-set-inactivity-timeout-proposal //Alice 8
../target/release/encointer-client-notee listen -b 1
# Submitting proposal id 2, UpdateNominalIncome(cid, 44)
../target/release/encointer-client-notee --cid sqm1v79dF6b submit-update-nominal-income-proposal //Alice 44
```

We wait for the extrinsics to be in a block and list the proposals:

```
../target/release/encointer-client-notee listen -b 1
../target/release/encointer-client-notee list-proposals
```

We expect to see the following output:

```
id: 1
state: ProposalState::Ongoing
action: ProposalAction::SetInactivityTimeout(8)
start time: 1704685908002
start cindex: 3
electorate size: 3
turnout: 0
ayes: 0
id: 2
state: ProposalState::Ongoing
action: ProposalAction::UpdateNominalIncome(sqm1v79dF6b, 44)
start time: 1704685920000
start cindex: 3
electorate size: 3
turnout: 0
ayes: 0
```

As an exmaple we show that Alice has 1 reputation and therefore a voting power of 1:
```
../target/release/encointer-client-notee reputation //Alice
```
which will yield:
```
1, sqm1v79dF6b, Reputation::VerifiedUnlinked
```
The same will hold for Bob and Charlie.  


Then, we let the users vote for the porposals:

```
# Alice votes aye for proposal 1
../target/release/encointer-client-notee vote //Alice 1 aye sqm1v79dF6b_1
# Alice votes again aye for proposal 1, this vote will not count as she has already voted
../target/release/encointer-client-notee vote //Alice 1 aye sqm1v79dF6b_1
# Bob votes aye for proposal 1
../target/release/encointer-client-notee vote //Bob 1 aye sqm1v79dF6b_1
# Charlie votes aye for proposal 1
../target/release/encointer-client-notee vote //Charlie 1 aye sqm1v79dF6b_1
# Alice votes nay for proposal 2
../target/release/encointer-client-notee vote //Alice 2 nay sqm1v79dF6b_1
# Bob votes nay for proposal 2
../target/release/encointer-client-notee vote //Bob 2 nay sqm1v79dF6b_1
# Charlie votes aye for proposal 2
../target/release/encointer-client-notee vote //Charlie 2 aye sqm1v79dF6b_1
```

Now, we wait for 5 blocks, as out confirmation period is 10 blocks and we already waited 6 blocks during voting:

```
../target/release/encointer-client-notee listen -b 5
```

Now, we need to update the proposal state and check again the proposals and the enactment queue:

```
# Alice updates proposal state of proposal 1
../target/release/encointer-client-notee update-proposal-state //Alice 1
../target/release/encointer-client-notee list-proposals
../target/release/encointer-client-notee list-enactment-queue
```

For the proposals, we expect proposal 1 to be Approved:

```
id: 1
state: ProposalState::Approved
action: ProposalAction::SetInactivityTimeout(8)
start time: 1704685908002
start cindex: 3
electorate size: 3
turnout: 3
ayes: 3
id: 2
state: ProposalState::Ongoing
action: ProposalAction::UpdateNominalIncome(sqm1v79dF6b, 44)
start time: 1704685920000
start cindex: 3
electorate size: 3
turnout: 3
ayes: 1
```

And proposal 1 should be in the enactment queue, which means that it is scheduled for enactment at the start of the next cycle:

```
1
```

We wait for another 10 blocks, such that the proposal lifetime of 20 blocks for proposal 2 elapses and we expect proposal 2 to be cancelled:

```
# Waiting 10 blocks...
../target/release/encointer-client-notee listen -b 10
# Alice updates proposal state of proposal 2
../target/release/encointer-client-notee update-proposal-state //Alice 2
../target/release/encointer-client-notee list-proposals
```