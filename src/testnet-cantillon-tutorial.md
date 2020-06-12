# Testnet Gesell Tutorial

## Connect!

You may watch Cantillon working by navigating to [polkadot.js.org/apps](https://polkadot.js.org/apps). Then you connect to our node by setting our custom endpoint at Settings -> General
Set the endpoint to `wss://cantillon.encointer.org`

Hit "save" and you should see the explorer loading for Cantillon.

If you're interested in node statistics, please refer to  [telemetry.polkadot.io](https://telemetry.polkadot.io/#list/Encointer%20Testnet%20Cantillon)

Cantillon features regular ceremonies every 3 days (Whereas the vision for mainnet is every 41 days).
You can either gather a few people and hold an actual meetup or you register a bot currency with virtual people like we will do in this tutorial.

## Client Setup

Get our cli client and start playing! The following instructions start from prebuilt binaries for ubuntu 18.04. If you use some other OS, you will have to build the client yourself.

```bash
> mkdir test
> cd test
> wget https://github.com/encointer/encointer-worker/releases/download/M1/encointer-client
> chmod u+x encointer-client
> ./encointer-client -u wss://cantillon.encointer.org -p 443 get-phase
# you should see either of REGISTERING, ASSIGNING or ATTESTING
# for simplicity, we'll create an alias for the client
# Cantillon node endpoint
> NURL=wss://cantillon.encointer.org
> NPORT=443
# Cantillon worker endpoint
> WURL=wss://substratee03.scs.ch
> WPORT=443
> alias nctr="./encointer-client -u $NURL -p $NPORT -U $WURL -P $WPORT"
# let's create a new account
> nctr new-account
5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU
# you can now check that your local keystore has a new entry
> ls my_keystore
73723235ae365cf166bab30448f25b3751b06d034be9c992a8ba5501d3adcde640ab9b1e
# query on-chain ERT balance
> nctr balance 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU
ERT balance for 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU 0
```

As with other blockchains, you'll need some funds in order to pay fees.
As you can read in our whitepaper, we'll avoid this entry barrier in the future.
We have a faucet in place that gets you started immediately:

```bash
> nctr faucet 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU
```

Should the faucet be exhausted, please post a message to our riot channel and friendly request some topup. Please be patient.

```bash
> nctr balance 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU
ERT balance for 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU is 998999854
# now you could send around your new ERT
> nctr transfer 5GjmTbGuPPXwBSNvi6SVbTvwBxNTttiFyFvYAweZz221JESU 5G18LaJA315RwJqtYYbWrbE52g9FEQCgBYN1A1XG66XnKAw5 123456789
```

Now let's see all the workers who have registered their enclaves:

```bash
> nctr list-workers
number of workers registered: 2
Enclave 1
   AccountId: 5DBXHQgjd6CVuzHnoyXVW8WatuDi2rfhH57yDwVZ3ZzMgYiX
   MRENCLAVE: 5RqYVKwQdyvPqAPwauWz9oQMRcU9dw9M1vKfPWx81bgh
   RA timestamp: 2020-06-10 11:42:53 UTC
   URL: 127.0.0.1:2000
Enclave 2
   AccountId: 5Eztpox9YRidTCReasMep5m7x3vvXSFpmbjfHhihw7QqNyZY
   MRENCLAVE: HVmtypxe23ngaWWaRYmAtKWThuYeXL5V1nUALpQQvC3A
   RA timestamp: 2020-06-11 09:14:32 UTC
   URL: 127.0.0.1:19944
```

We see two enclaves and we have to know what is the most recent Encointer enclave version as identified by MRENCLAVE. In this case, Enclave 1 is outdated, so we chose the second one:

```bash
MRENCLAVE=HVmtypxe23ngaWWaRYmAtKWThuYeXL5V1nUALpQQvC3A
```

## Bootstrap your own currency

Now that we have some funds to pay platform fees, we can start our own local currency and bootstrap a bot population! 
Define Meetup Locations

First of all, we need to define in what region the currency shall be issued. For this we use the geojson standard to define a set of meetup places and add some meta-information about the currency. You can use geojson.io to select meetup places on a map (define a few "Points"). Make sure that you select places that are >100m apart. You also need to keep this minimal distance from other registered currencies. You can list all registered currencies with

```bash
> nctr list-currencies
```

The number of locations that you should define depends on the size of the population N you'd like to bootstrap. As a rule of thumb, there should be at least N locations in order to guarantee reasonable randomization. As a maximum of 12 people can attend the same meetup the hard lower limit is N/12. 

## Trusted Setup

Every local currency needs a trusted setup.
A trustworthy group of 3-12 local people will hold the bootstrapping ceremony publicly.
These bootstrappers need to be defined in the metadata block.
For improved privacy, we will use incognito accounts for this that will never be used on-chain.

```bash
> nctr trusted new-account --mrenclave $MRENCLAVE
5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex
```

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

You can now save the above to minimal.json and register your new currency. Store it to an env variable:

```bash
> nctr new-currency minimal.json 5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex
HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
> cid=HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
```

Your currency has been registered on-chain and the return value is your currency-identifier (cid). 
Let's check the registry again:

```bash
> nctr list-currencies
number of currencies:  1
currency with cid HKKAHQhLbLy8b84u1UjnHX9Pqk4FXebzKgtqSt8EKsES
```

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

Now you'll have to wait until the ceremony phase turns to ASSIGNING. The worker enclave then assigns all participants to randomized groups that will have to meet at a random meetup locations at the upcoming ceremony.
During the ASSIGNING phase you can learn where and when exactly you will have to be for your meetup and how many people you're goint to meet. The CLI however, doesn't support that query. The mobile phone app will.

The ceremony phase will change to ATTESTING on the date of the ceremony. The time of the ceremony will be high sun in your location. This way, no single person can attend more than one meetup. At each meetup, participants attest each others personhood. However, for a bot community this doesn't matter as they can jointly pretend to have met at the right time. (That's one of the reasons why we need a trusted *human* setup in the first place)

