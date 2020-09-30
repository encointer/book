# Decentralization

Decentralization can mean very different things and is never black and white but rather a goal that can never be fully achieved. Encointer aims at

  * **architectural decentralization:** avoid a single point of failure through redundancy (different machines in different geographic locations)
  * **political decentralization:** distribute control among many individuals and organizations
  * **logical decentralization:** ensure that the system can continue to exist if it is divided into arbitrary subgroups

The first two are common for blockchains. Logical decentralization, however, isn't a feature of global blockchains. There may only be one truth globally. Encointer, on the other hand, can be split into its shards of local communities and continue to exist - with restrictions on finality and interoperability. This way, Encointer could even continue to exist in countries that eclipse their citizens from the internet or selective parts of it. At least temporarily.

Over all, *resilience* is what we aim for.

## Blockchain

Why does Encointer need a blockchain? All the economic aspects could be implemented with a central server as well. 

We develop the Encointer software with altruistic intentions. Still, Encointer may likely be judged as unlawful in certain juristictions and repression may follow. If a trusted entity would operate Encointer as a service, that system would be very vulnerable to states or other economically powerful entities.

P2P systems, including blockchains, have proven to be very resilient versus state intervention. 

## Consensus and Security

On Blockchains, code is law. This law needs to be secured against malicious attacks. While the security of the bitcoin blockchain is measured in mining hashpower, the security of PoS chains like Polkadot is measured in staked capital. Both approaches boild down to the simple rule: *the "biggest" blockchain is the most secure*.

As Encointer is no business case or speculative asset by design, it needs to rely on other things than capital or hashpower for security of consensus. 

Thanks to its proof-of-personhood, Encointer could apply democratic consensus like described in [PoPcoin](http://bford.info/pub/dec/pop.pdf).
However, a web of trust would have to be established first.

[Polkadot](https://polkadot.network/) is a heterogeneous multi-chain system with shared security. While this approach reduces the amount of capital Encointer would have to bind for equal security, it still is a profoundly capitalistic system and competition around parachain slots may make it impossible for Encointer to become a [Polkadot parachain](https://wiki.polkadot.network/docs/en/learn-parachains). 

While the final solution is yet to be discovered, Encointer will start off as a proof-of-authority (PoA) chain which is initially operated by trusted entities. The worker registry, however, will be permissionless. Anyone with a suitable TEE will be able to operate workers for communities of their choice.

Once possible, Encointer will attempt to become a parachain of Polakdot or Kusama by means of crowdfunding.

Should this fail, it can still get finality for its PoA consensus by running as a [parathread](https://wiki.polkadot.network/docs/en/learn-parathreads) instead.

## Governance

Encointer is no static system. It shall evolve over time and different communities shall be able to adjust their local rules independently. Such a foederalistic governance is possible thanks to Encointers' [self-sovereign identity (SSI) features](./ssi.md).  Encointer's proof-of-personhood allows to have democratic voting. Its proof-of-location ensures that people can only vote on matters concerning their local community. This local governance shall rule over the parameters of individual community currencies, such as defining meetup locations, changing the [demurrage rate](./economics-demurrage.md), changing the [UBI](./economics-ubi.md) amount or the name of their currency.

Global governance shall leverage the [web-of-trust among communities](./ssi.md#Community-Web-Of-Trust). Democratic votes are thus weighted with the trust score of each community. Global governance shall decide on updates to the blockchain logic and global parameter such as the ceremony period, the set of trusted TEE manufacturers or the distribution of fees.

## Privacy

Encointer protects its users' privacy by design:

  * transfers of local community currencies are private by default. No one learns who sends how much to whom.
  * account balances of local community currencies are private (while aggregated values such as a community's total issuance and turnover are public)
  * ceremony meetup assignments are processed confidentially because that strenghtens the Encointer protocol by hindering collusion. 

### Philosophy

Privacy can also be a threat to communities. There is a good reason why most nation states enforce anti money laundering (AML) laws. Encointer aims to follow a simple rule: **The smaller the amounts, the higher the privacy should be. And vice versa - the higher the amounts, the higher the transparency should be**. This - unfortunately - is pretty much the opposite of what we experience in today's reality as big capital and gains can easily be offshored and obscured while everyday purchases are traced down to the penny.

As encointer is made up of many small communities - none of which will have a significant market capitalization on a global scale - it will never be easy to move great amounts of capital through these local currencies unnoticed. That's why Encointer simply is not an attractive channel for laundering money.