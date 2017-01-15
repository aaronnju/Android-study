
[Java泛型：类型檫除、模板和泛型传递](http://m.blog.csdn.net/article/details?id=47978047)


泛型最主要的应用是在JDK 5中的新集合类框架中。泛型可以解决之前的集合类框架在使用过程中通常会出现的**运行时刻类型错误**，因为编译器可以在编译时刻就发现很多明显的错误。

**类型擦除:**类型擦除是Java的泛型实现方式与C++模板机制实现方式之间的重要区别。使用泛型的时候加上的类型参数，会被编译器在编译的时候去掉。如在代码中定义的List\<Object>和List\<String>等类型，在编译之后都会变成List。JVM看到的只是List

**规则：**
为了确保类型的安全性,很多泛型的奇怪特性都与这个类型擦除的存在有关，包括：

- 泛型类并没有自己独有的Class类对象。比如并不存在List\<String>.class或是List\<Integer>.class，而只有List.class。
- 静态变量是被泛型类的所有实例所共享的。对于声明为MyClass\<T>的类，访问其中的静态变量的方法仍然是 MyClass.myStaticVar。不管是通过new MyClass\<String>还是new MyClass\<Integer>创建的对象，都是共享一个静态变量。
- 泛型的类型参数不能用在Java异常处理的catch语句中。因为异常处理是由JVM在运行时刻来进行的。由于类型信息被擦除，JVM是无法区分两个异常类型MyException\<String>和MyException\<Integer>的。对于JVM来说，它们都是 MyException类型的。也就无法执行与异常对应的catch语句。


**类型擦除的基本过程**首先是找到用来替换类型参数的具体类。这个具体类一般是Object。如果指定了类型参数的上界的话，则使用这个上界。把代码中的类型参数都替换成具体的类。同时去掉出现的类型声明，即去掉<>的内容。比如T get()方法声明就变成了Object get()；List\<String>就变成了List

	public void inspect(List<Object> list) {    
	    for (Object obj : list) {        
	        System.out.println(obj);    
	    }    
	    list.add(1); //这个操作在当前方法的上下文是合法的。 
	}
	public void test() {    
	    List<String> strs = new ArrayList<String>();    
	    inspect(strs); //编译错误 
	}  
**通配符与上下界**

在使用泛型类的时候，既可以指定一个具体的类型，如List\<String>就声明了具体的类型是String；也可以用通配符?来表示未知类型，如List<?>就声明了List中包含的元素类型是未知的。 通配符所**代表一组类型**，但具体的类型是未知的。List<?>所声明的就是所有类型都是可以的。但是List<?>并不等同于List\<Object>。List\<Object>实际上确定了List中包含的是Object及其子类，在使用的时候都可以通过Object来进行引用。而List<?>则其中所包含的元素类型是不确定。其中可能包含的是String，也可能是 Integer。如果它包含了String的话，往里面添加Integer类型的元素就是错误的。正因为类型未知，就不能通过new ArrayList<?>()的方法来创建一个新的ArrayList对象。因为编译器无法知道具体的类型是什么。但是对于 List<?>中的元素确总是可以用Object来引用的，因为虽然类型未知，但肯定是Object及其子类。考虑下面的代码：

	public void wildcard(List<?> list) {
    	list.add(1);//编译错误 
	}  
如上所示，试图对一个带通配符的泛型类进行操作的时候，总是会出现编译错误。其原因在于通配符所表示的类型是未知的。

因为对于List<?>中的元素只能用Object来引用，在有些情况下不是很方便。在这些情况下，可以使用上下界来限制未知类型的范围。 如List<? extends Number>说明List中可能包含的元素类型是Number及其子类。而List<? super Number>则说明List中包含的是Number及其父类。当引入了上界之后，在使用类型的时候就可以使用上界类中定义的方法。比如访问 List<? extends Number>的时候，就可以使用Number类的intValue等方法。


**类型系统**

在Java中，大家比较熟悉的是通过继承机制而产生的类型体系结构。比如String继承自Object。根据Liskov替换原则，子类是可以替换父类的。当需要Object类的引用的时候，如果传入一个String对象是没有任何问题的。但是反过来的话，即用父类的引用替换子类引用的时候，就需要进行强制类型转换。编译器并不能保证运行时刻这种转换一定是合法的。这种自动的子类替换父类的类型转换机制，对于数组也是适用的。 String[]可以替换Object[]。但是泛型的引入，对于这个类型系统产生了一定的影响。正如前面提到的List\<String>是不能替换掉List\<Object>的。

引入泛型之后的类型系统增加了两个维度：

- 一个是类型参数自身的继承体系结构，对于 List\<String>和List\<Object>这样的情况，类型参数String是继承自Object的。
- 一个是泛型类或接口自身的继承体系结构。 List接口继承自Collection接口。

有如下的一些规则：

- 相同类型参数的泛型类的关系取决于泛型类自身的继承体系结构。即List\<String>是Collection\<String> 的子类型，List\<String>可以替换Collection\<String>。这种情况也适用于带有上下界的类型声明。
- 当泛型类的类型声明中使用了通配符的时候， 其子类型可以在两个维度上分别展开。如对Collection\<? extends Number>来说，其子类型可以在Collection这个维度上展开，即List\<? extends Number>和Set\<? extends Number>等；也可以在Number这个层次上展开，即Collection\<Double>和 Collection\<Integer>等。如此循环下去，ArrayList\<Long>和 HashSet\<Double>等也都算是Collection\<? extends Number>的子类型。
- 如果泛型类中包含多个类型参数，则对于每个类型参数分别应用上面的规则。

理解了上面的规则之后，就可以很容易的修正实例分析中给出的代码了。只需要把List<Object>改成List<?>即可。List<String>是List<?>的子类型，因此传递参数时不会发生错误。

	class ClassTest<X extends Number, Y, Z> {    
	    private X x;    
	    private static Y y; //编译错误，不能用在静态变量中    
	    public X getFirst() {
	        //正确用法        
	        return x;    
	    }    
	    public void wrong() {        
	        Z z = new Z(); //编译错误，不能创建对象    
	    }
	} 


**最佳实践**

在使用泛型的时候可以遵循一些基本的原则，从而避免一些常见的问题。

- 在代码中避免泛型类和原始类型的混用。比如List\<String>和List不应该共同使用。这样会产生一些编译器警告和潜在的运行时异常。当需要利用JDK 5之前开发的遗留代码，而不得不这么做时，也尽可能的隔离相关的代码。
- 在使用带通配符的泛型类的时候，需要明确通配符所代表的一组类型的概念。由于具体的类型是未知的，很多操作是不允许的。
- 泛型类最好不要同数组一块使用。你只能创建new List<?>[10]这样的数组，无法创建new List<String>[10]这样的。这限制了数组的使用能力，而且会带来很多费解的问题。因此，当需要类似数组的功能时候，使用集合类即可。
- 不要忽视编译器给出的警告信息。


**语法**

泛型方法定义:
public static <T extends Comparable<? super T>> void sort(List<T> list)
必须在返回值前边加一个<T>做为泛型标识

**泛型限定**

- ? extends E: 可以接收E类型或者E的子类型。上限。
- ? super E: 可以接收E类型或者E的父类型。 下限

		public class GenericDemo3 {  
		    public static void main(String[] args) {  
		        ArrayList<Personal = new ArrayList<Person>();  
		        al.add(new Person("tan11"));  
		        al.add(new Person("tan13"));  
		        al.add(new Person("tan21"));  
		        ArrayList<Studental1 = new ArrayList<Student>();  
		        al1.add(new Student("stu01"));  
		        al1.add(new Student("stu02"));  
		        al1.add(new Student("stu03"));  
	  
	        printColl(al);  
	        System.out.println();  
	        printColl2(al);  
	        printColl2(al1);  
	  
	    }  
	  
	    // 1、只能打印父类类型  
	    public static void printColl(ArrayList<Personal) {  
	        Iterator<Personit = al.iterator();  
	        while (it.hasNext()) {  
	            System.out.println(it.next().getName());  
	        }  
	    }  
	  
	    // 2.既可以打印父类也可以打印子类类型       《 上限》？ extends E: 可以接收E类型或者E的子类型  
	    public static void printColl2(ArrayList<? extends Personal) {  
	        Iterator<? extends Personit = al.iterator();  
	        while (it.hasNext()) {  
	            System.out.println(it.next().getName());  
	        }  
	    }  
		}
		class Person {  
	    private String name;  
	  
	    Person(String name) {  
	        this.name = name;  
	    }  
	  
	    public String getName() {  
	        return name;  
	    }  
		}  
		  
		class Student extends Person {  
		    Student(String name) {  
		        super(name);  
		    }  
		  
		}  