
![](http://3.bp.blogspot.com/-LUCDWSG5qXE/Uy_ee5bIR5I/AAAAAAAAAZA/oY1hR_1fcwk/s1600/Java+collection+cheat+sheet.PNG)

## Map
1. HashMap:基于hash值实现，类似于cpp的std::unordered_map。不排序。
2. TreeMap：基于红黑树。类似于cpp的std::map。排序了。实现了NavigableMap、SortedMap，可以有序的遍历。
3. LinkedHashMap： 是 HashMap 的一个子类，能够按照插入顺序或者访问顺序进行迭代。LinkedHashMap 维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序可以是插入顺序或者是访问顺序。
4. IdentityHashMap：简单说IdentityHashMap与常用的HashMap的区别是：前者比较key时是“引用相等”而后者是“对象相等”，即对于k1和k2，当k1==k2时，IdentityHashMap认为两个key相等，而HashMap只有在k1.equals(k2) == true 时才会认为两个key相等。IdentityHashMap有其特殊用途，比如序列化或者深度复制。或者记录对象代理。