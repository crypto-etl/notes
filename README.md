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
```

## bitcoin-core
Mount volumes, start daemon
```
export chain=bitcoin-core
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/bitcoind
docker run -v /data:/data -it cryptoetl/$chain-docker nice -19 ./src/bitcoind -listenonion=0 -datadir=/data/bitcoind -onlynet=ipv4
```
Run blocksci
```
docker run -v /data:/data -v /blocksci:/blocksci -it allenday/blocksci-docker blocksci_parser --output-directory /blocksci/parser update disk --coin-directory /data/bitcoind
```

## dogecoin
Mount volumes, start daemon
```
export chain=dogecoin
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci

```
Run blocksci
```
XXX
```

## litecoin
Mount volumes, start daemon
```
export chain=litecoin
mount /dev/disk/by-id/google-blockchain-$chain /data
mount /dev/disk/by-id/google-blocksci-$chain /blocksci
mkdir -p /data/litecoind
docker run -e LITECOIN_DATA=/data/litecoind -v /data:/data -it uphold/litecoin-core -listenonion=0 -onlynet=ipv4 
```
Run blocksci
```
XXX
```

## ethereum
Mount volumes, start daemon
```
export chain=ethereum
mount /dev/disk/by-id/google-blockchain-$chain /data
docker run -v /data:/data -ti parity/parity --jsonrpc-interface=all --jsonrpc-port=8545 --jsonrpc-apis=all
 --tracing=on --pruning=archive --db-compaction=ssd -d /data -l info
 ```
