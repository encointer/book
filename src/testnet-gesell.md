# Testnet Gesell
Gesell is the Encointer testnet. It allows you to bootstrap new local currencies and perform regular proof-of-personhood ceremonies that give participants a universal basic income. This network is meant for testing with bot populations in order to audit and stress-test the protocol. Gesell does not provide privacy for transactions. We do not expect real physical meetups to happen on this network except occasional demo meetups.

## Design

Gesell is a standalone [Substrate](https://substrate.io/)-based chain (not a parachain) running the same pallets as the [Kusama mainnet](./parachain-kusama.md) but with accelerated timing.

### Core pallets

- **Scheduler** — Keeps track of time and maintains the ceremony state-machine phase changes
- **Communities** — Registry for all local currencies with their properties and meetup locations
- **Balances** — Account balances in all currencies, featuring [demurrage](./economics-demurrage.md)
- **Ceremonies** — Participant registration, meetup assignment, attestation and reward issuance

### Governance and economy pallets

- **Democracy** — [On-chain governance](./protocol-democracy.md) with adaptive quorum biasing
- **Faucet** — [Token dispensers](./tutorials-faucets.md) for proof-of-personhood holders
- **Treasuries** — [Community treasuries](./protocol-treasuries.md) with democratic spending proposals
- **Reputation Commitments** — Tracks which ceremony attendances have been committed for specific purposes

### Advanced feature pallets

- **Reputation Rings** — [Ring-VRF based anonymous proof of personhood](./protocol-reputation-rings.md)
- **Offline Payment** — [ZK-proof based offline transfers](./protocol-offline-payments.md)
- **Bazaar** — On-chain [business and offering registry](./bazaar.md)

### Timing

On Gesell, the ceremony cycle is compressed to 30 minutes (10 minutes per phase) to enable rapid testing. See [time warping](./deployments.md#time-warping-for-testnets) for details.
