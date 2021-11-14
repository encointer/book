# Explorer
The explorer is a Web UI tool to visualize chain data such as the communities and bootstrappers, the phases and ceremonies around the world.
A working example can be visited at [explorer.encointer.org](https://explorer.encointer.org).

Checkout the [explorer repository](https://github.com/encointer/explorer). <br>
In the terminal, go to the the root directory of the repo and enter: <br>
```console
git clone https://github.com/encointer/explorer
cd explorer
yarn install
yarn start
```
Then you should be able to view the explorer in your browser at http://localhost:8000/. <br>
At the bottom, the registered chain is displayed. You can click on it and change between the local and remote chain.
You can also set the rpc address via the query paramter, for example: <br>
`localhost:8000?rpc=ws://127.0.0.1:9945` will connect to the chain on localhost with port 9945. <br>
The registered community should be visible in the explorer.
