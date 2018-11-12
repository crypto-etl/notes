# notes

## setup, all nodes

Install docker
```
apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys F76221572C52609D
apt-get -y install apt-transport-https ca-certificates curl software-properties-common dirmngr
echo 'deb https://apt.dockerproject.org/repo debian-stretch main' >> /etc/apt/sources.list
apt-get -y update
apt-get -y install docker-engine
```
Initialize volumes
```
export chain=FIXME
mkfs.ext4 /dev/disk/by-id/google-blockchain-$chain
mkfs.ext4 /dev/disk/by-id/google-blocksci-$chain
mkdir /data
mkdir /blocksci
```

## bitcoin-cash
Mount volumes, start daemon
```
export chain=bitcoin-cash
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/bitcoind
docker run -p 3389:8332 -v /data:/data -it cryptoetl/$chain-docker nice -19 ./src/bitcoind -listenonion=0 -datadir=/data/bitcoind -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x
```
Run blocksci
```
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_parser --output-directory /blocksci/parser update disk --coin-directory /data/bitcoind
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_clusterer /blocksci/parser /blocksci/clusterer --overwrite
```
Issues
* Failing to do incremental updates. Always restarts.

## bitcoin-core
Mount volumes, start daemon
```
export chain=bitcoin-core
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/bitcoind
docker run -p 3389:8332 -v /data:/data -it cryptoetl/$chain-docker nice -19 ./src/bitcoind -listenonion=0 -datadir=/data/bitcoind -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x
```
Run blocksci
```
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_parser --output-directory /blocksci/parser update disk --coin-directory /data/bitcoind
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_clusterer /blocksci/parser /blocksci/clusterer --overwrite
```
Issues
* None :)

## bitcoin-diamond
```
docker run -e BITCOINDIAMOND_DATA=/data/bitcoindiamondd -v /data:/data -it messari/bitcoin-diamond bitcoindiamondd -listenonion=0 -datadir=/data/bitcoindiamondd -onlynet=ipv4 -txindex=1
```

## bitcoin-gold
```
docker run -e BITCOIN_GOLD_DATA=/data/bgoldd -v /data:/data -it cryptoetl/bitcoin-gold bgoldd -listenonion=0 -datadir=/data/bgoldd -onlynet=ipv4 -txindex=1
```

## dash
```
export chain=dash
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
#dogecoind rpc user/pass specified in config file
docker run -v /data/dashd:/dash/.dashcore -p 3389:8332 dashpay/dashd
```
Run blocksci
```
TODO
```
Issues

## dogecoin
Mount volumes, start daemon
```
export chain=dogecoin
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
#dogecoind rpc user/pass specified in config file
docker run -p 3389:8332 -v /data:/data -it cryptoetl/dogecoin-docker nice -19 ./bin/dogecoind -listenonion=0 -datadir=/data/dogecoind -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcallowip=10.0.0.0/8
```
Run blocksci
```
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_parser --output-directory /blocksci/parser update disk --coin-directory /data/dogecoind
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_clusterer /blocksci/parser /blocksci/clusterer --overwrite
```
Issues
* `blocksci_parser` silently fails to recognize contents of `dogecoind` 

## litecoin
Mount volumes, start daemon
```
export chain=litecoin
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/litecoind
docker run -p 3389:9332 -e LITECOIN_DATA=/data/litecoind -v /data:/data -it uphold/litecoin-core -listenonion=0 -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x 
```
Run blocksci
```
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_parser --output-directory /blocksci/parser update disk --coin-directory /data/litecoind
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_clusterer /blocksci/parser /blocksci/clusterer --overwrite
```
Issues
* None :)

## namecoin
Mount volumes, start daemon
```
docker run -v /data/namecoin:/data/namecoin acejam/namecoin:latest namecoind -datadir=/data/namecoin -txindex=1
-OR-
docker run -p 3389:8232 -v /data:/data allenday/namecoin-core-docker namecoind -listenonion=0 -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcport=8232 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x -datadir=/data/namecoind -onlynet=ipv4 -txindex=1
```
Run blocksci
```
TODO
```

## zcash
Mount volumes, start daemon
```
export chain=zcash
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/zcashd
docker run -p 3389:8232 -v /data:/data cryptoetl/zcash-docker:latest /usr/bin/zcashd -datadir=/data/zcashd -listenonion=0 -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcport=8232 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x
```
Run blocksci
```
TODO
```

## steem
Mount volumes, start daemon
```
export chain=steem
mount /dev/disk/by-id/google-blockchain-$chain /data
docker run -v /data/steemd:/var/lib/steemd --env USE_WAY_TOO_MUCH_RAM=1 --env USE_FULL_WEB_NODE=1 -p 2001:2001 -p 8090:8090 --restart unless-stopped steemit/steem
```

## ethereum
Mount volumes, start daemon
```
export chain=ethereum
mount /dev/disk/by-id/google-blockchain-$chain /data
docker run -v /data:/data -p 3389:8545 -ti parity/parity:v2.0.8 --jsonrpc-interface=all --jsonrpc-port=8545 --jsonrpc-apis=all --tracing=on --pruning=archive --db-compaction=ssd -d /data -l info
 ```
 Issues
 `TBD`
 
 ## blocksci flask
 ```
 docker run --rm -d --name flask-bitcoin -p 9001:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/bitcoin.cfg -it allenday/docker-blocksci-flask:v0.6
docker run --rm -d --name flask-bitcoin-cash -p 9002:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/bitcoin-cash.cfg -it allenday/docker-blocksci-flask:v0.6
#docker run --rm -d --name flask-bitcoin-diamond -p 9003:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/bitcoin-diamond.cfg -it allenday/docker-blocksci-flask:v0.6
#docker run --rm -d --name flask-bitcoin-gold -p 9004:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/bitcoin-gold.cfg -it allenday/docker-blocksci-flask:v0.6
docker run --rm -d --name flask-dash -p 9005:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/dash.cfg -it allenday/docker-blocksci-flask:v0.6
#docker run --rm -d --name flask-dogecoin -p 9006:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/dogecoin.cfg -it allenday/docker-blocksci-flask:v0.6
docker run --rm -d --name flask-litecoin -p 9007:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/litecoin.cfg -it allenday/docker-blocksci-flask:v0.6
docker run --rm -d --name flask-namecoin -p 9008:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/namecoin.cfg -it allenday/docker-blocksci-flask:v0.6
docker run --rm -d --name flask-zcash -p 9009:5000 -v /data:/data -e BLOCKSCI_PARSER_FILES_LOC=/data/dev/blocksci-cfg/zcash.cfg -it allenday/docker-blocksci-flask:v0.6
```
