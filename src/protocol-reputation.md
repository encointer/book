# Encointer Personhood Reputation

Successful attendance at [Encointer ceremonies](./protocol-ceremony-cycle.md) gives you personhood *reputation*.

The more frequently you attend ceremonies, the higher your reputation.

## Reputation Lifetime

Reputation has limited lifetime, currently set to 5 ceremonies on mainnet. The reasoning is that personhood reputation should not benefit early adopters as they are not more human than late adopers. The upside is also that identity loss has limited impact. You just start all over with a new account in case you lost your phone and did not backup your account.

## Reputation Ratio

We refer to reputation as number of attended ceremonies during the last N ceremonies, i.e. 3/5 will prove that you are not maintaining 2 or more accounts with that reputation, because you can only attend one meetup per ceremony.

## User Journey

### Newbie

Usually, you will start with zero reputation as a newbie. You will need a tiny bit of your community's currency from someone else in your community or around 0.001 KSM (for Encointer Mainnet) to get started. You may register for a ceremony, but your assignment is not guaranteed because our [threat model](./protocol-threat-model.md) assumes an honest majority. As anyone can register as a newbie, we need to limit the number of newbies per meetup. 

#### Endorsement

If you know the bootstrappers of your community currency, you can ask them for an endorsement. They can endorse you as a trusted contact and you get a guaranteed assignment for th upcoming ceremony.

### Reputable

Once you have attended at least one ceremony successfully, your status is *reputable* and as long as you attend at least one ceremony within the [reputation lifetime](#reputation-lifetime) you will always have a guaranteed assignemnt as long as there are enough other participants.

#### No-Show Punishment

Every time you register as reputable, you actually *spend* you reputation on your guaranteed slot. If you successfully attend the ceremony, you'll get a fresh repuatation. However, if you do not show up although you have registered, your reputation will not refresh and you fall back to newbie status.

Spent reputation still counts for your [reputation ratio](#reputation-ratio), but you can't use it again to register for a ceremony.

## Bootstrappers

Bootstrappers are the first few pre-defined participants in the first ceremony of a community. They build the trusted setup as they are the ones who initate a new community. 

The *bootstrapper* status is immortal. Bootstrappers are always guaranteed an assignment if they register in time, irrespective of their recent reputation. The reasoning for this is that the entire community relies on the trusted setup and their special status allows a community to recover in case of an interruption or failure of an entire ceremony.

Bootstrappers are also granted the right to [endorse](#endorsement) newbies to accelerate community growth based on interpersonal trust. Bootstrappers should only endorse people who they have met physically in their community area and should not rely on some online message asking them for endorsement.