# Governance

## Subsidiarity

The Encointer Network is governed on multiple levels. 
The design paradigm is subsidiarity: *issues should be dealt with at the most immediate (or local) level that is consistent with their resolution.*

### Global Protocol Scope

Encointer is a common-good parachain of the Kusama Network. The Kusama Network provides its security to Encointer, but requires that Encointer protocol updates are decided by KSM holders who granted the common-good slot in the first place and have to evaluate if the update is sound and still complies with common-good criteria. 

### Global Operative Scope

The Encointer protocol defines certain global parameters which are common to all communities. Changing such parameters requires a global decision

### Local Community Scope

Each community can decide on parameters that only concern itself. Such parameters are demurrage, nominal income per ceremony and community metadata like Logo, Name

## Council

Encointer aims at [democratic governance based on one-person-one-vote](./protocol-democracy.md). However, democracy requires a representative number of users and communities to be meaningful and legitimate.
Therefore, governance is delegated to a council until the community deems to be ready for democracy. 

The council currently consists of 7 members of the Encointer Association. Each local community may elect a representative which shall join the council.

### Powers of Council

The council currently governs the Global Operative Scope and the Local Community Scope. Through [propsals](https://encointer.subscan.io/event?module=collective&event=all), it can execute the following actions:

* Adjust ceremony schedule
    * [`set_phase_duration`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/scheduler/src/lib.rs#L173): adjust ceremony schedule phase durations
    * [`set_next_phase_timestamp`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/scheduler/src/lib.rs#L184): arbitrarily define the time for next phase change
* Manage communities
    * [`new_community`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L76): Register new communities
    * [`add_location`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L143): add meetup location for community
    * [`remove_location`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L189): remove meetup location for community
    * [`update_community_metadata`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L214): change name, currency, artwork IPFS cid for community
    * [`update_demurrage`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L238): change how fast balances are demurraged per community
    * [`update_nominal_income`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L258): the amount of basic income per ceremony per person per community
    * [`set_min_solar_trip_time_s`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L279): security parameter to calculate minimal location distance
    * [`set_max_speed_mps`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L289): security parameter defining the maximal speed over ground of an adversary
    * [`purge_community`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/communities/src/lib.rs#L299): Remove a community by from the registry entirely, wiping all balances, reputation and locations
* Manage Ceremony Parameters and Memory
    * [`set_inactivity_timeout`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L386): define how many ceremonies a community can be idle before getting purged
    * [`set_endorsement_tickets_per_bootstrapper`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): define how many endorsement tickets bootstrappers should get to invite people they trust
    * [`set_reputation_lifetime`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): define how long proof-of-personhood reputation is valid for and stored
    * [`set_meetup_time_offset`](https://github.com/encointer/pallets/blob/91cbd7c9c0d47c4a80c096d3b2b501625a6bb724/ceremonies/src/lib.rs#L396): finetune meetup time difference to high sun
    * [`purge_community_ceremony`](https://github.com/encointer/pallets/blob/f37679a922a675f7a98a9006704d32fe6c3874ec/ceremonies/src/lib.rs#L451): garbage collect outdated reputation if necessary due to parameter changes
    * [`set_time_tolerance`](https://github.com/encointer/pallets/blob/1d52b0775471270bbcb42686f02555d5367aa68f/ceremonies/src/lib.rs#L452) set how precisely the meetup time needs to be attested to be considered valid
    * [`set_location_tolerance`](https://github.com/encointer/pallets/blob/1d52b0775471270bbcb42686f02555d5367aa68f/ceremonies/src/lib.rs#L464) set how precisely the meetup location needs to be attested to be considered valid
* Manage Currency/Fee Parameters
    * [`setFeeConversionFactor`](https://github.com/encointer/pallets/blob/1d52b0775471270bbcb42686f02555d5367aa68f/balances/src/lib.rs#L104) tune community currency extrinsic fees relative to KSM fees and community income

* Treasury
    * [accept / reject treasury spend proposals](https://github.com/encointer/encointer-parachain/pull/100) (the treasury receives KSM fees for extrinsics plus potential donations

In the beginning, onboarding of new communites will be permissioned, subject to the council's approval. 
The team sees no other way to avoid bot communities squatting the earth's surface. 
Over time, a web-of-trust will build and new communities can be onboarded by endorsement of other communites.

