# Indexer

If you run your own node, you may want to run your own indexer for better privacy and autonomy.

## LiteScan

Encointer uses a very simple low-level indexer called [LiteScan](https://github.com/pifragile/litescan) and extends it with its own types.

### Setup 

We assume you're running your node on the same ubuntu 22.04 machine. We want to run the indexer as a service, feeding into a mongodb database.

```bash
sudo apt install -y mongodb-org curl
```
To run a node program as a service, install [PM2](https://pm2.io/docs/runtime/guide/installation/)
```bash
sudo npm install pm2 -g
```

Install nvm and node >=20
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install 20
```


```bash
git clone https://github.com/brenzi/litescan.git
cd litescan
git checkout encointer
npm install
```

### MongoDB

We encourage you to pick strong passwords in the following steps. We will create the `admin` user and `litescan`with r/w permission as well as `encointer`with read permission for queries 

```bash
sudo service mongod start
mongosh
use admin
db.createUser( { user: "admin", pwd: "*****", roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] } )
use encointer-kusama
db.createUser({user: "litescan", pwd: "******", roles: [{ role: "readWrite", db: "encointer-kusama" }]})
db.createUser({user: "encointer", pwd: "*******", roles: [{ role: "read", db: "encointer-kusama" }]})
```

### Test Indexer

Now we need to configure the indexer. Create a `.env` file in the repo root. Make sure to adjust according to your setup.

```bash 
DB_URL=mongodb://litescan:*******@localhost:27017/?authSource=encointer-kusama
NUM_CONCURRENT_JOBS=1000
START_BLOCK=508439
DB_NAME=encointer-kusama
RPC_NODE=ws://127.0.0.1:9944
```

Now you can run the indexer

```bash
node index.js
```

you should see block sync happening

### Run as Service

To make sure the indexer is always up, we run it as a service. 

```bash
pm2 start index.js --name encointer-indexer
pm2 save
pm2 startup
```
And execute the suggested command to make the service start on boot.


## Querying

You can now run any [MongoDB Query](https://www.mongodb.com/docs/manual/tutorial/query-documents/) against the database. 

We recommend [mongodb compass](https://www.mongodb.com/products/tools/compass) to browse the database

As a simple cli example, let's query the highest indexed block:

```bash
mongosh --username encointer --eval "db.blocks.find().sort({height: -1}).limit(1)" encointer-kusama
``` 
