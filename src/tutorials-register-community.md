# How to Register an Encointer Community (manually)

We assume you have [set up the CLI client previously](./tutorials-cli.md)

This tutorial will guide you through the process of setting up a new community on any of our [networks](./deployments.md)

If you haven't done so, please learn about the [ceremony cycle](#TODO) of the Encointer Protocol

## Set Up Bootstrappers

As Encointer is all about proof of personhood, you will need people to get started. We suggest to pick 10 diverse, dependable and trustworthy members of your community for a trusted setup. This first group of people performing the very first ceremony of a new community we call *Bootstrappers*.

If you want to test all by yourself, we suggest you create 3 bootstrapper accounts using 3 phones and register your community on [Testnet Gesell](./testnet-gesell.md). In the following, we will assume you're just testing yourself, but the process is very similar if the accounts are for real bootstrappers. 

All the bootstrappers need to [install the mobile app](https://encointer.org/encointer-app/) and set up a new account. You can then collect all the bootstrapper accounts by sharing them from within the app by going to *profile -> tap account -> share account -> send link*

The bootstrapper accounts need initial funding. On our testnets, you can use the faucet, on Mainnet, you can supply KSM or any other existing community currency

```
nctr-gsl faucet 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG 5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM 5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```

*Note: the displayed account addresses will differ from yours when you follow the tutorial. They are just provided for readabiltiy. Use your account addresses instead of the ones used in the tutorial.*

To register a community, you need to create a specfile.json containing details about the community and an account with funds to the chain client. 

## Define Ceremony Meetup Locations

We need to define in what region the community shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the community. You can use [geojson.io](https://geojson.io) to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered communities. 

If you want to be sure which location will be the bootstrapping location, please specify only a single location and register more locations after the bootstrapping ceremony.

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N/12 locations but the more locations, the better. 

## Specfile

The specfile defines the set of [bootstrappers](#set-up-bootstrappers), the initial set of [ceremony locations](#define-meetup-locations) as well as metadata like name, symbol and icons stored on [IPFS](https://ipfs.io).

An example of the specfile.json is shown below with one meetup locations:  
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
      "geometry": {
        "type": "Point",
        "coordinates": [
          -28.102619,
          31.141935
        ]
      },
      "properties": {}
    }
  ]
}
```
Note: replace the account addresses in "bootstrappers" with the ones you have created and add additional meetup locations.

*caveat: The following step is temporarily restricted to Encointer Association Council for all public networks. Please [contact us on Matrix/Element](https://app.element.io/#/room/#encointer:matrix.org) to have this performed for you. However, if you use a [local setup](./developers-notee-local-setup.md), you can perform this yourself*

Register your new community with the specfile.json:

```bash
nctr-dev new-community specfile.json 
```

Your community has been registered and the return value is your community-identifier (cid). Let's check the registry again:

```bash
nctr-dev list-communities
```

Congratulations! You are ready to perform your first Ceremony with your bootstrappers.
