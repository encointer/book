# Testnet Gesell
Gesell is our first testnet. It allows you to bootstrap new local currencies and perform regular proof-of-personhood ceremonies that give participants a universal basic income. This network is meant for testing with bot populations in order to audit and stress-test the protocol. Gesell does not give you the privacy that later networks will provide. We do not expect real physical meetups to happen on this network except occasional demo meetups. 

## Design

Gesell is based on [substrate](https://substrate.dev/) and its nodes consist of four *pallets*

![Gesell](./fig/Testnet-Gesell-Component-Interactions.svg)

### Scheduler

Keeps track of time and maintains the ceremony state-machine phase changes.

### Currencies

Registry for all local currencies with their properties and meetup locations

### Balances

The individual's account balances in all currencies, featuring [demurrage](./economics-demurrage.md).

### Ceremonies

Where participants register for ceremonies. Assignment of meetups and issuance of UBI upon proof-of-personhood.

