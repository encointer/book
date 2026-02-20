![logo](./fig/encointer_logo_orig.svg)

# Introduction

Encointer is a blockchain protocol that provides **proof of personhood** and **local community currencies**. It runs as a common good parachain on [Kusama](https://kusama.network/).

At its core, Encointer leverages the fact that every person can only be in one place at one time. Participants attend regular physical [ceremony meetups](./protocol-ceremony-cycle.md) with small groups of random people. Because all meetups happen simultaneously around the world, no one can attend two. This yields a unique proof of personhood (uPoP) for each participant — without relying on government IDs, biometrics, or centralized authorities.

## What Encointer provides

- **Proof of personhood** — Sybil-resistant identity that can be used for democratic voting, faucet drips, and anonymous credential proofs via [reputation rings](./protocol-reputation-rings.md).
- **Local community currencies** — Each community issues its own currency as a [universal basic income](./economics-ubi.md), subject to [demurrage](./economics-demurrage.md). Communities govern their own parameters through [on-chain democracy](./protocol-democracy.md).
- **Community treasuries** — Communities can hold and spend funds [democratically](./protocol-treasuries.md), including issuing [swap options](./tutorials-swap-options.md) to compensate service providers.
- **Offline payments** — [Zero-knowledge proofs](./protocol-offline-payments.md) enable community currency transfers without an internet connection.
- **Privacy-preserving reputation** — [Ring-VRF signatures](./protocol-reputation-rings.md) let participants prove they are unique humans without revealing which specific person they are.

## Learn more

- [The Encointer Protocol](./protocol.md) — How proof of personhood works
- [Economics](./economics.md) — Why local currencies with demurrage and UBI
- [Tutorials](./tutorials.md) — Hands-on guides for the CLI and app
- [Whitepaper](https://github.com/encointer/whitepaper/raw/master/encointer_whitepaper.pdf) — Formal protocol definition
