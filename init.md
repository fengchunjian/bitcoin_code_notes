# init

## main入口（#ifndef GUI）

```
// main入口
#ifndef GUI
int main(int argc, char* argv[])
{
    bool fRet = false;
	// 调取AppInit
	// 实际调取AppInit2
    fRet = AppInit(argc, argv);

    if (fRet && fDaemon)
        return 0;

    return 1;
}
#endif
// init.cpp
```

```
// fDaemon定义
extern bool fDaemon;
// util.h

// fDaemon赋值
#ifndef __WXMSW__
    fDaemon = GetBoolArg("-daemon");
#else
    fDaemon = false;
#endif
// init.cpp
```

## AppInit2实现

```
bool AppInit2(int argc, char* argv[])
{
    ...
    // Turn off microsoft heap dump noise
    // Disable confusing "helpful" text message on abort, ctrl-c
    // Clean shutdown on SIGTERM
    ...
    // 解析参数，写入extern std::map<std::string, std::string> mapArgs
	// 多值参数写入extern std::map<std::string, std::vector<std::string> > mapMultiArgs
    ParseParameters(argc, argv);
	...
	// 读取配置文件
	ReadConfigFile(mapArgs, mapMultiArgs); // Must be done after processing datadir
	...
	
	// Load data files
    printf("Loading addresses...\n");
    nStart = GetTimeMillis();
    if (!LoadAddresses())
        strErrors += _("Error loading addr.dat      \n");
    printf(" addresses   %15"PRI64d"ms\n", GetTimeMillis() - nStart);

	printf("Loading block index...\n");
    nStart = GetTimeMillis();
    if (!LoadBlockIndex())
        strErrors += _("Error loading blkindex.dat      \n");
    printf(" block index %15"PRI64d"ms\n", GetTimeMillis() - nStart);
	...
	
    if (!CheckDiskSpace())
        return false;

    RandAddSeedPerfmon();

	// 启动节点
    if (!CreateThread(StartNode, NULL))
        wxMessageBox("Error: CreateThread(StartNode) failed", "Bitcoin");

    if (fServer)
        CreateThread(ThreadRPCServer, NULL);
}
// init.cpp
```



