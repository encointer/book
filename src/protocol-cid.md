# Community Identifiers

Community Identifiers are a short string identifying a particular community. The format is based on a geohash of the community's location combined with a hash of the list of all bootstrapper accounts.

Example: The Leu.Zuerich community has the cid `u0qj9QqA2Q`

## js api

Unfortunatrely, Polkadot js/apps does not support our cid type natively, so the above example appears as 
```
  {
    geohash: u0qj9
    digest: 0x1012ea85
  }
```

If you'd like to use javascript, please consider using our npm library [encointer-js](https://github.com/encointer/encointer-js) which adds support for all our types



