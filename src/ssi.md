# Self-Sovereign Identity and Digital Personhood

> With self-sovereign identity (SSI) the individual identity holders can fully create and control their credentials, without being forced to request permission of an intermediary or centralised authority and gives control over how their personal data is shared and used. *Wikipedia*

[SSI](https://en.wikipedia.org/wiki/Self-sovereign_identity) is a key building block for any decentralized ecosystem. To establish trust online, we need to be able to present verifyable credentials to any counterparty of our choice. With SSI we can prove our name, residence, citizenship, diplomas online.

> While identity is about distinguishing one person from another through attributes or affiliations, personhood is about giving all real people inalienable digital participation rights independent of identity, including protection against erosion of their democratic rights through identity loss, theft, coercion, or fakery

[Ford2020](https://arxiv.org/pdf/2011.02412.pdf)


# Digital Personhood

The [Encointer protocol](./protocol.md) provides a decentralized [sybil-defense](https://en.wikipedia.org/wiki/Sybil_attack) mechanism. Because each human can only be in one place at one time, Encointer provides each human with a *singular* proof of attendance for every ceremony attended. We call this a  unique-proof-of-personhood (uPoP). This uPoP is used for sybil-attack mitigation for [Encointer's universal basic income](./economics-ubi.md), but can also be useful for other use cases as described below.

## Privacy Considerations

With Encointer, users may maintain different identities on different platforms. The Encointer uPoP can be used to ensure one human only maintains one ID on one specific platform with a high degree of anonymity.

## Sybil-Defense for Social Media 

Let's assume a decentralized social media platform like [subsocial](https://subsocial.network/) enforces all its users to maintain only one account per human and prevents sybil attack with Encointer uPoP.

Alice Smith registers on subsocial with an alter ego of Sophie Summers because she doesn't want her presence on subsocial to be easily linkable to her physical world identity. Upon registration, subsocial would send a challenge to Alice to provide a uPoP. This challenge includes 

  * a platform token identifying subsocial (must be unique for subsocial)
  * a timestamp (or at least a ceremony index)
  * a confidence threshold (like: *must prove attendance for N of the last M encointer ceremonies*)
  * a list of trusted community identifers (can be restricted to a region to also provide plausibility of location of residence) Encointer will itself attempt to maintain a [web-of-trust score for communities](#Community-Web-of-Trust) which can be referenced instead.

Alice now requests her uPoP for subsocial from the Encointer platform. Encointer maintains a registry of attested attendance for all recent ceremonies. Alice is represented in this registry by changing pseudonyms for every ceremony. These pseudonyms are public keys and she now has to provide evidence that she knows all private keys for all pseudonyms she claims. 

Given that Alice can prove attendance of the requested N of the last M ceremonies, Encointer provides a uPoP to be sent to subsocial in the form of a verifiable credential signed by one encointer-worker enclave's signing-key. Encointer then stores the subsocial platform token along with Alice's ceremony attendance attestations. Should Alice attempt to claim the same ceremony attendances again for another account on subsocial, Encointer would deny to provide a uPoP.

uPoPs need to be renewed over time because an adversary could register an additional account every few months otherwise.

Now imagine Sophie Summers becomes a victim of cyberbullying and Alice wants to drop that identity and create a new one under the name of Maria Gonzales. She will have to attend a few encointer ceremonies without claiming the attendance with subsocial. After a few months, she can register a new account on subsocial because she has collected enough unlinked evidence.

## Community Web of Trust

The [Encointer protocol](./protocol.md) builds communities based on trusted setups involving well-known people in every community. On a community level, this trusted setup is the root of trust. On a global level, these people may not be known nor trusted and communities will not trust each other blindly. Adversaries might maintain bot communities in the middle of the pacific. 

Encointer can leverage an economic incentive to build a web-of-trust (WoT) among communities: Encointer issues local currencies. Communities that trade with each other will exchange their local currencies frequently in both directions on a decentralized exchange (DEX). Based on a few trusted seed communities that act as a root of trust (one per country, continent, world), the whole world may become linkable in a single WoT given geographically dense adoption of Encointer.

## Polkadot Parachain Personhood Service

Should Encointer become a common good parachain of Polkadot, it could provide proofs of digital personhood to other parachains through cross-chain communication. As uPoP will be signed by SGX enclaves, a client parachain needs a trustelss way to query the enclave registry to verify a uPoP.

[XCMP](https://wiki.polkadot.network/docs/en/learn-crosschain) can be used to verify a certain enclave is registered as an encointer-worker and has passed remote attestation on the encointer platform.

Please see our [sybil-defense demo video](https://www.youtube.com/watch?v=Fw2dalJO1hM) to learn how sybil defense works within the Polkadot ecosystem of parachains.
