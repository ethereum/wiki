The [Ethereum (centralised) network status monitor](http://eth-netstats.herokuapp.com) (known sometimes as "eth-netstats") is a web-based application to monitor the health of the testnet/mainnet through a group of nodes.

### Listing

To list your node, you must install the client-side information relay, a node module. Instructions given here work on Ubuntu. Other platforms vary.

Clone the git repo, then install pm2:

```
git clone https://github.com/cubedro/eth-net-intelligence-api
cd eth-net-intelligence-api
sudo npm install -g pm2
```

Then edit the `app.json` file in it to configure for your node:

- alter the value to the right of `cwd` to the path of `eth-net-intelligence-api` (including the `eth-net-intelligence-api` part);
- alter the value to the right of `INSTANCE_NAME` to whatever you wish to name your node;
- and alter the value to the right of `WS_SECRET` to the secret (you'll have to get this off an ethÐΞV official if you don't have one).

Finally run the process with:

```
pm2 start app.json
```

Several commands are available:

- `pm2 list` to display the process status;
- `pm2 logs` to display logs;
- `pm2 gracefulReload node-app` for a soft reload;
- `pm2 stop node-app` to stop the app;
- `pm2 kill` to kill the daemon.
