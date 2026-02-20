# Offline Payments

Encointer's offline payment system enables community currency transfers without an active internet connection. It uses zero-knowledge proofs so a buyer can authorize a payment offline and a seller (or any third party) can later settle it on-chain.

## Motivation

In many regions where Encointer communities operate, reliable internet connectivity cannot be assumed. Offline payments allow face-to-face transactions to complete immediately, with on-chain settlement happening later when connectivity is available.

## How it works

### Identity registration

Each user registers a **Poseidon hash commitment** derived from a secret (typically derived from their account seed). This commitment is stored on-chain and links the user's account to a ZK-friendly identity.

### Proof generation (offline)

When a buyer wants to pay, they generate a **Groth16 proof** (on BN254) that proves:
1. They know the secret behind a registered commitment (authentication)
2. The payment details (recipient, amount, community) are bound to the proof
3. A **nullifier** derived from the secret and a nonce prevents the same proof from being used twice (double-spend prevention)

This proof is generated entirely offline — no chain access is needed. In practice, the buyer's phone generates the proof and encodes it as a QR code.

### Settlement (online)

Anyone with the proof (typically the seller) submits it on-chain. The chain:
1. Verifies the Groth16 proof against the on-chain verification key
2. Checks that the nullifier has not been seen before
3. Executes the community currency transfer from sender to recipient

### Trusted setup

Groth16 requires a one-time **trusted setup** that produces a proving key (distributed to wallets) and a verification key (set on-chain). The setup can be performed as a multi-party ceremony where each participant adds entropy. As long as at least one participant is honest, the setup is secure.

A compromised setup would allow forging proofs (spending without authorization), so the ceremony should involve multiple independent participants from different organizations.

## Security properties

- **Soundness** — A valid proof can only be produced by someone who knows the secret behind a registered commitment
- **Double-spend prevention** — The nullifier ensures each proof can only be settled once
- **Offline capability** — Proof generation requires no network access; only settlement needs chain access
- **Privacy** — The ZK proof reveals nothing about the secret; only the public commitment, recipient, amount, and nullifier are visible on-chain

## Limitations

- Settlement requires someone to be online and pay transaction fees
- The proving key must be distributed to wallets (bundled in the app or downloaded once)
- The buyer must have registered their offline identity before going offline

## Tutorial

See the [Offline Payments tutorial](./tutorials-offline-payments.md) for hands-on usage with the CLI, including single-party and multi-party trusted setup.
