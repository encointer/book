# Testnet Cantillon Tutorial

## Connect!

You may watch Cantillon working by navigating to [polkadot.js.org/apps](https://polkadot.js.org/apps//?rpc=wss://cantillon.encointer.org#/explorer). 

If you're interested in node statistics, please refer to  [telemetry.polkadot.io](https://telemetry.polkadot.io/#list/Encointer%20Testnet%20Cantillon)

Cantillon features regular ceremonies every 3 days (Whereas the vision for mainnet is every 41 days).
You can either gather a few people and hold an actual meetup or you register a bot currency with virtual people like we will do in this tutorial.

## Client Setup

Get our cli client and start playing! The following instructions start from prebuilt binaries for ubuntu 18.04. If you use some other OS, you will have to [build the client yourself](./testnets.md).

```console
mkdir test
cd test
wget https://github.com/encointer/encointer-worker/releases/download/v0.6.11-sub2.0.0-alpha.7/encointer-client-teeproxy-0.6.11
chmod u+x encointer-client-teeproxy-0.6.11
ln -s encointer-client-teeproxy-0.6.11 encointer-client
./encointer-client -u wss://cantillon.encointer.org -p 443 get-phase
```

You should see either of REGISTERING, ASSIGNING or ATTESTING

for simplicity, we'll create an alias for the client

```console
# Cantillon node endpoint
NURL=wss://cantillon.encointer.org
NPORT=443
# Cantillon worker endpoint
WURL=wss://substratee03.scs.ch
WPORT=443
alias nctr="./encointer-client -u $NURL -p $NPORT -U $WURL -P $WPORT"
```

Encointer Cantillon uses *workers* to confidentially process your calls inside TEEs. In order to use Encointer currencies, you will be interacting with *workers*. You can list all available workers as follows: 

```bash
> nctr list-workers
number of workers registered: 1
Enclave 1
   AccountId: 5FBFhWyBgXsz6CtFzbgxprEmrtRLaiQBBsFHvK32vUeqUT7k
   MRENCLAVE: 3YM1AH5qdQAsh6BjYqDeYKQbuKgyDgNiSoFmqSUJTYvV
   RA timestamp: 2020-11-11 07:59:31 UTC
   URL: 127.0.0.1:19944
```

We may see a few enclaves and now we have to know what is the most recent Encointer enclave version as identified by MRENCLAVE. We suggest that you use our node which listens at https://substratee03.scs.ch as you have configured above. The URL in the registry is misleading due to an open issue. You will always be able to identify our node by its public signing key: `5FBFhWyBgXsz6CtFzbgxprEmrtRLaiQBBsFHvK32vUeqUT7k`.

```bash
MRENCLAVE=3YM1AH5qdQAsh6BjYqDeYKQbuKgyDgNiSoFmqSUJTYvV
```

Now we'll connect to a worker and request some publicly available information on an existing currency (aggregated values from its confidential state)

```bash
cid=7eLSZLSMShw4ju9GvuMmoVgeZxZimtvsGTSvLEdvcRqQ
nctr trusted info -m $MRENCLAVE --shard $cid
```

The query may be slow but you should see something like:

```console
Public information about currency 7eLSZLSMShw4ju9GvuMmoVgeZxZimtvsGTSvLEdvcRqQ
  total issuance: 19.6336295455992388671
  participant count: 0
  meetup count: 0
  ceremony reward: 1
  location tolerance: 1000m
  time tolerance: 600000ms
```

If you get such a response that means that you are successfully talking to our worker. Don't worry about the contents too much for now.

## Create Accounts

Now we'll create three *incognito* accounts for three virtual people that we will use for the remainder of the tutorial. In a real setting, every user will at least need one *public* and one *incognito* account but we'll use the same *public* account for all users for simplicity.

```bash
nctr trusted new-account -m $MRENCLAVE
nctr trusted new-account -m $MRENCLAVE
nctr trusted new-account -m $MRENCLAVE
```

This will create three accounts, in our case being these:

```console
5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex
5Dy4K5eNr13D37NcMcq4ffQZBAmt9BZhkgi5kBGuUWwK8cB7
5GCdWmdr5eZRvRPx6XE8YxFD472EvSMSTK6GQCHyuiNnw7rK
```

You can observe that a new keystore has been created in your working directory below `my_trusted_keystore`. 

*Incognito* keys do not need to be pre-funded as the Encointer STF does not charge fees. The underlying blockchain in fact does charge fees but for simplicity, our testnet client delegates all fee payments to Alice (with a well-known and hard-coded private key), who is very friendly and pays the fees for everyone. The Encointer Association will ensure that Alice doesn't run out of funds.

The CLI uses the keyword *trusted* for all calls involving *incognito* accounts. Under the hood this means that you're acutually interacting with a worker TEE. The blockchain is merely a proxy in this case, forwarding your request to the worker enclave. See [indirect invocation](https://www.substratee.com/design.html#indirect-invocation-current-implementation) for in-depth documentation.

## Bootstrap your own currency

Now we can start our own local currency and bootstrap a bot population with our three users! 

### Define Meetup Locations

First of all, we need to define in what region the currency shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the currency. You can use [geojson.io](http://geojson.io) to select meetup places on a map (define one or a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered currencies. You can list all registered currencies with

```bash
> nctr list-currencies
```

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12.

### Trusted Setup

Every local currency needs a trusted setup.
A trustworthy group of 3-12 local people will hold the bootstrapping ceremony publicly.
These bootstrappers need to be defined in the metadata block.
For improved privacy, we will use our incognito accounts for this that will never be used on-chain.

Now we create our currency specification:

```json
{
  "type": "FeatureCollection",
  "currency_meta": {
    "name": "my minimal test currency",
    "bootstrappers": [
      "5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex",
      "5Dy4K5eNr13D37NcMcq4ffQZBAmt9BZhkgi5kBGuUWwK8cB7",
      "5GCdWmdr5eZRvRPx6XE8YxFD472EvSMSTK6GQCHyuiNnw7rK"
    ]
  },
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Point",
        "coordinates": [
          11.25,
          40.03182061333687
        ]
      }
    }
  ]
}
```

Replace bootstrappers with your newly created accounts and replace the Point with your location of choice. Then save the above to minimal.json and register your new currency. Currencies are registered in public on-chain, so we'll ask Alice to pay for the fees.

```bash
nctr new-currency minimal.json //Alice
```
Your currency has been registered on-chain and the return value is your currency-identifier (cid). Store the cid to an env variable:

```console
cid=HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
```

Let's check the registry again:

```bash
> nctr list-currencies
number of currencies:  1
currency with cid HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
```

Cantillon features [sharding](https://www.substratee.com/sharding.html) and every new currency will have its own shard. The CLI client allows you to use different accounts on different shards. For this tutorial we'd like to use exactly the same accounts that we just created for the default shard (identified by the mrenclave). So we need to link our sharded keystore to the default shard:

```console
cd my_trusted_keystore
ln -sd E9h2hi91jn8Y9taz3JynF82sLkaUREY13XAhRWeu1fiR HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
cd ..
```

Now we will be able to use our keys for the new currency.

In order to bootstrap your bot currency, You'll need to register all of them for the next ceremony during the 16h registering phase. You can only start this procedure every 3 days!

```bash
#check if phase is REGISTERING
> nctr get-phase
REGISTERING
# ok, let's register, but first we will define a few variables and a new alias
> account1=5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex
> account2=5Dy4K5eNr13D37NcMcq4ffQZBAmt9BZhkgi5kBGuUWwK8cB7
> account3=5GCdWmdr5eZRvRPx6XE8YxFD472EvSMSTK6GQCHyuiNnw7rK
> nctr trusted register-participant $account1 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted register-participant $account2 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted register-participant $account3 --mrenclave $MRENCLAVE --shard $cid
# if everything goes well, you should be able to get your registration index >0:
> nctr trusted get-registration $account1 --mrenclave $MRENCLAVE --shard $cid
send TrustedGetter::get_registration for 5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex
Participant index: 1
```

The current client may throw an irrelevant error about decoding events. Just ignore that!

Now's a good time to check what public information is available about our currency at this time:

```console 
> nctr trusted info -m $MRENCLAVE --shard $cid
Public information about currency HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
  total issuance: 0
  participant count: undisclosed (might be REGISTERING phase?)
  meetup count: 0
  ceremony reward: 1
  location tolerance: 1000m
  time tolerance: 600000ms
```

So, right now, there is nothing to see. The participant count is deliberately undisclosed during REGISTERING to prevent information leakage about participants.

You'll have to wait until the ceremony phase turns to ASSIGNING. The worker enclave then assigns all participants to randomized groups that will have to meet at a random meetup locations at the upcoming ceremony.
During the ASSIGNING phase you can learn where and when exactly you will have to be for your meetup and how many people you're goint to meet. The CLI however, doesn't support that query. The [mobile phone app](./app.md) does, however. There is publicly available information we can query on the CLI:

```console
> nctr trusted info -m $MRENCLAVE --shard $cid
Public information about currency HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
  total issuance: 0
  participant count: 3
  meetup count: 1
  ceremony reward: 1
  location tolerance: 1000m
  time tolerance: 600000m
```

You can see your three participants have been assigned to one meetup. No money has ever been issued for this currency. Perfect.

The ceremony phase will change to ATTESTING on the date of the ceremony. The time of the ceremony will be high sun in your location. This way, no single person can attend more than one meetup. At each meetup, participants attest each others personhood. However, for a bot community this doesn't matter as they can jointly pretend to have met at the right time. (That's one of the reasons why we need a trusted *human* setup in the first place)

See [Time Warping](./testnets.html#time-warping-for-testnets) to learn how the timing maps between mainnet and Cantillon.

Our bot communities can perform meetups simply with the following lines. In our next tutorial, we will explain how to perform real physical meetups with our mobile phone app.

```bash
# each participant generates a claim of attendance including her vote on how many people N are actually physically present at that moment
claim1=$(nctr trusted new-claim $account1 3 --mrenclave $MRENCLAVE --shard $cid )
claim2=$(nctr trusted new-claim $account2 3 --mrenclave $MRENCLAVE --shard $cid )
claim3=$(nctr trusted new-claim $account3 3 --mrenclave $MRENCLAVE --shard $cid )
# this claim is then sent to all other participants who will verify them and sign an attestation
witness1_2=$(nctr trusted sign-claim $account1 $claim2 --mrenclave $MRENCLAVE --shard $cid)
witness1_3=$(nctr trusted sign-claim $account1 $claim3 --mrenclave $MRENCLAVE --shard $cid)
witness2_1=$(nctr trusted sign-claim $account2 $claim1 --mrenclave $MRENCLAVE --shard $cid)
witness2_3=$(nctr trusted sign-claim $account2 $claim3 --mrenclave $MRENCLAVE --shard $cid)
witness3_1=$(nctr trusted sign-claim $account3 $claim1 --mrenclave $MRENCLAVE --shard $cid)
witness3_2=$(nctr trusted sign-claim $account3 $claim2 --mrenclave $MRENCLAVE --shard $cid)
# and send that attestation back to the claimant who assembles all attestations and sends them to the chain
nctr trusted register-attestations $account1 $witness2_1 $witness3_1 --mrenclave $MRENCLAVE --shard $cid
nctr trusted register-attestations $account2 $witness1_2 $witness3_2 --mrenclave $MRENCLAVE --shard $cid
nctr trusted register-attestations $account3 $witness1_3 $witness2_3 --mrenclave $MRENCLAVE --shard $cid
# each participant can verify that her own attestations have been registered
nctr trusted get-attestations $account1 --mrenclave $MRENCLAVE --shard $cid
nctr trusted get-attestations $account2 --mrenclave $MRENCLAVE --shard $cid
nctr trusted get-attestations $account3 --mrenclave $MRENCLAVE --shard $cid
```

Now you have to wait for the ceremony phase to become REGISTERING.
Then we can verify that our bootstrapping was successful and our bootstrappers
have received their basic income issue on their accounts in units of the new currency

```bash
> nctr trusted balance $account1 --mrenclave $MRENCLAVE --shard $cid
arg_who = "5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex"
0.9999990985917757029
```

And the public information about our currency becomes:

```console
> nctr trusted info -m $MRENCLAVE --shard $cid
Public information about currency HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
  total issuance: 2.9999932394428882902
  participant count: undisclosed (might be REGISTERING phase?)
  meetup count: 0
  ceremony reward: 1
  location tolerance: 1000m
  time tolerance: 600000ms
```

Your new currency has a very special property called [demurrage](./economics-demurrage.md).
This means that the nominal value of your holdings decreases over time.
Currently it is halving every year.
You can observe this by waiting for a few blocks and checking your balance again.
Think of this demurrage like a solidarity fee that you pay to the decentralized "state"
that takes care of redistributing wealth among the local population at every ceremony
as newly issued basic income.

## What's next

Now that you bootstrapped your community currency, you should grow your population. At every subsequent ceremony you can add a few participants more but it is important to maintain reputation. At least 3/4 of all participants need to have attended the previous ceremony. So you can only grow your population at a pace that allows to build reputation.
