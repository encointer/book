# Testnets

Encointer maintains two testnets: Gesell and Cantillon.

The networks differ in their goals and designs:

## Gesell

Aimed at automated testing of the protocol and our [mobile phone app](./app.md).

* Time-warping allows to script bot populations and hold ceremonies every 30min.
* Complete transparency of all registries and balances. Everything happens on-chain.
* experimenting with real ceremony meetups, physically meeting people.

## Cantillon

The main purpose of Cantillon is privacy enhancement through Trusted Execution environments (with enclaves still in development mode, so privacy is not guaranteed before we move to production mode)

Watch our [demo video on bootstrapping a bot community](https://youtu.be/X1Zb68Z1fac)

## Outlook on Mainnet

The mainnet is planned to become a parachain of [Kusama](https://kusama.network/), the canary network of [Polkadot](https://polkadot.network/). The chain security will depend on Kusama relay chain.

## Time Warping for Testnets

In order to understand the different timing on our networks, we offer the following figure:

![Phase Timing](./fig/phase-timing.svg)

* Temporarily, we will apply 30min ceremony cycle to both Gesell and Cantillon*
