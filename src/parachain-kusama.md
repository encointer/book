# Encointer Kusama Parachain

Encointer runs as a **common good parachain** on [Kusama](https://kusama.network/). As a common good chain, Encointer's parachain slot is granted by Kusama governance rather than won through auction, reflecting Encointer's public-good mission of providing proof of personhood and local community currencies.

## What this means

- **Shared security** — Encointer inherits the full security of the Kusama relay chain. Block validity is verified by Kusama validators, not by Encointer's own collators.
- **Native KSM integration** — Users can hold KSM on Encointer Network, pay transaction fees in KSM or community currencies, and receive KSM from [faucets](./tutorials-faucets.md).
- **XCM interoperability** — Encointer can send and receive cross-chain messages with other Kusama parachains, enabling future integrations like personhood verification for other chains.

## Collators

Encointer Network is maintained by a set of [collators](./infrastructure-collator-setup.md) who collect transactions and produce block candidates. Collator operation is critical for availability but not for security.

## Governance

Protocol upgrades to Encointer must be approved by Kusama relay chain governance (KSM holders), since Encointer is a common good chain. Community-level parameters (income, demurrage, locations) are governed by [Encointer's own democracy](./protocol-democracy.md). Global operational parameters are currently managed by the [Encointer council](./decentralization-governance.md).

## Endpoints

- RPC: `wss://kusama.api.encointer.org`
- Explorer: [explorer.encointer.org](https://explorer.encointer.org)
- Subscan: [encointer.subscan.io](https://encointer.subscan.io)
