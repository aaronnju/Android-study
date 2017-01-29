## 第2章 Java 内存

### java 运行时数据区

![image](http://upload-images.jianshu.io/upload_images/2614605-246286b040ad10c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



1. 程序 计数器（ Program Counter Register） 是一 块 较小 的 内存 空间， 它可 以 看作 是 当前 线程 所 执行 的 字节 码 的 行号 指示器。
2. 虚拟 机 栈 描述 的 是 Java 方法 执行 的 内存 模型： 每个 方法 在 执行 的 同时 都会 创建 一个 栈 帧（ Stack Frame） 用于 存储 局部 变 量表、 操 作数 栈、 动态 链接、 方法 出口 等 信息。
3. 本地 方法 栈（ Native Method Stack） 与 虚拟 机 栈 所 发挥 的 作用 是非 常 相似 的， 它们 之间 的 区别 不过 是 虚拟 机 栈 为 虚拟 机 执行 Java 方法（ 也就是 字节 码） 服务， 而 本地 方法 栈 则为 虚拟 机 使 用到 的
4. 堆： 所有 的 对象 实例 以及 数组 都 要在 堆 上 分配
5. 方法区用于存储 已被 虚拟机加载 的类信息、 常量、 静态变量、 即时 编译器 编译 后的代码等 数据。
	1. 运行时常量池（Runtime Constant Pool） 是 方法 区 的一部分。**Class文件中**除了 有 类 的 版本、 字段、 方法、 接口 等 描述 信息 外， 还有 一项 信息 是 常量 池（ Constant Pool Table）， 用于 存放 编译 期 生成 的 各种 字面 量 和 符号 引用， 这部 分 内容 将 在 类 加载 后进 入 方法 区 的 运行时 常量 池 中 存放。并非预置入Class文件 中 常量 池 的 内容 才能 进入 方法 区 运行时 常量 池， 运行 期间 也可 能将 新的 常量 放入 池 中， 这种 特性 被 开发 人员 利用 得比 较多 的 便是 String 类 的 intern() 方法。


### 对象在内存中存储的布局
1. 对象头（Header）
	1. 存储 对象 自身 的 运行时 数据， 如 哈 希 码（ HashCode）、 GC 分 代 年龄、 锁 状态 标志、 线程 持 有的 锁、 偏向 线程 ID、 偏向 时间 戳 等，
	2. 另外 一部分 是 类型 指针， 即对 象 指向 它的 类 元 数据 的 指针，
2. 实例数据（ Instance Data）:存储 顺序 会受 到 虚拟 机 分配 策略 参数（ FieldsAllocationStyle） 和 字段 在 Java 源 码 中 定义 顺序 的 影响。相同 宽度 的 字段 总是 被 分配 到一起。
3. 对齐填充（ Padding）。

### 对象 的 访问 定位
![image](http://img.blog.csdn.net/20160808125925120)
![image](http://img.blog.csdn.net/20160808125939374)


## 第 3 章 　 垃圾 收集 器 与 内存 分配 策略  

jvm区域总体分两类，heap区和非heap区。  
1. heap区又分：Eden Space（伊甸园）、Survivor Space(幸存者区)、Tenured Gen（老年代-养老区）。  
2. 非heap区又分：Code Cache(代码缓存区)、Perm Gen（永久代）、Jvm Stack(java虚拟机栈)、Local Method Statck(本地方法栈)。  
![image](http://img.blog.csdn.net/20130930164907500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXhjMTM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 对象 已死 吗
1. 引用 计数 算法
2. 可达 性 分析 算法,可 作为 GC Roots 的 对象
	1. 虚拟 机 栈（ 栈 帧 中的 本地 变 量表） 中 引用 的 对象。
	2. 方法 区 中 类 静态 属性 引用 的 对象。
	3. 方法 区 中常 量 引用 的 对象。
	4. 本地 方法 栈 中 JNI（ 即 一般 说的 Native 方法） 引用 的 对象。


### 垃圾 收集 算法
1. **标记-清除Mark-Sweep算法**:先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象，由于未经过整理，所以导致很多内存碎片  
![image](http://img.blog.csdn.net/20160321122324549?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  

2. **复制算法**.将可用的内存按容量划分为大小相等的两块（from，to），每次只是用其中一块,将还存活着的对象复制到另外一块上面，然后把已使用过的内存空间一次清理完。现在 的 商业 虚拟 机 都 采用 这种 收集 算法 来 回收 新生代，将 内存 分为 一块 较大 的 Eden 空间 和 两块 较小 的 Survivor 空间， 每次 使用 Eden 和 其中 一块 Survivor。当 回收 时， 将 Eden 和 Survivor 中 还 存 活着 的 对象 一次 性地 复制 到 另外 一块 Survivor 空间 上， 最后 清理 掉 Eden 和 刚才 用过 的 Survivor 空间。当 Survivor 空间 不 够用 时， 需要 依赖 其他 内存（ 这里 指 老 年代） 进行 分配 担保（ Handle Promotion）。   
![image](http://img.blog.csdn.net/20160321122217221?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 
![image](http://img2016.itdadao.com/d/file/tech/2016/11/16/cbb315765162200266.png)

3. **标记-整理（ Mark- Compact）算法** 标记 过程 仍然 与“ 标记- 清除” 算法 一样， 但 后续 步骤 不是 直接 对 可回收 对象 进行 清理， 而是 让 所有 存活 的 对象 都 向 一端 移动， 然后 直接 清理 掉 端 边界 以外 的 内存，
![image](http://img.blog.csdn.net/20160321122350910?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  
4. **分 代 收集（ Generational Collection） 算法**，是把 Java 堆 分为 新生代 和 老 年代，
	1. 新生代 中， 每次 垃圾 收集 时 都 发现 有大 批 对象 死去， 只有 少量 存活， 那就 选用 复制 算法，
	2. 老 年代 中 因为 对象 存活率 高、 没有 额外 空间 对 它 进行 分配 担保， 就必须 使用“ 标记— 清理” 或者“ 标记— 整理” 算法 来 进行 回收。

5. **HotSpot垃圾收集器**   
7 种 作用于 不同 分 代 的 收集 器， 如果 两个 收集 器 之间 存在 连线， 就说 明 它们 可以 搭配 使用。
 ![image](http://img.blog.csdn.net/20160403143402376)
	1. Serial收集器: 单 线程、简单 而 高效、是最 基本、 发展 历史 最 悠久 的 收集 器
	![image](http://img.blog.csdn.net/20160505192254430)
	2. ParNew收集器： 多 线程
	![image](http://img.blog.csdn.net/20160505192308164)
	3. Parallel Scavenge 收集 器：CMS 等 收集 器 的 关注 点 是 尽可能 地 缩短 垃圾 收集 时 用户 线程 的 停顿 时间， 而 Parallel Scavenge 收集 器 的 目标 则是 达到 一个 可 控制 的 吞吐量（ Throughput）。
	4. Serial Old 收集 器： Serial Old 是 Serial 收集 器 的 老年 代 版本， 它 同样是 一个 单线 程 收集 器， 使用“ 标记- 整理” 算法。
	5. Parallel Old 收集 器:Parallel Old 是 Parallel Scavenge 收集 器 的 老年 代 版本， 使用 多 线程 和“ 标记- 整理” 算法。  
	![image](http://img.blog.csdn.net/20160505192422531)
	6. CMS（ Concurrent Mark Sweep） 收集 器 是 一种 以 获取 最短 回收 停顿 时间 为 目标 的 收集 器。 目前 很大 一部分 的 Java 应用 集中 在 互 联网 站 或者 B/ S 系统 的 服务 端上，分四步：
		1. 初始 标记（ CMS initial mark） 
		2. 并发 标记（ CMS concurrent mark） 
		3. 重新 标记（ CMS remark） 
		4. 并发 清除（ CMS concurrent sweep）
	![image](http://img.blog.csdn.net/20160505193622716)  
	7. G1（Garbage-First）收集器,有以下特点：	
		1. 并行与并发
		2. 分代收集（仍然保留了分代的概念）
		3. 空间整合（整体上属于“标记-整理”算法，不会导致空间碎片）
		4. 可预测的停顿（比CMS更先进的地方在于能让使用者明确指定一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间不得超过N毫秒）  
	此外，G1收集器将Java堆划分为多个大小相等的Region（独立区域），新生代与老年代都是一部分Region的集合，G1的收集范围则是这一个个Region（化整为零）。
	![image](http://img.blog.csdn.net/20160505194916580)  

	初始标记阶段仅仅只是标记一下GC Roots能够直接关联的对象，并且修改TAMS（Next Top at Mark Start）的值，让下一阶段的用户程序并发运行的时候，能在正确可用的Region中创建对象，这个阶段需要暂停线程。并发标记阶段从GC Roots进行可达性分析，找出存活的对象，这个阶段食欲用户线程并发执行的。最终标记阶段则是修正在并发标记阶段因为用户程序的并发执行而导致标记产生变动的那一部分记录，这部分记录被保存在Remembered Set Logs中，最终标记阶段再把Logs中的记录合并到Remembered Set中，这个阶段是并行执行的，仍然需要暂停用户线程。最后在筛选阶段首先对各个Region的回收价值和成本进行排序，根据用户所期望的GC停顿时间制定回收计划。.
6. 内存 分配 与 回收 策略  
	基本概念
	1. -XX:+PrintGCDetails 收集 器 日志 参数， 告诉 虚拟 机 在 发生 垃圾 收集 行为 时 打印 内存 回收 日志， 并且 在 进程 退出 的 时候 输出 当前 的 内存 各区 域 分配 情况。
	2. 新生代 GC（ Minor GC）： 指 发生 在 新生代 的 垃圾 收集 动作，Minor GC 非常 频繁， 回收速度较快。
	3. 老 年代 GC（ Major GC/ Full GC）： 指 发生 在 老 年代 的 GC

	内存 分配  
	1. 对象 优先 在 Eden 分配: -verbose:gc -Xms20M -Xmx20M -Xmn10M -XX:+PrintGCDetails -XX:SurvivorRatio=8
	2. 大 对象 直接 进入 老 年代:所谓 的 大 对象 是指， 需要 大量 连续 内存 空间 的 Java 对象， 最 典型的 大 对象 就是 那种 很长 的 字符串 以及 数组.虚拟 机 提供 了 一个- XX: PretenureSizeThreshold 参数， 令 大于 这个 设置 值 的 对象 直接 在 老年 代 分配。
	3. 长期 存活 的 对象 将 进入 老 年代:为了 做到分 代， 虚拟 机 给 每个 对象 定义 了 一个 对象 年龄（ Age） 计数器。 如果 对象 在 Eden 出生 并经 过 第一次 Minor GC 后仍 然 存活， 并且 能被 Survivor 容纳 的 话， 将被 移动 到 Survivor 空间 中， 并且 对象 年龄 设为 1。 对象 在 Survivor 区 中 每“ 熬过” 一次 Minor GC， 年龄 就 增加 1 岁， 当它 的 年龄 增加 到 一定程度（ 默认 为 15 岁）， 就 将会 被 晋升 到 老 年代 中。 对象 晋升 老 年代 的 年龄 阈值， 可以 通过 参数- XX: MaxTenuringThreshold 设置。
	4. 动态 对象 年龄 判定:如果 在 Survivor 空间 中 相同 年龄 所有 对象 大小 的 总和 大于 Survivor 空间 的 一半， 年龄 大于 或 等于 该 年龄 的 对象 就可以 直接 进入 老 年代， 无须 等到 MaxTenuringThreshold 中 要求 的 年龄。
	5. 空间 分配 担保:担保 失败进行 一次 Full GC。
		
## 第 5 章 　 调 优 案例 分析 与 实战

除了 Java 堆 和 永久 代之 外， 我们 注意到 下面 这些 区域 还会 占用 较多 的 内存，


1. Direct Memory： 可通过- XX: MaxDirectMemorySize 调整 大小， 内存 不足 时 抛出 OutOfMemoryError 或者 OutOfMemoryError: Direct buffer memory。
2. 线程 堆栈： 可通过- Xss 调整 大小， 内存 不足 时 抛出 StackOverflowError（ 纵向 无法 分配， 即 无法 分配 新的 栈 帧） 或者 OutOfMemoryError: unable to create new native thread（ 横向 无法 分配， 即 无法 建立 新的 线程）。
3. Socket 缓存 区： 每个 Socket 连接 都 Receive 和 Send 两个 缓存 区， 分别 占 大约 37KB 和 25KB 内存， 连接 多的 话 这块 内存 占用 也比 较 可观。 如果 无法 分配， 则 可能 会 抛出 IOException: Too many open files 异常。
4. **JNI 代码： 如果 代码 中 使用 JNI 调用 本地 库， 那 本地 库 使用 的 内存 也 不在 堆 中。**
5. 虚拟 机 和 GC： 虚拟 机、 GC 的 代码 执行 也要 消耗 一定 的 内存。


## 第 6 章 　 类 文件 结构

### class 文件格式
![](http://7xqlat.com1.z0.glb.clouddn.com/Class%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F.png)

Class文件格式采用类似C语言结构体的伪结构来存储数据，这种伪结构只有两种数据类型：无符号数和表  

- 无符号数  
	- 属于基本的数据类型  
	- u1、u2、u4、u8来分别代表1、2、4、8个字节的无符号数  
	- 可以用来描述数字、索引引用、数量值或者按照UTF-8编码构成的字符串值  
	
- 表
	- 是由多个无符号数或者其他表作为数据项构成的复合数据类型
	- 以“_info”结尾
	- 整个class文件本质上就是一张表

### 常量池 
可以 理解 为 Class 文件 之中 的 资源 仓库，常量 池 中 主要 存放 两大 类 常量：  

- 字面 量（ Literal）:比 较 接 近于 Java 语言 层面 的 常量 概念， 如 文本 字符串、 声明 为 final 的 常 量值 等。
- 符号 引用（ Symbolic References）属于 编译 原理 方面 的 概念，当 虚拟 机 运行时， 需要 从 常量 池 获得 对应 的 符号 引用， 再 在 类 创建 时 或 运行时 解析、 翻译 到 具体 的 内存 地址 之中。 包括了 下面 三类 常量：
	- 类 和 接口 的 全 限定 名（ Fully Qualified Name） 
	- 字段 的 名称 和 描述 符（ Descriptor） 
	- 方法 的 名称 和 描述 符
![](http://7xqlat.com1.z0.glb.clouddn.com/%E5%B8%B8%E9%87%8F%E6%B1%A0%E4%B8%AD%E7%9A%8414%E4%B8%AD%E5%B8%B8%E9%87%8F%E9%A1%B9%E7%9A%84%E7%BB%93%E6%9E%84%E6%80%BB%E8%A1%A8.png)

使用命令可以查看const细节：  

	"/cygdrive/c/Program Files/Java/jdk1.8.0_45/bin/javap.exe" -verbose TestClass


Code属性
Java程序方法体中的代码经过Javac编译器处理后，最终变成字节码指令存储在Code属性中。
![](http://7xqlat.com1.z0.glb.clouddn.com/Code%E5%B1%9E%E6%80%A7%E8%A1%A8%E7%9A%84%E7%BB%93%E6%9E%84.png)