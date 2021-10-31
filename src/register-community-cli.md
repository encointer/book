# How to Register an Encointer Community (manually)
For simplicity, we'll create an alias for the chain client
```console
# Gesell node endpoint
NURL=wss://gesell.encointer.org
NPORT=443
alias nctr="./target/release/encointer-client-notee -u $NURL -p $NPORT"
```
For the local chain, remove the NURL and modify the NPORT accordingly. 
Then create some accounts (at least 3): 
```
> nctr new-account
5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
> nctr new-account
5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM
> nctr new-account
5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```
Note: the displayed account addresses will differ from yours when you follow the tutorial. They are just provided for readabiltiy. Use your account addresses instead of the ones used in the tutorial. 

Secure funds for the accounts with the faucet:
```
> nctr faucet 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG \ 5HB4kbo67Hgv846DNMRnt7i1xNMum66LLBFkqtghKsNwRknM \ 5GxWKwbrPL88uH3Zv7zAiz6ozdpSFHzSfK1aXhVxDcNQYU8t
```

To register a community, you need to pass a specfile.json containing details about the community and an account with funds to the chain client. 

## Meetup Locations

We need to define in what region the community shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the community. You can use geojson.io to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered communities. 

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12. 

You can list all registered communities with:

```bash
> nctr list-communities
```
Our [explorer](https://explorer.encointer.org) allows you to browse all regsitered communities and their attributes on a map.

## Trusted Setup

Every local community needs a trusted setup. A trustworthy group of 3-12 local people will hold the bootstrapping ceremony publicly. These bootstrappers need to be defined in the metadata block. 

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

Use one of the bootstrappers to register your new community with the specfile.json:
```bash
> nctr new-community specfile.json 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D
```
Your community has been registered and the return value is your community-identifier (cid). Let's check the registry again:

```bash
> nctr list-communities
number of communities:  1 
A9xSvDWnV351uKh5Ni59xFwU2Q3t37J7MMp8tN2Gya9D: Mediterranea locations: 1
```