See [Time Warping](./testnets.html#time-warping-for-testnets) to learn how the timing maps between mainnet and Cantillon.

Our bot communities can perform meetups simply with the following lines. In our next tutorial, we will explain how to perform real physical meetups with our mobile phone app.

```bash
# each participant generates a claim of attendance including her vote on how many people N are actually physically present at that moment
> claim1=$(nctr trusted new-claim $account1 3 --mrenclave $MRENCLAVE --shard $cid )
> claim2=$(nctr trusted new-claim $account2 3 --mrenclave $MRENCLAVE --shard $cid )
> claim3=$(nctr trusted new-claim $account3 3 --mrenclave $MRENCLAVE --shard $cid )
# this claim is then sent to all other participants who will verify them and sign an attestation 
> witness1_2=$(nctr sign-claim $account1 $claim2)
> witness1_3=$(nctr sign-claim $account1 $claim3)
> witness2_1=$(nctr sign-claim $account2 $claim1)
> witness2_3=$(nctr sign-claim $account2 $claim3)
> witness3_1=$(nctr sign-claim $account3 $claim1)
> witness3_2=$(nctr sign-claim $account3 $claim2)
# and send that attestation back to the claimant who assembles all attestations and sends them to the chain
> nctr trusted register-attestations $account1 $witness2_1 $witness3_1 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted register-attestations $account2 $witness1_2 $witness3_2 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted register-attestations $account3 $witness1_3 $witness2_3 --mrenclave $MRENCLAVE --shard $cid
# each participant can verify that her own attestations have been registered
> nctr trusted get-attestations $account1 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted get-attestations $account2 --mrenclave $MRENCLAVE --shard $cid
> nctr trusted get-attestations $account3 --mrenclave $MRENCLAVE --shard $cid
```

Now you have to wait for the ceremony phase to become REGISTERING.
Then we can verify that our bootstrapping was successful and our bootstrappers
have received their basic income issue on their accounts in units of the new currency

```bash
> nctr trusted balance $account1 --mrenclave $MRENCLAVE --shard $cid
arg_who = "5EcDWHsGzERpiP3ZBoFfceHpinBeifq5Lh1VnCkzxca9f9ex"
1
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
