# Ubuntu Server 16.04.1 LTS 64位下编译并运行bitcoin-v0.15.1测试网络

### 依赖库安装

* 安装编译环境

```bash
apt-get -y install build-essential libtool autotools-dev \
 automake autoconf pkg-config bsdmainutils python3
```

* 安装必备依赖库

```
apt-get -y install libssl-dev libboost-all-dev libevent-dev
```

* 安装钱包依赖库

```
apt-get -y install libdb-dev libdb++-dev
```

* 安装其他依赖库

```
apt-get -y install libminiupnpc-dev libzmq3-dev
```

* 安装GUI依赖库

```
apt-get -y install libqt5gui5 libqt5core5a libqt5dbus5 \
 qttools5-dev qttools5-dev-tools libprotobuf-dev \
 protobuf-compiler libqrencode-dev
```

### 源代码编译

* 下载源代码

```bash
git clone https://github.com/bitcoin/bitcoin.git
cd bitcoin/
git checkout -b v0.15.1 v0.15.1
```

* 编译源代码

```bash
./autogen.sh
./configure --with-incompatible-bdb --prefix=/data/install/bitcoin
#--with-incompatible-bdb为忽略libdb版本差异
make
make install
```

* 其他编译选项

```bash
#--disable-wallet不编译钱包
#--without-gui不编译GUI
```

编译完成后，安装目录下文件为：

```bash
ls /data/install/bitcoin/bin/
bench_bitcoin  bitcoin-cli  bitcoind  bitcoin-qt  bitcoin-tx  test_bitcoin  test_bitcoin-qt
```

附官方文档：https://github.com/bitcoin/bitcoin/blob/master/doc/build-unix.md

g++: internal compiler error: Killed (program cc1plus)问题解决:

```bash
dd if=/dev/zero of=/swapfile bs=64M count=16
mkswap /swapfile
swapon /swapfile
```

* bitcoin-cli、bitcoind、bitcoin-qt区别

```
bitcoin-qt，带GUI的完整节点。
bitcoind，无GUI的完整节点。
bitcoin-cli，使用bitcoin-cli访问JSON-RPC接口。
```

### 启动bitcoind加入比特币测试网络

* 启动bitcoind

```bash
cd /data/install/bitcoin/bin/
./bitcoind --daemon -testnet
Bitcoin server starting
```

* 钱包及数据目录

```bash
cd ~/.bitcoin/testnet3/
ls -l
banlist.dat  bitcoind.pid  blocks  chainstate  database  db.log  debug.log  peers.dat  wallet.dat
```

* 停止bitcoind

```bash
./bitcoin-cli -testnet stop
Bitcoin server stopping
```

### bitcoin-cli访问JSON-RPC接口

* 获取节点统计信息

```bash
./bitcoin-cli -testnet getinfo
{
  "deprecation-warning": "WARNING: getinfo is deprecated and will be fully removed in 0.16. Projects should transition to using getblockchaininfo, getnetworkinfo, and getwalletinfo before upgrading to 0.16",
  "version": 150100,
  "protocolversion": 70015,
  "walletversion": 139900,
  "balance": 0.00000000,
  "blocks": 531765,
  "timeoffset": 0,
  "connections": 0,
  "proxy": "",
  "difficulty": 858629.5213440134,
  "testnet": true,
  "keypoololdest": 1516255482,
  "keypoolsize": 2000,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "errors": ""
}
```

* 获取区块链信息

```bash
./bitcoin-cli -testnet getblockchaininfo
{
  "chain": "test",
  "blocks": 721658,
  "headers": 1259495,
  "bestblockhash": "00000000000005c7be6d6a242e281756ea155ab248df12877c579439b3e9fcbe",
  "difficulty": 262144,
  "mediantime": 1456540510,
  "verificationprogress": 0.5614052534742976,
  "chainwork": "000000000000000000000000000000000000000000000007b2d3c70327f81d35",
  "pruned": false,
  "softforks": [
    {
      "id": "bip34",
      "version": 2,
      "reject": {
        "status": true
      }
    }, 
    {
      "id": "bip66",
      "version": 3,
      "reject": {
        "status": true
      }
    }, 
    {
      "id": "bip65",
      "version": 4,
      "reject": {
        "status": true
      }
    }
  ],
  "bip9_softforks": {
    "csv": {
      "status": "defined",
      "startTime": 1456790400,
      "timeout": 1493596800,
      "since": 0
    },
    "segwit": {
      "status": "defined",
      "startTime": 1462060800,
      "timeout": 1493596800,
      "since": 0
    }
  }
}
```

* 获取挖矿信息

```bash
./bitcoin-cli -testnet getmininginfo
{
  "blocks": 721883,
  "currentblockweight": 0,
  "currentblocktx": 0,
  "difficulty": 1,
  "errors": "",
  "networkhashps": 6860463578666.306,
  "pooledtx": 0,
  "chain": "test"
}
```

* 获取钱包信息

```bash
./bitcoin-cli -testnet getwalletinfo
{
  "walletname": "wallet.dat",
  "walletversion": 139900,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 0,
  "keypoololdest": 1516255482,
  "keypoolsize": 1000,
  "keypoolsize_hd_internal": 1000,
  "paytxfee": 0.00000000,
  "hdmasterkeyid": "1f264d1522573ed5b189940b4b80e60c1d8572e2"
}
```

* 获取钱包余额

```bash
./bitcoin-cli -testnet getbalance
0.00000000
```

更多JSON-RPC接口使用方法见官方文档：https://bitcoin.org/en/developer-reference#bitcoin-core-apis