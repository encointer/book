# Testnet Cantillon

## Design

Cantillon will use the same pallets as Gesell, but the privacy-sensitive ones will be executed off-chain, inside a trusted execution environment (TEE). [SubstraTEE](https://github.com/scs/substraTEE) will be the framework that isolates sensitive information inside Intel SGX enclaves (Alternative TEE technologies are being evaluated)

![Cantillon](./fig/Testnet-Cantillon-Component-Interactions.svg)
