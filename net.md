# net

## StartNode实现

```
void StartNode(void* parg)
{
	// 定义extern CNode* pnodeLocalHost;
	// 初始化CNode* pnodeLocalHost = NULL;
    if (pnodeLocalHost == NULL)
        pnodeLocalHost = new CNode(INVALID_SOCKET, CAddress("127.0.0.1", 0, false, nLocalServices));
		
	// Get local host ip
	...
	// 获取外网IP
	CreateThread(ThreadGetMyExternalIP, NULL);
	...
    // Get addresses from IRC and advertise ours
	// 从IRC获取地址
    if (!CreateThread(ThreadIRCSeed, NULL))
        printf("Error: CreateThread(ThreadIRCSeed) failed\n");

    // Send and receive from sockets, accept connections
	// 接受连接，发送或接收
    pthread_t hThreadSocketHandler = CreateThread(ThreadSocketHandler, NULL, true);

    // Initiate outbound connections
	// 初始化出站连接
    if (!CreateThread(ThreadOpenConnections, NULL))
        printf("Error: CreateThread(ThreadOpenConnections) failed\n");

    // Process messages
	// 处理消息
    if (!CreateThread(ThreadMessageHandler, NULL))
        printf("Error: CreateThread(ThreadMessageHandler) failed\n");

    // Generate coins in the background
	// 后台挖矿
    GenerateBitcoins(fGenerateBitcoins, pwalletMain);
}
// net.cpp
```

## CAddress定义

```
class CAddress
{
public:
    uint64 nServices;
    unsigned char pchReserved[12];
    unsigned int ip;
    unsigned short port;

    // disk and network only
    unsigned int nTime;

    // memory only
    unsigned int nLastTry;
	...
}
// net.h
```

## CNode定义

```
class CNode
{
public:
    // socket
    uint64 nServices;
    SOCKET hSocket;
    CDataStream vSend;
    CDataStream vRecv;
    CCriticalSection cs_vSend;
    CCriticalSection cs_vRecv;
    int64 nLastSend;
    int64 nLastRecv;
    int64 nLastSendEmpty;
    int64 nTimeConnected;
    unsigned int nHeaderStart;
    unsigned int nMessageStart;
    CAddress addr;
    int nVersion;
    std::string strSubVer;
    bool fClient;
    bool fInbound;
    bool fNetworkNode;
    bool fSuccessfullyConnected;
    bool fDisconnect;
protected:
    int nRefCount;
public:
    int64 nReleaseTime;
    std::map<uint256, CRequestTracker> mapRequests;
    CCriticalSection cs_mapRequests;
    uint256 hashContinue;
    CBlockIndex* pindexLastGetBlocksBegin;
    uint256 hashLastGetBlocksEnd;
    int nStartingHeight;

    // flood relay
    std::vector<CAddress> vAddrToSend;
    std::set<CAddress> setAddrKnown;
    bool fGetAddr;
    std::set<uint256> setKnown;

    // inventory based relay
    std::set<CInv> setInventoryKnown;
    std::vector<CInv> vInventoryToSend;
    CCriticalSection cs_inventory;
    std::multimap<int64, CInv> mapAskFor;

    // publish and subscription
    std::vector<char> vfSubscribe;
	...
}
// net.h
```