# Community Treasuries

Every Encointer community has a **treasury** — a technical account that holds funds on behalf of the community. The treasury is not controlled by any private key; instead, funds can only be spent through [democratic proposals](./protocol-democracy.md) approved by the community's reputables.

## How treasuries work

### Treasury accounts

Each community's treasury address is [deterministically derived](https://github.com/encointer/pallets/blob/master/treasuries/src/lib.rs) from the community identifier. No person or entity holds the private key. This means funds sent to the treasury can only be moved by on-chain democracy — there is no backdoor.

### Funding

Treasuries can receive funds from:
- Direct transfers (donations from individuals or organizations)
- A share of network fees (planned for the future)
- Faucet drip fees (planned for the future)

### Spending

Community members propose spending through [democracy proposals](./protocol-democracy.md). Available spending actions:

- **Spend native** — Transfer native tokens (KSM on mainnet, ERT on testnet) from the treasury to a beneficiary
- **Issue swap native option** — Grant someone the right to exchange community currency for native tokens from the treasury at a specified rate
- **Issue swap asset option** — Grant someone the right to exchange community currency for XCM-transferable assets from the treasury

All spending proposals must pass the community's democratic vote before enactment.

## Swap options

Swap options are a mechanism for communities to compensate service providers, fund projects, or incentivize participation using treasury-held assets.

A **swap option** grants an individual the right (but not obligation) to exchange community currency for treasury assets at a specified exchange rate. Parameters include:

- **Allowance** — Maximum amount of native/asset tokens that can be exchanged
- **Rate** — Exchange rate (CC per native/asset token)
- **Burn** — Whether the CC paid is burned or sent to the treasury
- **Validity window** — Optional time bounds (valid_from, valid_until)

When the option holder exercises their option, they pay the equivalent CC amount and receive native/asset tokens from the treasury. The option's remaining allowance decreases accordingly.

## Tutorial

See the [Community Treasury Spending tutorial](./tutorials-treasuries.md) and [Swap Options tutorial](./tutorials-swap-options.md) for hands-on usage.
