# 代码量统计

## 代码量

```bash
// https://github.com/bitcoin/bitcoin/archive/v0.3.24.tar.gz
// 核心文件数
find ./ | grep -vE '\./json/|\./cryptopp/|\./test/|\./ui\.|\./uibase\.|\./wallet\.' | grep -E '\.cpp$|\.h$' | wc -l
26

// 核心代码行数
wc -l `find ./ | grep -vE '\./json/|\./cryptopp/|\./test/|\./ui\.|\./uibase\.|\./wallet\.' | grep -E '\.cpp$|\.h$'`
18989 total
```

## 代码行数分布

```bash
wc -l `find ./ | grep -vE '\./json/|\./cryptopp/|\./test/|\./ui\.|\./uibase\.|\./wallet\.' | grep -E '\.cpp$|\.h$'`
  1271 ./serialize.h
     6 ./rpc.h
   210 ./base58.h
    13 ./init.h
  1205 ./script.cpp
    86 ./strlcpy.h
   765 ./uint256.h
  2226 ./rpc.cpp
  1068 ./net.h
    69 ./noui.h
   554 ./init.cpp
   175 ./key.h
   939 ./db.cpp
    13 ./irc.h
    30 ./keystore.h
  3119 ./main.cpp
  1573 ./main.h
    33 ./keystore.cpp
   718 ./script.h
  1768 ./net.cpp
   448 ./irc.cpp
   117 ./headers.h
   909 ./util.cpp
   681 ./util.h
   456 ./db.h
   537 ./bignum.h
 18989 total
```

