
## 内存分析
1. [adb shell dumpsys](../tools/dumpsys.md)
2. [APT](../tools/apt.md)

## 频繁GC

**分析工具和方法**

0. 在Android Studio的android monitor查看GC情况
1. 使用Android Device Monitor的工具Dump HPROF file两次，一次是GC之前，一次是GC之后，可以在ADM中手动触发Cause GC
2. 使用hprof-conv转换hprof文件到MAT支持的格式
3. 使用MAT打开对比的hprof，查看Unreachable Objects Histogram视图，就可以看到尚未被回收的对象，重点关注自定义的类
4. 回头在Android Device Monitor中Allocation Tracker中跟踪一段时间内的对象创建，就能很方便找到申请位置。


**最佳实践**

1. 遍历List读操作的时候用for(int i=0; i<.size; ++i)格式较好，如用foreach，编译器会直接翻译成迭代器方式遍历（可以直接查看.class代码），导致创建很多java.util.ArrayList$ArrayListIterator临时对象。foreach好处是语法简单，使用迭代器的好处是删除、修改列表方便。 性能上，针对ArrayList来说，索引遍历略好于迭代器；LinkedList迭代器性能更优。因为ArrayList内部是数组，LinkedList是链表
		
		// 索引遍历
    	int size = list.size();
    	for (int j = 0; j < size; j++) {
    		list.get(j);
    	}
		// 迭代器
		Iterator<Integer> iterator = list.iterator();
		while(iterator.hasNext()) {
			Integer j = iterator.next();
		}

2. 绝对不要手动调用 Message#recycle。因为 Looper 已经帮我们处理好手尾了。如自己手动回收会怎样？因为 Looper 在调用 msg.recycle() 前并没有作检查，不会对进入池中的对象是否已存在进行检查，一旦同一个 Message 被回收两次，对象池将会被破坏。
3. 可以在初始化的过程中计算好的变量尽量在开始处理好或者第一次用的时候处理，不要在使用运行过程中反复计算。
4. JNI编程中，java和JNI之间传递数据尽量使用primary type，不要为了方便直接返回数据对象，这样会导致创建很多java临时对象，浪费内存，可以考虑回调的方式传回多个返回值，参考[android jni return multiple variables](http://stackoverflow.com/questions/29043872/android-jni-return-multiple-variables)，对于调用JNI接口同Java方法的性能对比，参考[这里](http://stackoverflow.com/questions/13973035/what-is-the-quantitative-overhead-of-making-a-jni-call)
5. JNI中，在初始化JNI_OnLoad的时候把Java类的jclass引用、以及MethodID获取到并缓存成全局变量，后期直接使用。

## 术语

• VSS - Virtual Set Size 虚拟耗用内存（包含共享库占用的内存）
• RSS - Resident Set Size 实际使用物理内存（包含共享库占用的内存）
• PSS - Proportional Set Size 实际使用的物理内存（比例分配共享库占用的内存）
• USS - Unique Set Size 进程独自占用的物理内存（不包含共享库占用的内存）


- **VSS** (reported as VSZ from ps) is the total accessible address space of a process. This size also includes
memory that may not be resident in RAM like mallocs that have been allocated but not written to.VSS is of very little use for determing real memory usage of a process
- **RSS** is the total memory actually held in RAM for a process. RSS can be misleading, because it reports the total all of the shared libraries that the process uses, even though a shared library is only loaded into memory once regardless of how many processes use it.RSS is not an accurate representation of the memory usage for a single process.
- **PSS** differs from RSS in that it reports the proportional size of its shared libraries, i.e. if three processes all use a shared library that has 30 pages, that library will only contribute 10 pages to the PSS that is reported for each of the three processes.PSS is a very useful number because when the PSS for all processes in the system are summed together, that is a good representation for the total memory usage in the system. When a process is killed, the shared libraries that contributed to its PSS will be proportionally distributed to the PSS totals for the remaining processes still using that library. In this way PSS can be slightly misleading, because when a process is killed, PSS does not accurately represent the memory returned to the overall system.
- **USS** is the total private memory for a process, i.e. that memory that is completely unique to that process. USS is an extremely useful number because it indicates the true incremental cost of running a particular process. When a process is killed, the USS is the total memory that is actually returned to the system. USS is the best number to watch when initially suspicious of memory leaks in a process.


来自 <http://blog.csdn.net/edisonlg/article/details/7082063> 