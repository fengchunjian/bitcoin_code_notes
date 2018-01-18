# Ubuntu Server 16.04.1 LTS 64位下编译bitcoin-v0.15.1

### 依赖库安装

安装编译环境
```
apt-get -y install build-essential libtool autotools-dev automake autoconf pkg-config bsdmainutils python3
```

*build-essential提供编译程序必须软件包的列表信息。*
*libtool是一个通用库支持脚本，将使用动态库的复杂性隐藏在统一、可移植的接口中。*
*autotools是个系列工具，主要由autoconf、automake、perl语言环境和m4等组成。*
*pkg-config可以通过库提供的一个.pc文件获得库的各种必要信息，包括版本信息、编译和连接需要的参数等。*

安装必备依赖库
* apt-get -y install libssl-dev libboost-all-dev libevent-dev

*OpenSSL是一个安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议。*
*Boost库是一个可移植、提供源代码的C++库，作为标准库的后备，是C++标准化进程的开发引擎之一。*
*Libevent 是一个用C语言编写的、轻量级的开源高性能事件通知库。*

安装钱包依赖库
* apt-get -y install libdb-dev libdb++-dev

安装其他依赖库
* apt-get -y install libminiupnpc-dev libzmq3-dev

安装GUI依赖库
* apt-get -y install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler libqrencode-dev

### 源代码编译

下载源代码
* git clone https://github.com/bitcoin/bitcoin.git
* cd bitcoin/
* git checkout -b v0.15.1 v0.15.1

编译源代码
* ./autogen.sh
* ./configure --with-incompatible-bdb --prefix=/data/install/bitcoin
--with-incompatible-bdb为忽略libdb版本差异
* make
* make install

其他编译选项
* --disable-wallet不编译钱包
* --without-gui不编译GUI

编译完成后，安装目录下文件为：

附官方文档：https://github.com/bitcoin/bitcoin/blob/master/doc/build-unix.md

g++: internal compiler error: Killed (program cc1plus)问题解决:
* dd if=/dev/zero of=/swapfile bs=64M count=16
* mkswap /swapfile
* swapon /swapfile
