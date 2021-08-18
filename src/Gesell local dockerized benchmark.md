# Gesell local dockerized benchmark
Follow the instruction on [docker-tutorial](https://hub.docker.com/r/nomelc/encointer-notee) to get the docker image and required yml files.
## Launch local docker environment with Gesell node and comminities
After the docker-compose file is set-up, you can start the docker containers with 
```console
docker-compose up -d
```
This will start a gesell node, 3 bot communities that will grow, a faucet script to ensure funds and a phase shifting script that changes every 10 blocks to a new phase (REGISTERING, ATTESTING, ???)
## Watch communities grow on explorer

## Watch performance metrics (prometheus/grafana)
According to the [docker-tutorial](https://hub.docker.com/r/nomelc/encointer-notee), grafana and prometheus should be set-up. 
Now, visit localhost:3000 and view the preformance metrics in your dashboard.
