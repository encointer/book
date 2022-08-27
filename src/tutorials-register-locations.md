# Register new meetup locations

Once your community starts to grow, you'll have to make sure there are enough meetup locations. 
Otherwise less people will be assigned to ceremonies and your community will stall.

As a rule of thumb, you should always have at least one location for every 8 ceremony participants.

## define locations

We assume you have set up your environment along [basic cli usage](./tutorials-cli.md). 
Like you did when [registering your community](./tutorials-register-community.md), you can use geojson.io to define a geojson file with all the new locations you'd like to register

Let's define 4 new locations for the Adriana community on Gesell:

newlocations.json
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Point",
        "coordinates": [
          13.151750564575195,
          44.67957825832565
        ]
      }
    },
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Point",
        "coordinates": [
          13.127632141113281,
          44.680249583587546
        ]
      }
    },
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Point",
        "coordinates": [
          13.127717971801758,
          44.69733524402991
        ]
      }
    },
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Point",
        "coordinates": [
          13.151750564575195,
          44.69721322146929
        ]
      }
    }
  ]
}
```

## register locations

Registering locations is only possible during the *registering* phase of the [ceremony cycle](./protocol-ceremony-cycle.md). Check the current phase with

```
nctr-gsl get-phase
```

Register locations with

```
nctr-gsl add-locations newlocations.json --cid srcq45PYNyD --signer 5D5V3couq7o42FYkLG4vVhaqQPrfk4NT3kWzZJH66ZeHr3iG
```

You should see `encointerCommunities.LocationAdded` events for each of your added locations

If you now visit [explorer.encointer.org](https://explorer.encointer.org/?rpc=wss%3A%2F%2Fgesell.encointer.org) you should see the new locations if you zoom the adriatic sea.


## how to add locations on mainnet

On Encointer's production network, no single person is allowed to register new locations. At the current state, Encointer council has to approve new communities and locations

In order to prepare a proposal for council, you can use
```
nctr-k add-locations newlocations.json --cid u0qj944rhWE  --dryrun
```

The `--dryrun` flag will disable sending the extrinsic but will print the encoded call to *stdout* instead

like this:
```
0x32020c2800103e017530716a3977f79df70000d0943ad7f8ad2c00000000000000000000000020d9260d000000000000003e017530716a3977f79df700004aa032d624ae2c000000000000000080ffffff7fac200d000000000000003e017530716a3977f79df70000e674039084b22c00000000000000000000000020b2200d000000000000003e017530716a3977f79df70000a68cd0907cb22c00000000000000000000000020d9260d00000000000000cd02
```

