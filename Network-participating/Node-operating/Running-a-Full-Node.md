# Running a full node

## Using Docker

Make sure Docker daemon is installed (see [here](Installing-Dependencies#Docker)) and running.

### Connecting to the MainNet
Then run a CENNZnet full node that connects to the MainNet:

```bash
$ docker run cennznet/cennznet:1.4.0 \
    # genesis is included in the image
    --chain=/cennznet/genesis/azalea.raw.json \
    --name=<NODE_NAME> \
    --telemetry-url 'ws://cennznet-telemetry.centrality.me:1024 0'
```

Check its status on the telemetry site:
http://cennznet-telemetry.centrality.me/#/CENNZnet-Azalea

### Connecting to Rata for development
[Rata](Getting-started/CENNZnet-technical-overview?id=cennznet-networks-and-genesis-files) is a playground network for testing. The following command would run a node that connects to Rata and  with full WebSocket connectivity enabled (unsafe for validators).
```bash
docker run cennznet/cennznet:b7923b1 --chain=/cennznet/genesis/rata.raw.json --bootnodes=/dns4/bootnode-rata-0.centrality.me/tcp/30333/p2p/12D3KooWA6iSdKNJUpvNPTRzxiLfA2LzPgCTywWP2ZeRnabDaDEa --unsafe-ws-external --ws-port=9944 --rpc-cors=all
```

## Building from Source

Make sure Rust is installed on your machine (see [here](Installing-Dependencies#Rust)).

Clone the repository and build the binary:

```bash
$ git clone https://github.com/cennznet/cennznet --branch release/1.2.0 && cd cennznet
$ cargo build --release
```

Then run the binary directly:
```bash
$ ./target/release/cennznet \
    --chain=genesis/azalea.raw.json \
    --name=<NODE_NAME> \
    --telemetry-url 'ws://cennznet-telemetry.centrality.me:1024 0'
```

## Chain Config Files

Config files for specific CENNZnet networks are available in the main repository [here](https://github.com/cennznet/cennznet/tree/develop/genesis).

The `<name>.raw.json` versions should be used as they're compatible with multiple client versions, while the `<name>.json` merely serves as a human readable reference of the config, but does not maintain compatibility between client versions.

## Enabling Ws/RPC Ports
The following combination of flags will allow the node's RPC and/or websocket functionality to be exposed.
```bash
# Allow ws connections on non-local interface
--ws-external \
# configure ws port
--ws-port 9944
        
# Allow rpc connections on non-local interface
--rpc-external \
# configure rpc port
--rpc-port 9933

# Allow connections from selected origins (default local only)
--rpc-cors
```

## Other Flags

To get detailed descriptions on the flags, run either of the following:
```bash
$ docker run cennznet/cennznet:1.4.0 --help  # using docker
$ ./target/release/cennznet -- --help        # using binary
```

## Synchronisation

When you start a node with a genesis file, it only holds the initial block. This means it only knows about the very beginning of CENNZnet but not any events that every other nodes acknowledges. Synchronisation is the process where the node learns the history (existing blocks) on the network, so it's up to date with the latest events on the MainNet.

The node finishes synchronising when the best block number is the same or close to the target block number. 
Example output from the node:

```
INFO ⚙️ Syncing 21.2 bps, target=#1140132 (1 peers), best: #532405 (0xfad8…07ea), finalized #531968 (0x1a24…d249), ⬇ 7.3kiB/s ⬆ 60 B/s
```