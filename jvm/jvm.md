
## [static初始化](http://m.jb51.net/article/86629.htm)
Java静态变量初始化遵循以下规则: 

- 静态变量会按照声明的顺序先依次声明并设置为该类型的默认值，但不赋值为初始化的值。
- 声明完毕后,再按声明的顺序依次设置为初始化的值，如果没有初始化的值就跳过。
- 只有主动请求一个类,这个类才会初始化,仅包含静态变量,函数,等静态的东西.
- 继承关系时,先初始化父类,后初始化子类.
- 初始化A的时候，如果需要初始化B，就暂停A，当交叉初始化，一个类C处于暂停初始化状态时，就直接跳过C，直接取当前值继续执行当前初始化
- final,静态常量在编译时,编译器会将不可变的常量值在使用的地方替换掉。其他的按照上面规则执行

## \<init>与\<clinit>的区别
- **clinit：**是虚拟机在装载一个**类初始化**的时候调用的, 所有的类变量初始化语句和类型的静态初始化语句都被Java编译器收集到了一起，放在一个特殊的方法中。
> class Example {  
>        static int size = 3 * (int) (Math.random() * 5.0);  
> }  

.
> class Example{  
>      static int size;  
>      static {  // 静态初始化语句
>            size = 3 * (int) (Math.random() * 5.0);  
>      }  
> } 

- **init**是在类实例化时调用的,实例化一个类有四种途径：
	- 调用new操作符；
	- 调用Class或java.lang.reflect.Constructor对象的newInstance()方法；
	- 调用任何现有对象的clone()方法；
	- 通过java.io.ObjectInputStream类的getObject()方法反序列化。


![image](http://www.uml.org.cn/mobiledev/images/20150521417.png)
<http://www.uml.org.cn/mobiledev/201505224.asp>