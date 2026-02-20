# Reputation Rings (Anonymous Proof of Personhood)

Reputation rings allow Encointer ceremony participants to prove their personhood **anonymously**. A participant can demonstrate they are *some* member of the set of ceremony attendees without revealing *which* member. This enables privacy-preserving applications like anonymous voting, sybil-resistant access control, and token airdrops.

## How it works

### Bandersnatch keys

Each participant registers a public key on the [Bandersnatch](https://eprint.iacr.org/2021/1152) elliptic curve, linked to their on-chain account. This key is used exclusively for ring signatures and is typically derived automatically from the account seed.

### Ring computation

After each ceremony, the chain computes a **ring**: the ordered set of Bandersnatch public keys belonging to participants who attended. Ring computation is triggered automatically during the phase transition from *Registering* to *Assigning* (for the previous ceremony). The resulting ring is stored on-chain and publicly queryable.

Rings are computed per **(community, ceremony_index, level)**:
- **Level 1** includes all attendees of that ceremony
- **Level N/5** includes only accounts that attended at least N of the last 5 ceremonies

Higher levels prove stronger commitment but result in smaller rings.

### Ring-VRF signatures

A participant can produce a [ring-VRF](https://eprint.iacr.org/2020/498) signature that proves:
1. The signer holds a private key corresponding to *some* public key in the ring
2. A deterministic **pseudonym** (VRF output) unique to this signer within the given context

The VRF input binds to `(context, cid, ceremony_index, level, sub_ring)`, ensuring that the pseudonym is deterministic per signer within the same parameters. This prevents double-claims: the same person always produces the same pseudonym for the same context.

### Contextual pseudonymity

The **context** string acts as a domain separator. Different contexts yield **unlinkable** pseudonyms for the same person. A voting application might use context `my-vote-2025` while a token airdrop uses `airdrop-campaign-3`. The two proofs cannot be linked to each other, even though they come from the same person.

This is the key property that enables privacy-preserving sybil defense across multiple independent applications.

## Anonymity guarantees

Ring-VRF proofs provide **k-anonymity**: the prover is indistinguishable from any of the k members in their ring.

**Ring size = k.** If 200 people attended a ceremony, the level-1 ring has k=200. A verifier learns that the prover is one of those 200 people, but not which one.

**Levels reduce k.** Level N/5 includes only accounts that attended >= N of the last 5 ceremonies. Higher levels prove stronger commitment but shrink the anonymity set. A community with 200 attendees at level 1 might only have 30 at level 5.

**Sub-rings.** When a ring exceeds MaxRingSize (255), it is split into sub-rings of 128–255 members each. Each sub-ring is an independent anonymity set. The prover's sub-ring assignment is deterministic (sorted by key), so k <= 255 per sub-ring, even for very large communities.

**Small communities have weak anonymity.** If only 5 people attended, k=5. Applications requiring strong anonymity should enforce a minimum ring size.

**Context separation does not increase k.** The `--context` flag ensures unlinkability across applications, but within one context the prover is still hidden among the same k ring members.

### Practical guidance for application developers

- Require `k >= 20` for meaningful anonymity (reject proofs from rings smaller than your threshold)
- Use level 1 unless you specifically need to filter for committed long-term participants
- Use a unique context per application to prevent cross-application linkability

## Verification

Ring-VRF proofs can be verified by anyone who has access to the ring (the list of public keys). Verification does not require any secret information. The ring data is publicly available on-chain, so third-party applications can verify proofs off-chain.

## Tutorial

See the [Reputation Rings tutorial](./tutorials-reputation-rings.md) for hands-on usage with the CLI.
