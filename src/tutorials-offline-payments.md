# Offline Payments

Encointer's offline payment system allows community currency transfers without an active internet connection. It uses zero-knowledge proofs (Groth16 on BN254) so a buyer can generate a payment proof offline and present it as a QR code. A seller (or any third party) can later settle the payment on-chain.

We assume you already have
* [set up the CLI client](./tutorials-cli.md)
* [registered a community](./tutorials-register-community.md)
* [performed a virtual cycle gathering](./tutorials-perform-cycle.md)

## Concepts

Offline payments involve three steps:

1. **Identity registration** — Each user registers a Poseidon commitment derived from their account seed. This links their on-chain identity to a ZK-friendly secret.
2. **Proof generation** — The buyer generates a Groth16 proof offline that proves they know the secret behind their registered commitment and authorizes a specific transfer (recipient, amount, community).
3. **Settlement** — Anyone with the proof (typically the seller) submits it on-chain. The chain verifies the proof against the on-chain verification key and executes the transfer.

## Testnet Gesell Tutorial

### Trusted setup

Before offline payments can work, a Groth16 verification key must be set on-chain. On a dev node, we can use the test key:

```bash
nctr-dev offline-payment admin set-vk --signer //Alice
```

This uses the built-in test verification key. For production, see [Trusted Setup Ceremony](#trusted-setup-ceremony) below.

### Register offline identities

Both the buyer and seller need to register their offline identities. This computes a Poseidon hash commitment from the account's seed and stores it on-chain.

```bash
nctr-dev account poseidon-commitment register //Alice
nctr-dev account poseidon-commitment register //Bob
```

Verify the registration:

```bash
nctr-dev account poseidon-commitment get //Alice
# Account: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
# Commitment: 0x...
```

### Generate an offline payment

Alice wants to pay Bob 10 MTA. She generates the proof offline:

```bash
nctr-dev offline-payment pay --signer //Alice --to //Bob --amount 10 --cid srcq45PYNyD
```

This outputs a JSON object containing the proof, sender, recipient, amount, community and nullifier. Save it to a file:

```bash
nctr-dev offline-payment pay --signer //Alice --to //Bob --amount 10 --cid srcq45PYNyD > payment.json
```

In a real scenario, this JSON would be encoded as a QR code displayed on the buyer's phone.

### Settle the payment

Bob (or anyone) can submit the proof for on-chain settlement:

```bash
nctr-dev offline-payment settle --signer //Bob --proof-file payment.json
```

You should see:
```
Offline payment submitted successfully
Payment settled!
Sender: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
Recipient: 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
Amount: 10
```

The same proof cannot be used twice — the nullifier prevents double-spending.

### Using a custom proving key

For production use, you should use a proving key from a proper trusted setup instead of the test key:

```bash
nctr-dev offline-payment pay --signer //Alice --to //Bob --amount 10 --cid srcq45PYNyD --pk-file proving_key.bin
```

## Trusted Setup Ceremony

The Groth16 scheme requires a one-time trusted setup. The resulting proving key is distributed to wallets; the verification key is set on-chain. To minimize trust assumptions, the setup can be performed as a multi-party ceremony where each participant adds entropy. As long as at least one participant is honest, the setup is secure.

### Single-party setup (testing only)

Generate a proving key and verification key:

```bash
nctr-dev offline-payment admin trusted-setup generate --pk-out proving_key.bin --vk-out verifying_key.bin
```

Verify the keys are consistent:

```bash
nctr-dev offline-payment admin trusted-setup verify --pk proving_key.bin --vk verifying_key.bin
# PASS: Proving key and verifying key are consistent.
```

Set the verification key on-chain:

```bash
nctr-dev offline-payment admin set-vk --vk-file verifying_key.bin --signer //Alice
```

### Multi-party ceremony

For production deployments, use a multi-party ceremony:

**1. Initialize** — The ceremony coordinator generates the initial CRS:
```bash
nctr-dev offline-payment admin ceremony init --pk-out ceremony_pk.bin --transcript ceremony_transcript.json
```

**2. Contribute** — Each participant applies their secret randomness:
```bash
nctr-dev offline-payment admin ceremony contribute --pk ceremony_pk.bin --transcript ceremony_transcript.json --participant "Alice"
```

Pass the `ceremony_pk.bin` and `ceremony_transcript.json` files to the next participant. Each contribution modifies the proving key and appends a receipt to the transcript.

**3. Verify** — Anyone can verify the full chain of contributions:
```bash
nctr-dev offline-payment admin ceremony verify --pk ceremony_pk.bin --transcript ceremony_transcript.json
# Verifying 3 contribution(s)...
#   #1 Alice: PASS
#   #2 Bob: PASS
#   #3 Charlie: PASS
#   Functional test: PASS
# PASS: All 3 contribution(s) verified.
```

**4. Finalize** — Extract the final proving key and verification key:
```bash
nctr-dev offline-payment admin ceremony finalize --pk ceremony_pk.bin --pk-out proving_key.bin --vk-out verifying_key.bin
```

**5. Deploy** — Set the verification key on-chain and distribute the proving key to wallets:
```bash
nctr-dev offline-payment admin set-vk --vk-file verifying_key.bin --signer //Alice
```

### Inspect key files

You can inspect a key file to check its type and integrity:

```bash
nctr-dev offline-payment admin inspect-key --file proving_key.bin
# File:   proving_key.bin
# Size:   ... bytes
# Blake2: 0x...
# Type:   Proving Key (valid)
```

## Mainnet deployment

On Encointer's mainnet, the verification key is set via council governance (not sudo). A council member would propose setting the key through a democracy proposal. The trusted setup ceremony should involve multiple independent participants from different organizations to maximize the trust assumption.
