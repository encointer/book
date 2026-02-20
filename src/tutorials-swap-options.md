# Swap Options

Swap options allow Encointer communities to grant individuals the right to exchange community currency (CC) for native tokens (e.g. KSM) or other assets from the community treasury. This creates a mechanism for communities to compensate service providers, fund projects, or incentivize participation using treasury-held assets.

We assume you already have
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)
* [performed a virtual cycle gathering](./tutorials-perform-cycle.md)
* familiarized yourself with [democracy](./tutorials-democracy.md) and [community treasuries](./tutorials-treasuries.md)

## Concepts

- **Swap native option** — A right to exchange CC for native tokens (ERT on testnet, KSM on mainnet) from the community treasury at a specified exchange rate.
- **Swap asset option** — A right to exchange CC for any XCM-transferable asset held by the community treasury.
- **Rate** — The exchange rate (CC per native/asset token). If omitted, an oracle or auction mechanism determines the rate.
- **Burn** — Optionally, the CC paid by the exerciser can be burned instead of being sent to the treasury.
- **Validity window** — Options can be constrained to a time window (valid_from, valid_until).

Swap options are issued through [democracy proposals](./tutorials-democracy.md). Community reputables vote on whether to grant the option.

## Testnet Gesell Tutorial

### Fund the treasury

First, ensure the community treasury has native tokens to back the swap option:

```bash
nctr-gsl community treasury get-account --cid sqm1v79dF6b
# 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem
nctr-gsl transfer //Alice 5CWoc3mGF9VEnuZzBbPWxhKPvY743AGwxUbvkYQHS8yWZbem 1000000000000
```

### Propose a swap native option

Alice proposes to grant Bob a swap native option: Bob will be allowed to exchange CC for up to 100 ERT (100000000000000 units) at a rate of 10 CC per ERT:

```bash
nctr-dev democracy propose issue-swap-native-option //Alice //Bob --native-allowance 100000000000000 --rate 10.0 --cid sqm1v79dF6b
```

Optional parameters:
- `--do-burn` — Burn the CC instead of sending it to the treasury
- `--valid-from <ms>` — Unix timestamp (milliseconds) when the option becomes valid
- `--valid-until <ms>` — Unix timestamp (milliseconds) when the option expires

Check the proposal:

```bash
nctr-dev democracy proposal list
```

### Vote on the proposal

Community reputables vote on the proposal as described in the [democracy tutorial](./tutorials-democracy.md#vote):

```bash
nctr-gsl democracy vote //Alice 1 aye sqm1v79dF6b_1
```

After enough votes, trigger the tallying:

```bash
nctr-gsl democracy proposal update-state //Alice 1
```

If accepted, the option is issued and enacted during the next [assigning phase](./protocol-ceremony-cycle.md#assigning).

### Query the option

After enactment, query the issued swap option:

```bash
nctr-dev community treasury swap-option get-native //Bob --cid sqm1v79dF6b
# SwapNativeOption:
#   cid: sqm1v79dF6b
#   native_allowance: 100000000000000
#   rate: 10
#   do_burn: false
#   valid_from: None
#   valid_until: None
```

### Exercise the option

Bob exercises his option, exchanging CC for 50 ERT (50000000000000 units):

```bash
nctr-dev community treasury swap-option exercise-native //Bob 50000000000000 --cid sqm1v79dF6b
```

Bob pays the equivalent CC amount (at the specified rate) and receives native tokens from the treasury. The option's remaining allowance is reduced accordingly.

### Swap asset options

For non-native assets (e.g. stablecoins available via XCM), the flow is similar:

```bash
# Propose
nctr-dev democracy propose issue-swap-asset-option //Alice //Bob --asset-id 0x... --asset-allowance 1000000 --rate 5.0 --cid sqm1v79dF6b

# Query
nctr-dev community treasury swap-option get-asset //Bob --cid sqm1v79dF6b

# Exercise
nctr-dev community treasury swap-option exercise-asset //Bob 500000 --cid sqm1v79dF6b
```

The `--asset-id` is a SCALE-encoded `VersionedLocatableAsset` identifying the XCM asset.

## Mainnet usage

On mainnet, swap options enable communities to compensate service providers with KSM or other Kusama ecosystem assets. The democracy proposal process ensures community-wide consent before any treasury funds are committed. The exchange rate can be fixed or left to an oracle/auction mechanism for market-driven pricing.
