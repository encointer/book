# How to Register an Encointer Community (manually)

We assume you have [set up the CLI client previously](./tutorials-cli.md)

This tutorial will guide you through the process of setting up a new community on any of our [networks](./deployments.md)

If you haven't done so, please learn about the [ceremony cycle](./protocol-ceremony-cycle.md) of the Encointer Protocol

## Set Up Bootstrappers

As Encointer is all about proof of personhood, you will need people to get started. We suggest to pick 10 diverse, dependable and trustworthy members of your community for a trusted setup. This first group of people performing the very first ceremony of a new community we call *Bootstrappers*.

If you want to test all by yourself, we suggest you create 3 bootstrapper accounts using 3 phones and register your community on [Testnet Gesell](./testnet-gesell.md). In the following, we will assume you're just testing yourself, but the process is very similar if the accounts are for real bootstrappers. 

All the bootstrappers need to [install the mobile app](https://encointer.org/encointer-app/) and set up a new account. You can then collect all the bootstrapper accounts by sharing them from within the app by going to *profile -> tap account -> share account -> send link*

The bootstrapper accounts need initial funding. On our testnets, you can use the faucet, on Mainnet, you can supply KSM or any other existing community currency

```
nctr-gsl account fund 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG 5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM 5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
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
      "assets": "QmVmew4gZHyCK2Fv4UBgsvfLdf1Q6UiF9MD6wsfPCuNVQp",
      "url": "",
      "announcementSigner": null,
      "rules": "loCoFlex"
    },
    "bootstrappers": [
      "5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG",
      "5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM",
      "5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t"
    ],
    "demurrage_halving_blocks": 2628000,
    "ceremony_income": 100
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

* replace the account addresses in "bootstrappers" with the ones you have created and add additional meetup locations.
* `demurrage_halving_blocks` of 2'628'000 corresponds to 1 year if block time is 12 seconds
* `ceremony_income` in this example is set to 100.00 MTA

> **ADVANCED**
>
> ceremony income and demurrage rate are stored as a fixpoint type (I64F64 - or 64 signed integer bits before the decimal point and 64 bit thereafter). That is why the raw numbers in js/apps can be confusing. An income of 100.00 MTA will be represented as
> ```python
> >>> 100*2**64
> 1844674407370955161600
> ```
>
> demurrage halving blocks will first be translated to an exponential function coefficient:
> ```python
> >>> int(-1*np.log(0.5)*2**64/2628000)
> 4865414248555
> ```
> Applying demmurage of one block then becomes: balance * exp(-demurrage_rate). In the case of a balance of `100.0` demurrage for one block would decrease the balance to
> ```python
> >>> 100.0*np.exp(-1*4865414248555/2**64)
> 99.99997362453999
> ```
>
> *explanatory code is provided in python syntax*

### Use Your Own Community Icon

The example specfile features a dummy icon. You may want to brand your community with its own icon. You'll need a circular icon in SVG format.

Place your icon into its own folder, i.e. `./leu.rococo/community-icon.svg`

Make sure your SVG community icon doesn't include `<style>` attributes
```
cargo install svgcleaner
svgcleaner community_icon.svg community_icon.svg
```

Upload the entire folder to ipfs:

1. using your own ipfs server
  ```
  ipfs add -rw --pin ./leu.rococo
  ```

2. using infura's [client](https://github.com/INFURA/ipfs-upload-client):
  ```
  ipfs-upload-client --id <your infura id> --secret <your infura secret> --pin ./leu.rococo 
  ```


test if you can fetch the cid through the encointer gateway which will be used by the app

```
wget http://ipfs.encointer.org:8080/api/v0/object/get?arg=QmXydp7gdTGwxkCn24vEtvtSXbR7wSAGBDLQpc8buF6T92/community_icon.svg
```

It may take a while to sync from the server you used for uploading and pinning. IPFS routing isn't very reliable nor fast yet, unfortunately.

Once you can fetch your icon, replace the IPFS cid in the community specfile with the cid of your icon folder

## Register your new community

Register your new community with the specfile.json. Signer should be an account in your keystore which is able to pay fees:

*caveat: The following step is possible to perform by anyone on out testnet Gesell. 
But on our mainnet you'll need to pass our [onboarding process](https://trello.com/b/1Yhln6NT/encointer-community-leader-applications) to be able to register your new community*

```bash
nctr-dev community new specfile.json --signer 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
```

Your community has been registered and the return value is your community-identifier (cid). Let's check the registry again:

```bash
nctr-dev community list
```

Congratulations! You are ready to [select your community in the Encointer Wallet app](./app-select-community.md) [perform your first Ceremony](./app-meetup.md) with your bootstrappers.
