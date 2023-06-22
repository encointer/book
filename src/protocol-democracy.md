# Democracy

> *this feature is work in progress and not yet released. See [Governance](./decentralization-governance.md) to learn how it currently works*

The democracy module will bring decentralized governance to Encointer facilitating participants to take decisions. Such a **universal human suffrage** (one person one vote) governance shall render the current Encointer council obsolete.  Examples of such decisions are the addition of new meetup locations to a community, an adjustment of the Demurrage rate or changes in the ceremony schdeule. 

The decision making process should follow the subsidiarity principle, meaning that decisions should be taken on the lowest possible level. So for example, if a community wants to extend their region by adding some new meetup locations, only community members should be allowed to participate in the vote.

## Scope of Democracy

This section describes the powers of Encointer's onchain democracy and at what level decisions are to be made.

### Protocol Changes

Changes to the Encointer protocol are out of scope because they need to be decided by Kusama Relay-Chain Governance as Encointer is a common good parachain. Upgrades to the Encointer Protocol must pass a public [referendum on Kusama](https://guide.kusama.network/docs/learn-governance/#referenda), where KSM token holders decide.

### Global Actions

These actions can only be decided upon by the quorum of all encointer communities globally

* Adjust ceremony schedule (can be adjusted anytime)
   * [`next_phase`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/scheduler/src/lib.rs#L151): force progress to next ceremony phase
   * [`push_by_one_day`](push_by_one_day): postpone next phase change by one day push_by_one_day
   * [`set_phase_duration`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/scheduler/src/lib.rs#L173): adjust ceremony schedule phase durations
   * [`set_next_phase_timestamp`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/scheduler/src/lib.rs#L184): arbitrarily define the time for next phase change
* Manage communities (can only be enacted during *Registering* phase)
   * [`new_community`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L76): Register new communities
   * [`purge_community`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L299): well...
* Manage Ceremony Parameters (may only make sense to enact during *Registering* phase)
   * [`set_min_solar_trip_time_s`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L279): security parameter to calculate minimal location distance
   * [`set_max_speed_mps`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L289): security parameter defining the maximal speed over ground of an adversary
   * [`set_inactivity_timeout`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L386): define how many ceremonies a community can be idle before getting purged
   * [`set_endorsement_tickets_per_bootstrapper`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): define how many endorsement tickets bootstrappers should get to invite people they trust
   * [`set_reputation_lifetime`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): define how long proof-of-personhood reputation is valid for and stored
   * [`set_meetup_time_offset`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): finetune meetup time difference to high sun
* Decide on Treasury Proposals

### Community Actions

These actions can be decided per community for themselves

Only to be changed during *Registering* phase
   * [`add_location`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L143): add meetup location for community
   * [`remove_location`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L189): remove meetup location for community
   * [`update_nominal_income`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L258): the amount of basic income per ceremony per person per community

Not strictly related to a particular ceremony phase. could be adjusted anytime.
   * [`update_community_metadata`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L214): change name, currency, artwork IPFS cid for community
   * [`update_demurrage`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L238): change how fast balances are demurraged per community

## Proposals

There is a set of predefined proposal *actions* that can be voted on (ie. set basic income to XY)

Everyone can start a proposal on an *action* (ie. set basic income to 48 LEU)

Every member of the community can use their reputation to vote on a proposal (the more reputation, the more voting power. Capped at *reputation_lifetime*)

A proposal gets approved if it is in a passing state (enough aye votes) for a long enough time period (confirmation period)

There can be multiple proposals up for vote simultaneously, even on the same action

When a proposal gets approved, all other proposals on the same *action* get cancelled, to avoid conflicts

When a proposal A gets approved, its enactment will be scheduled to the beginning of the next ceremony cycle. Should another proposal B get approved after proposal A’s approval but before it’s enactment, proposal B will be scheduled for enactment and proposal A will be cancelled.

![Proposal state machine](./fig/democracy-proposal-state-machine.drawio.svg)

### Proposal Lifetime

The following examples will describe examples of how proposals change their state over time based on a changing number of votes and on other proposals.

Let

* Confirmation Period = 3 units,
* Proposal Lifetime = 12 units,
* X/Y denote X aye votes and Y total votes,
* O = Ongoing,
* C = Confirming,
* A = Approved,
* X = Cancelled.

For the sake of simplicity, we assume that just a simple majority is needed for a proposal to pass and there is no minimum vote required.

In the case of multiple proposals, all proposals shall be of the same action.

![Democracy Voting](./fig/democracy-voting.drawio.svg)

## Voting

### Eligible Reputations

We currently allow only reputations older that the ceremony cycle of the proposal start - 2 to participate in the vote. This is because the count of those reputations is not subject to change anymore. We need a reliable count of all eligible reputations in order to determine the maximum amount of possible votes, which is required 1. for AQB and 2. to determine the minimum turnout.

If we want to relax this in the future, we would need to come up with a way to handle the dynamic change of the electorate while a proposal is running. This is not trivial.

### Adaptive Quorum Biasing (AQB) and Minimum Approval

In order to determine if a vote is passing, we use Positive Turnout Bias. In addition we enforce a minimum turnout of 5%.

