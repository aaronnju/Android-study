
Shallow size就是对象本身占用内存的大小，不包含对其他对象的引用

Retained size是该对象自己的shallow size，加上只能从该对象直接或间接访问到对象的shallow size之和。换句话说，retained size是该对象被GC之后所能回收到内存的总和。

来自 <http://blog.csdn.net/edisonlg/article/details/7082063> 
