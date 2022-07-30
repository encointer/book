# How to Register an Encointer Community (manually)

To register a community to Encointer's test network (Gesell) you will need to submit a `specfile.json` file to the Gesell chain.
Every local community needs a trustworthy group of 3-12 local people who will help register the community and hold the bootstrapping ceremony. 
These bootstrapping accounts need to be defined a specification file which will consist of:
* Community information
* A set of bootstrapper accounts
* Geotag coordinates of your meetup location

Before continuing, make sure you have followed the [installation steps](./developers-notee-local-setup.md#Gesell-local-setup) and installed the Encointer node and client.
We will be connecting to the `wss://gesell.encointer.org` endpoint to register a community on the live test net using the provided client, but you could run a node locally for local testing if you wanted.

## Send tokens to bootstrap accounts

1. To make things easy, we'll create an alias for the chain client.

  ```console
  # Gesell node endpoint
  NURL=wss://gesell.encointer.org
  NPORT=443
  alias gesell="./target/release/encointer-client-notee -u $NURL -p $NPORT"
  ```

  Use `gesell --help` to check what commands and subcommands the client CLI comes with.

1. Generate accounts to use as bootstrappers in your registration `specfile.json` file.
  Running this command will display an SS58 account address and generate its corresponding secret phrase in the `/my_keystore` folder inside your local node directory:

  
  ```bash
  gesell new-account
  ```
  
  Do this at least three times and make sure not to loose the secret phrases.
  Note: you could also use skip this step and use accounts from real members of your community if you would like to bootstrap using their own accounts.


1. Send faucet tokens to the accounts:

  ```bash
  gesell faucet <bootstrap-account-1> \ <bootstrap-account-2> \ <bootstrap-account-3>
  ```

## Specify your meetup location

To define a region for your community you will need to provide accurate geo coordinates for the meetup locations and some meta-information about the community.
If you want to be sure which location will be the bootstrapping location, specify only a single location and register more locations after the bootstrapping ceremony.

> The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12. Read more about why this is important in the [protocol threat model](protocol-threat-model.md) document.


Go to [geojson.io](https://geojson.io) and select your meetup location(s) on a map.
This will generate some JSON on the right side panel, for example:

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {}
      "geometry": {
        "type": "Point",
        "coordinates": [
          -28.102619,
          31.141935
        ]
      },
    }
  ]
}
```

Copy this to a text editor and save it as `specfile.json`.

Note: If you are selecting several locations, make sure that you select places that are >100m apart.
You also need to keep this minimal distance from other registered communities.
Use the [explorer](https://explorer.encointer.org) to browse all registered communities and their attributes on a map.


## Update metadata and register

Now that you have your bootstrapper accounts, you can define them in the metadata block of the specification file. 

1. Modify your `specfile.json` to add metadata about your community and specify your bootstrapping accounts by replace the account addresses in "bootstrappers" with the ones you have created and add additional meetup locations.
  Here's an example of a `specfile.json` with a single meetup location:  
  ```json
  {
    "type": "FeatureCollection",
    "community": {
      "meta": {
        "name": "Mediterranea",
        "symbol": "MTA",
        "icons": "QmVmew4gZHyCK2Fv4UBgsvfLdf1Q6UiF9MD6wsfPCuNVQp"
      },
      "bootstrappers": [
        "5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG",
        "5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM",
        "5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t"
      ]
    },
    "features": [
      {
        "type": "Feature",
        "properties": {}
        "geometry": {
          "type": "Point",
          "coordinates": [
            -28.102619,
            31.141935
          ]
        },
      }
    ]
  }
  ```

  Make sure to save your specification file.

1. Use one of the bootstrappers to register your new community with your `specfile.json`:

  ```bash
  gesell new-community specfile.json <bootstrap-account-1>
  ```

1. Check that your community has been registered successfully:

  ```bash
  gesell list-communities
  ```

  This should return your community identified (CID) followed by your community name and locations.

Congratulations, you have just registered your community.

What's next?

* TODO 
* TODO..