# Testnet Cantillon

> **DISCONTINUED**

Cantillon was the testnet to showcase the privacy features using trusted execution environments with [Integritee technology](https://integritee.network)

It is still on our roadmap to include these features, but currently, the focus lies on running a [common good parachain on Kusama](./parachain-kusama.md)

## Design

Cantillon uses the same pallets as Gesell, but the privacy-sensitive ones will be executed off-chain, inside a trusted execution environment (TEE). [SubstraTEE](https://github.com/scs/substraTEE) will be the framework that isolates sensitive information inside Intel SGX enclaves (Alternative TEE technologies are being evaluated)

![Cantillon](./fig/Testnet-Cantillon-Component-Interactions.svg)
