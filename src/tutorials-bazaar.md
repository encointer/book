# Bazaar

*THIS IS WORK IN PROGRESS*
*recommended for developers only*

## setup ipfs uploads

In order to make our helper scripts work on our testnet, we need to upload metadata and assets to a public IPFS gateway. In this example we use infura.

```bash
export IPFS_ADD_URL=https://ipfs.infura.io:5001/api/v0/add
export IPFS_API_KEY=<KEY>:<SECRET>
```

## create a pure proxy business account

*In the future, we will recommend that businesses create pure proxy accounts for their business accounts. This way, control over the account can be distributed, revoked and multisiged*

## create a business entry for Bazaar

save the following as `my_business.json`
```json
{
  "name": "Kueche Edison",
  "description": "bei uns gibt es köstlichen Kaffe",
  "category": "food",
  "address": "Technoparkstrasse 1, 8005 Zürich",
  "telephone": null,
  "email": null,
  "longitude": "8.515962660312653",
  "latitude": "47.390349148891545",
  "openingHours": "Mon-Fri 8h-18h",
  "logo": "QmUH7W2eAWTfHRYYV1YitZaz54sTjEwv6udjZjh7Tg47Xv",
  "photos": ""
}
```
The owner of this business can no register the business entry with the follwoing comman
```
./bazaar.py -u wss://gesell.encointer.org -p 443 --cid u0qj94fxxJ6 --bizaccount //Alice register-business ./my_busyness.json 
```

This helper script will upload your json to IPFS and register its ipfs url on testnet gesell

verify on-chain registry with

```
./bazaar.py -u wss://gesell.encointer.org -p 443 --cid u0qj94fxxJ6 list-businesses
```

## register an offering

An offering is a product with a price, so let's define the product:

save the following as `my_product.json`
```json
{
	"name": "Kaffee",
	"description": "Köstlicher Kaffe",
	"category": "food",
	"image": "QmZzkgNe6B6M9Y3UeGgugwEB56v5qBm35bcPTPnZFNtY7d",
	"itemCondition": null
}
```

Let's register this offering with product and price
```
./bazaar.py --client "../target/release/encointer-client-notee -u wss://gesell.encointer.org" --cid sqm1v79dF6b --bizaccount //Alice register-offering my_product.json --price 42
```
This helper script will upload your json to IPFS and register its ipfs url on testnet gesell

verify
```
/bazaar.py -u wss://gesell.encointer.org -p 443 --cid u0qj94fxxJ6 list-offerings
```

## Why IPFS?

While IPFS isn't very reliable in terms of discovery and availability, it represents the only content distribution method currently available which has a potential of working in decentralized setups. It is also content-addressed which guarantees that the correct content is delivered in untampered state. 

## Bazaar-web

You can visit [bazaar.encointer.org](http://bazaar.encointer.org/?rpc=wss%3A%2F%2Fgesell.encointer.org) and browse you newly created businesses and offerings

