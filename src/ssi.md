# Self-Sovereign Identity and Digital Personhood

> With self-sovereign identity (SSI) the individual identity holders can fully create and control their credentials, without being forced to request permission of an intermediary or centralised authority and gives control over how their personal data is shared and used. *Wikipedia*

[SSI](https://en.wikipedia.org/wiki/Self-sovereign_identity) is a key building block for any decentralized ecosystem. To establish trust online, we need to be able to present verifiable credentials to any counterparty of our choice. With SSI we can prove our name, residence, citizenship, diplomas online.

> While identity is about distinguishing one person from another through attributes or affiliations, personhood is about giving all real people inalienable digital participation rights independent of identity, including protection against erosion of their democratic rights through identity loss, theft, coercion, or fakery

[Ford2020](https://arxiv.org/pdf/2011.02412.pdf)


# Digital Personhood

The [Encointer protocol](./protocol.md) provides a decentralized [sybil-defense](https://en.wikipedia.org/wiki/Sybil_attack) mechanism. Because each human can only be in one place at one time, Encointer provides each human with a *singular* proof of attendance for every ceremony attended. We call this a unique-proof-of-personhood (uPoP). This uPoP is used for sybil-attack mitigation for [Encointer's universal basic income](./economics-ubi.md), but is also useful for many other applications.

## Anonymous Proof of Personhood with Reputation Rings

[Reputation rings](./protocol-reputation-rings.md) allow ceremony participants to prove their personhood **anonymously** using ring-VRF signatures. After attending a ceremony, a participant can prove they are *some* member of the set of attendees without revealing *which* member. This is implemented using Bandersnatch curve ring-VRF signatures.

### Contextual Pseudonymity

Each application that verifies personhood should use a unique **context** string. The ring-VRF output is deterministic per (person, context) pair, which means:
- Within the same context, a person always produces the same pseudonym (preventing double-claims)
- Across different contexts, the same person gets different, **unlinkable** pseudonyms

This enables privacy-preserving sybil defense: a voting app and a token airdrop can both verify personhood without being able to link the same person across applications. See the [reputation rings tutorial](./tutorials-reputation-rings.md) for hands-on usage.

## Sybil-Defense for Applications

Let's assume a decentralized social media platform enforces all its users to maintain only one account per human and prevents sybil attacks with Encointer's proof of personhood.

Alice registers on the platform with an alter ego of Sophie Summers because she doesn't want her presence to be easily linkable to her physical world identity. Upon registration, the platform requests an anonymous proof of personhood via reputation rings. The platform specifies:

  * a unique **context** string (e.g. `my-social-platform-2025`)
  * a minimum attendance level (e.g. at least 1 of the last 5 ceremonies)
  * optionally, a list of trusted community identifiers

Alice produces a ring-VRF proof that demonstrates she attended a qualifying ceremony. The proof reveals only a deterministic pseudonym bound to the platform's context — not Alice's identity. If Alice tries to register a second account on the same platform, the identical pseudonym will be detected.

uPoPs need to be renewed over time because an adversary could register an additional account every few months otherwise.

Now imagine Sophie Summers becomes a victim of cyberbullying and Alice wants to drop that identity and create a new one under the name of Maria Gonzales. She will have to attend a few Encointer ceremonies without claiming the attendance for this platform. After enough unlinked attendances accumulate, she can register a new account because she has collected enough fresh reputation.

## Community Web of Trust

The [Encointer protocol](./protocol.md) builds communities based on trusted setups involving well-known people in every community. On a community level, this trusted setup is the root of trust. On a global level, these people may not be known nor trusted and communities will not trust each other blindly. Adversaries might maintain bot communities in the middle of the pacific.

Encointer can leverage an economic incentive to build a web-of-trust (WoT) among communities: Encointer issues local currencies. Communities that trade with each other will exchange their local currencies frequently in both directions on a decentralized exchange (DEX). Based on a few trusted seed communities that act as a root of trust (one per country, continent, world), the whole world may become linkable in a single WoT given geographically dense adoption of Encointer.

## Cross-Chain Personhood

As a [common good parachain on Kusama](./parachain-kusama.md), Encointer can provide proofs of digital personhood to other parachains through [XCM](https://wiki.polkadot.network/docs/en/learn-crosschain) cross-chain messaging. Third-party applications can also verify ring-VRF proofs off-chain using the publicly available ring data from Encointer's on-chain storage.
