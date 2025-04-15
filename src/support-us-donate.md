# Donate

For donations to the Encointer Association, please reach out to us: info@encointer.org

## Donate directly to Encointer communities

Encointer communities all have a treasury which can hold funds and enables [democratic decisions on spending proposals](./protocol-democracy.md). No single person or entity can control the treasury. The community members decide democratically how to spend the funds. This is a great way to support local communities and their projects. The Encointer Association has no way to intercept these donations or influence spending decisions.

Please make sure to donate to the correct community treasury on the Encointer Network. The following list contains the addresses of all communities that are currently active on the
Encointer Mainnet:

* Leu Zurich, Switzerland: `HNJDzJEGaBgWRXz7bjERsRidJFQBnj1AZ2Tn3Q9uRGynhwq`
* Nyota, Dar-es-Salaam, Tanzania: `E9KVuDLEtBBWSqhCiKn31VPBBLe33CbYJTrnWAbjszwskWH`
* Paynuq, Zaria, Nigeria: `E2mZ1u2xepTF8nuEQVkrimPVwqtqq1joC56cUwYPftXAEQL`

How to donate:
1. obtain [KSM tokens](https://www.kraken.com/learn/what-is-kusama-ksm)
2. [teleport KSM](./tutorials-faucets.md#moving-ksm) to Encointer Network
3. transfer KSM to the community treasury address

### Verify addresses

Community treasury addresses have no associated private key. They are [generated deterministically](https://github.com/encointer/pallets/blob/0c0307df120144cd1200243d2198a2e41baa09bb/treasuries/src/lib.rs#L150-L159) from community identifiers and can be queried for all communities from an rpc endpoint

using our cli and subkey
```bash
nctr-k list-communities
nctr-k get-treasury --cid kygch5kVGq7
subkey inspect --network kusama 5DdhqasTcXAFko2FS1Wj948P2b4QENPR5vd7PqteG5nT8jxs
```

