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
