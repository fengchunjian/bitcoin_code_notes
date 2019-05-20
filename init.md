# init

## main入口（#ifndef GUI）

```
// main入口
#ifndef GUI
int main(int argc, char* argv[])
{
    bool fRet = false;
	// 调取AppInit
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



