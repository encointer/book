# Reputation Rings (Proof of Personhood)

Reputation rings allow Encointer participants to prove their personhood anonymously using ring-VRF signatures. After attending a ceremony, participants can prove they are a unique human without revealing which specific person they are. This enables privacy-preserving applications like anonymous voting, sybil-resistant access control, and more.

We assume you already have
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)
* [performed a virtual cycle gathering](./tutorials-perform-cycle.md) (at least one ceremony with reputables)

## Concepts

- **Bandersnatch key** — Each participant registers an elliptic-curve public key (on the Bandersnatch curve) linked to their account. This key is used for ring signatures.
- **Ring** — After each ceremony, the chain computes a ring: the set of all Bandersnatch keys belonging to participants who attended. The ring computation happens on-chain, triggered during the ceremony phase change.
- **Ring-VRF proof** — A participant can produce a verifiable random function (VRF) output that proves they are *some* member of the ring without revealing *which* member. The VRF input binds to `(context, cid, ceremony_index, level, sub_ring)`. Within the same context, the pseudonym is deterministic (preventing double-claims). Different `--context` values yield unlinkable pseudonyms for the same person, enabling contextual pseudonymity across applications.

## Testnet Gesell Tutorial

### Register a Bandersnatch key

Before participating in rings, each account must register a Bandersnatch public key. This should be done once, before attending a ceremony:

```bash
nctr-dev account bandersnatch-pubkey register //Alice
```

If no key is provided, one is automatically derived from the account's seed.

### Attend a ceremony

Follow the [ceremony tutorial](./tutorials-perform-cycle.md) to register, attend, and earn reputation. After the ceremony completes, the on-chain ring computation is triggered automatically during the phase transition.

### Check ring computation

When the REGISTERING phase transitions to ASSIGNING, rings are automatically queued for the previous ceremony and computed in the background. Check the ring for a given ceremony index:

```bash
nctr-dev personhood ring get --ceremony-index 1
```

If the ring hasn't been computed yet, you can trigger computation manually:

```bash
nctr-dev personhood ring initiate //Alice --ceremony-index 1
```

For large communities, ring computation may require multiple transactions:

```bash
nctr-dev personhood ring continue //Alice
```

### Prove personhood

Once a ring exists for a ceremony you attended, you can produce an anonymous proof of personhood:

```bash
nctr-dev personhood prove-ring-membership //Alice --ceremony-index 1
```

This outputs a hex-encoded ring-VRF signature and a pseudonym. The signature proves that:
1. The signer is *some* member of the ring for ceremony 1
2. The pseudonym is unique to this signer within this context (preventing double-claims)

### Contextual pseudonymity

Each application should use a unique `--context` string. The default is `encointer-pop`, but a voting app might use `my-vote-2025` and a token airdrop might use `airdrop-campaign-3`. The same person gets a different, unlinkable pseudonym in each context:

```bash
nctr-dev personhood prove-ring-membership //Alice --ceremony-index 1 --context "my-vote-2025"
nctr-dev personhood prove-ring-membership //Alice --ceremony-index 1 --context "airdrop-campaign-3"
```

These two proofs cannot be linked to the same person.

You can also adjust the attendance level requirement (default 1):

```bash
nctr-dev personhood prove-ring-membership //Alice --ceremony-index 1 --level 3
```

### Verify a proof

Anyone can verify a ring-VRF proof without knowing the prover's identity. The verifier must use the same `--context` that was used for signing:

```bash
nctr-dev personhood verify-ring-membership --signature 0xabcd... --ceremony-index 1 --context "my-vote-2025"
```

A successful verification confirms the signer is a unique, real person who attended ceremony 1.

### Reputation commitments

The reputation commitment system tracks which ceremony attendances have been "used" for specific purposes (like [faucet drips](./tutorials-faucets.md)). This prevents the same attendance from being claimed multiple times for the same purpose.

List all registered purposes:

```bash
nctr-gsl personhood commitment purposes
# 0: ectrfct0PioneerPot
```

List commitments for a specific purpose and community:

```bash
nctr-gsl personhood commitment list --purpose-id 0 --cid u0qj944rhWE
```

## Mainnet usage

On mainnet, the same commands apply. Bandersnatch keys are registered at account creation and rings are computed automatically after each ceremony cycle. Third-party applications can verify personhood proofs off-chain using the ring data and standard ring-VRF verification.
