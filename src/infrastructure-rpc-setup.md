# Public Rpc Setup

Multiple frontends for Encointer rely on public RPC nodes to query the blockchain state and history. This document describes how to set up a public RPC node for Encointer.

We will assume that you have a [full node](./infrastructure-full-node-setup.md) running already.

The standard config we provide for full node already assumes that you want to expose your rpc service. Please make sure you set `--enable-offchain-indexing true`. Then, all you need to do is make port 9944 accessible to the public. As the procedure to expose your service varies a lot depending on your setup, we can't provide a one-size-fits-all recipie here.

## Test

The following example tests the public rpc and also verifies that offchain indexing is enabled

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "encointer_getLocations", "params": [{ "geohash": "0x7530716A39", "digest": "0x77f79df7" }] }' https://kusama.api.encointer.org
# {"jsonrpc":"2.0","result":[{"lat":"47.3860442609734562325","lon":"8.5167555883526802063"},{"lat":"47.3863389067466229676","lon":"8.5233766213059425354"},{"lat":"47.3869987892637922755","lon":"8.51989746093749822364"},{"lat":"47.3885922795468204072","lon":"8.51957626640796661377"},{"lat":"47.3891715535333091225","lon":"8.5129961371421813965"},{"lat":"47.3895923857361651699","lon":"8.5164836794137954712"},{"lat":"47.3896700146103739826","lon":"8.52360427379608154297"},{"lat":"47.39043449402448260344","lon":"8.51080611348152160645"},{"lat":"47.3922230783101596785","lon":"8.51907670497894109474"},{"lat":"47.39408059955918872674","lon":"8.5130444169044494629"},{"lat":"47.38784547884539222196","lon":"8.52683097124099731445"}],"id":1}

```

## Announcing you Public RPC

If you'd like to support the Encointer Network, the world should know about your rpc node. The best way to publish it is through [Polkadot js apps](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fkusama.api.encointer.org#/chainstate). Please submit a PR on their Github repo like [this one](https://github.com/polkadot-js/apps/pull/10451)
