# Decentralization

Decentralization can mean very different things and is never black and white but rather a goal that can never be fully achieved. Encointer aims at

  * **architectural decentralization:** avoid a single point of failure through redundancy (different machines in different geographic locations)
  * **political decentralization:** distribute control among many individuals and organizations
  * **logical decentralization:** ensure that the system can continue to exist if it is divided into arbitrary subgroups

The first two are common for blockchains. Logical decentralization, however, isn't a feature of global blockchains. There may only be one truth globally. Encointer, on the other hand, can be split into its shards of local communities and continue to exist - with restrictions on finality and interoperability. This way, Encointer could even continue to exist in countries that eclipse their citizens from the internet or selective parts of it. At least temporarily.

Over all, *resilience* is what we aim for.

## Blockchain

Why does Encointer need a blockchain? All the economic aspects could be implemented with a central server as well.

We develop the Encointer software with altruistic intentions. Still, Encointer may likely be judged as unlawful in certain jurisdictions and repression may follow. If a trusted entity would operate Encointer as a service, that system would be very vulnerable to states or other economically powerful entities.

P2P systems, including blockchains, have proven to be very resilient versus state intervention.

## Consensus and Security

On blockchains, code is law. This law needs to be secured against malicious attacks. While the security of the Bitcoin blockchain is measured in mining hashpower, the security of PoS chains like Polkadot is measured in staked capital. Both approaches boil down to the simple rule: *the "biggest" blockchain is the most secure*.

Encointer runs as a [common good parachain on Kusama](./parachain-kusama.md), inheriting the security of the Kusama relay chain validators. This means Encointer does not need to bootstrap its own validator set or bind significant capital for consensus security. Block production is handled by [collators](./infrastructure-collator-setup.md), who are critical for availability but not for security.

In the long term, Encointer's proof of personhood could enable democratic consensus mechanisms as described in [PoPcoin](http://bford.info/pub/dec/pop.pdf), but this requires a sufficiently large and mature web of trust among communities.

## Governance

Encointer is governed on three levels following the principle of subsidiarity:

- **Protocol level** — Changes to the Encointer runtime must be approved by Kusama relay chain governance (KSM holders via OpenGov), since Encointer is a common good parachain.
- **Global operative level** — Global parameters (ceremony schedule, community onboarding, reputation lifetime) are currently managed by the [Encointer council](./decentralization-governance.md), which consists of members of the Encointer Association. The council acts as an interim governance body until Encointer's own democracy reaches sufficient adoption to take over.
- **Community level** — Each community governs its own parameters (income, demurrage, locations, treasury spending) through [on-chain democracy](./protocol-democracy.md) based on one-person-one-vote.

See [Governance](./decentralization-governance.md) for details.

## Privacy

Encointer aims to protect its users' privacy:

  * Account balances and transfer amounts of local community currencies are public on-chain (privacy enhancements through confidential transactions are on the roadmap)
  * [Reputation rings](./protocol-reputation-rings.md) allow participants to prove personhood anonymously using ring-VRF signatures, without revealing which specific person they are
  * Different application contexts yield unlinkable pseudonyms for the same person

### Philosophy

Privacy can also be a threat to communities. There is a good reason why most nation states enforce anti money laundering (AML) laws. Encointer aims to follow a simple rule: **The smaller the amounts, the higher the privacy should be. And vice versa - the higher the amounts, the higher the transparency should be**. This - unfortunately - is pretty much the opposite of what we experience in today's reality as big capital and gains can easily be offshored and obscured while everyday purchases are traced down to the penny.

As Encointer is made up of many small communities - none of which will have a significant market capitalization on a global scale - it will never be easy to move great amounts of capital through these local currencies unnoticed. That's why Encointer simply is not an attractive channel for laundering money.
