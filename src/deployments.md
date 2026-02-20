# Deployments

Encointer is deployed on multiple networks serving different purposes.

## Encointer Network (Mainnet)

Encointer runs as a [common good parachain on Kusama](./parachain-kusama.md). This is the production network where real communities operate, real ceremonies take place, and real community currencies are issued. Chain security is provided by Kusama relay chain validators.

On mainnet, ceremonies happen every 10 days. The ceremony cycle is expected to be prolonged in the future once the protocol has been tested at larger scale.

Endpoint: `wss://kusama.api.encointer.org`

## Testnet Gesell

Aimed at automated testing of the protocol and our [mobile phone app](./app.md).

* Time-warping allows to script bot populations and hold ceremonies every 30min.
* Complete transparency of all registries and balances. Everything happens on-chain.
* Experimenting with real ceremony meetups, physically meeting people.

Endpoint: `wss://gesell.encointer.org`

See [Gesell details](./testnet-gesell.md).

## Testnet Cantillon (Discontinued)

Cantillon was a testnet for privacy features using trusted execution environments. It has been [discontinued](./testnet-cantillon.md).

## Time Warping for Testnets

In order to understand the different timing on our networks, we offer the following figure:

![Phase Timing](./fig/phase-timing.svg)

On Gesell, the ceremony cycle is compressed to 30 minutes (10 minutes per phase) to enable rapid testing with bot communities.
