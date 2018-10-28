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
docker run -v /data:/data -it cryptoetl/$chain-docker nice -19 ./src/bitcoind -listenonion=0 -datadir=/data/bitcoind -onlynet=ipv4
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

## dash
```
export chain=dash
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
docker run -v /data/dashd:/dash/.dashcore -p 9999:9999 -p 127.0.0.1:9998:9998 dashpay/dashd
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
docker run -p 3389:8332 -v /data:/data -it cryptoetl/dogecoin-docker nice -19 ./bin/dogecoind -listenonion=0 -datadir=/data/dogecoind -onlynet=ipv4 -server -rpcbind=0.0.0.0 -rpcallowip=10.0.0.0/8 -rpcuser=x -rpcpassword=x
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

## zcash
Mount volumes, start daemon
```
export chain=zcash
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/zcashd
docker run -v /data:/data cryptoetl/zcash-docker:latest /usr/bin/zcashd -datadir=/data/zcashd
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
