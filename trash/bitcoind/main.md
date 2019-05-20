# bitcoind之main

## main入口

```c++
int main(int argc, char* argv[])
{
	// 设置本地运行环境，按不同国家设置不同编码，用于国际化
	SetupEnvironment();

	// Connect bitcoind signal handlers
	// 连接比特币核心服务的信号处理函数
	noui_connect();

	// 应用程序初始化，初始化并启动
	return (AppInit(argc, argv) ? EXIT_SUCCESS : EXIT_FAILURE);
}
// 代码在bitcoind.cpp
```

## SetupEnvironment（设置本地运行环境）

```c++
void SetupEnvironment()
{
#ifdef HAVE_MALLOPT_ARENA_MAX
	// glibc-specific: On 32-bit systems set the number of arenas to 1.
	// By default, since glibc 2.10, the C library will create up to two heap
	// arenas per core. This is known to cause excessive virtual address space
	// usage in our usage. Work around it by setting the maximum number of
	// arenas to 1.
	// 判断当前系统是否为32位
	if (sizeof(void*) == 4) {
		// 如果是，则通过mallopt设置只有1个内存分配区，即表示系统按CPU进行自动设置
		mallopt(M_ARENA_MAX, 1);
		
		// 扩展阅读 http://blog.jobbole.com/83878/
		// glibc为了分配内存的性能的问题，使用了很多叫做arena的memory pool,缺省配置在64bit下面是每一个arena为64M，一个进程可以最多有 cores * 8个arena。
		// 假设你的机器是4核的，那么最多可以有4 * 8 = 32个arena，也就是使用32 * 64 = 2048M内存。
		// 当然你也可以通过设置环境变量来改变arena的数量.例如export MALLOC_ARENA_MAX=1
		// 在初始化的时候采用mallopt(M_ARENA_MAX, 1)将其关掉，设置为0，表示系统按CPU进行自动设置
	}
#endif
	// On most POSIX systems (e.g. Linux, but not BSD) the environment's locale
	// may be invalid, in which case the "C" locale is used as fallback.
// 如果不是WIN32、MAC_OSX、FreeBSD、OpenBSD
#if !defined(WIN32) && !defined(MAC_OSX) && !defined(__FreeBSD__) && !defined(__OpenBSD__)
	try {
		// 设置本地化
		// 如果当前区域设置无效, 则引发运行时错误
		std::locale(""); // Raises a runtime error if current locale is invalid
	} catch (const std::runtime_error&) {
		setenv("LC_ALL", "C", 1);
		// 扩展阅读https://blog.csdn.net/cherisegege/article/details/80548814
		// LC_ALL它是一个宏，如果该值设置了，则该值会覆盖所有LC_*的设置值。注意，LANG的值不受该宏影响。
		// C"是系统默认的locale，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的locale就是C或POSIX。
	}
#endif
	// The path locale is lazy initialized and to avoid deinitialization errors
	// in multithreading environments, it is set explicitly by the main thread.
	// A dummy locale is used to extract the internal default locale, used by
	// fs::path, which is then used to explicitly imbue the path.
	// 文件系统的本地化设置
	// 先用一个虚假的区域设置获取原来内部的区域设置，然后再显示的填充路径区域设置
	std::locale loc = fs::path::imbue(std::locale::classic());
	fs::path::imbue(loc);
}
// 代码在util.cpp
```

## noui_connect（连接比特币核心服务的信号处理函数）

```c++
void noui_connect()
{
	
    // Connect bitcoind signal handlers
	uiInterface.ThreadSafeMessageBox.connect(noui_ThreadSafeMessageBox);
	uiInterface.ThreadSafeQuestion.connect(noui_ThreadSafeQuestion);
	uiInterface.InitMessage.connect(noui_InitMessage);
}
// 代码在noui.cpp
```

## 参考文档

* [比特币源码解读一](https://www.jianshu.com/p/4a5b0c0f9984)
* [比特币源码研读之二](https://www.jianshu.com/p/dd36d13ba781)
* [比特币源码学习0.13（一）](https://blog.csdn.net/m0_37847176/article/details/81167128)
* [比特币源码解析(9) - 可执行程序 - Bitcoind](https://blog.csdn.net/aabbc59/article/details/79320647)
* [比特币源码剖析](https://mistydew.github.io/blog/2018/05/bitcoin-source-anatomy-00.html)
* [比特币源码剖析（一）](https://mistydew.github.io/blog/2018/05/bitcoin-source-anatomy-01.html)
* [为什么linux下多线程程序如此消耗虚拟内存](http://blog.jobbole.com/83878/)
* [LC_ALL=C的含义](https://blog.csdn.net/cherisegege/article/details/80548814)