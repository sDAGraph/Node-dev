# Node-dev

## Bitcoin

```
mkdir -p bitcoin-source && cd bitcoin-source
git clone https://github.com/bitcoin/bitcoin.git
sudo apt-get install -y build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils python3
sudo apt-get install -y libboost-all-dev

sudo apt-get install -y libminiupnpc-dev
sudo apt-get install -y libzmq3-dev
sudo apt-get install -y libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
sudo apt-get install -y libqrencode-dev

cd bitcoin
git checkout 0.17
./autogen.sh

sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install -y libdb4.8-dev libdb4.8++-dev

./configure CPPFLAGS="-I${BDB_PREFIX}/include/ -O2" LDFLAGS="-L${BDB_PREFIX}/lib/" --with-gui
make
sudo make install
```

選擇性使用
```
mkdir ~/.bitcoin
cd ~/.bitcoin
touch ~/.bitcoin/debug.log
tail -f ~/.bitcoin/debug.log
```
圖形化界面
```
bitcoin-qt
```
非圖形化界面
```
mkdir btcData
bitcoind -datadir=./btcData
```
## Ethereum

```
git clone https://github.com/ethereum/go-ethereum
git checkout release/1.8
make
$path/go-ethereum/build/bin/geth
```
