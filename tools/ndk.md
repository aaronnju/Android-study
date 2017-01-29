
为了分析NDK相关问题，NDK提供了几个工具：

- addr2line  

		cd "C:\android-ndk-r10e\toolchains\arm-linux-androideabi-4.9\prebuilt\windows-x86_64\bin"
		./arm-linux-androideabi-addr2line.exe -C -f -e xxx.so 00096afb(backtrace段中的地址)
xxx.so 是调试版本的so库，一般在发布版本的时候，含有调试信息的 so，路径在 obj/local/目录下，release版本的so在 libs 目录下，提供给客户的是libs下，同时把obj下面做好备份，出问题时做分析。

- objdump

	导出so的函数表信息

- ndk-stack

		./ndk-stack.exe -sym "C:\...\obj\local\armeabi" -dump heap_error_log.txt
