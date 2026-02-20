# Encointer Protocol

The *Encointer protocol* provides a decentralized proof of personhood. It ensures that:
* only humans receive a basic income
* every human can only receive it once (per community)

## How it works

Encointer leverages the fact that every person can only be in one place at one time. Participants attend regular physical [ceremony meetups](./protocol-ceremony-cycle.md) with small groups of randomly assigned people. Because all meetups happen simultaneously around the world at local high sun, no single person can attend more than one.

Successful attendance earns [reputation](./protocol-reputation.md), which serves as proof of personhood. This reputation is used to:
- Issue [community currency income](./economics-ubi.md) (one income per ceremony per person)
- Vote on [community governance proposals](./protocol-democracy.md) (one person, one vote per attendance)
- Claim [faucet drips](./tutorials-faucets.md) (one drip per attendance per faucet)
- Produce anonymous [proof-of-personhood credentials](./protocol-reputation-rings.md) via ring-VRF signatures

## Protocol components

- **[Ceremony cycle](./protocol-ceremony-cycle.md)** — The three-phase cycle (Registering → Assigning → Attesting) that drives periodic meetups
- **[Reputation](./protocol-reputation.md)** — How attendance is tracked and how reputation lifetime works
- **[Community identifiers](./protocol-cid.md)** — How communities are identified by their geographic location
- **[Democracy](./protocol-democracy.md)** — On-chain governance with adaptive quorum biasing
- **[Community treasuries](./protocol-treasuries.md)** — Democratically governed community funds
- **[Reputation rings](./protocol-reputation-rings.md)** — Anonymous proof of personhood via ring-VRF
- **[Offline payments](./protocol-offline-payments.md)** — ZK-proof-based transfers without internet
- **[Threat model](./protocol-threat-model.md)** — Security assumptions and attack analysis

## Architecture

Encointer runs as a [common good parachain on Kusama](./parachain-kusama.md), inheriting the security of the Kusama relay chain. The protocol is implemented as a set of Substrate FRAME pallets. For testing, the same pallets run on standalone testnets ([Gesell](./testnet-gesell.md)).

The formal definition of the protocol can be found in the [whitepaper](https://github.com/encointer/whitepaper/raw/master/encointer_whitepaper.pdf).
