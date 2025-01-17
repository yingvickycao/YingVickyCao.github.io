# Java 内存模型

## Java 程序执行过程   
- Java 程序由 JVM 执行。    
- Java内存区域划分，事实上是指JVM内存区域划分。  
- .class ：二进制字节码 ，可执行文件。

![Java 程序执行过程](https://yingvickycao.github.io/img/java/java_application_process.jpg)

![Java 程序执行过程](https://yingvickycao.github.io/img/java/java_application_process_2.png)

如上图所示，首先Java源代码文件(.java后缀)会被Java编译器编译为字节码文件(.class后缀)，然后由JVM中的类加载器加载各个类的字节码文件，加载完毕之后，交由JVM执行引擎执行。    
在整个程序执行过程中，JVM会用一段空间来存储程序执行期间需要用到的数据和相关信息，这段空间一般被称作为`Runtime Data Area（运行时数据区）`，也就是我们常说的JVM内存。      
因此，在Java中常常说到的内存管理就是针对这段空间进行管理（如何分配和回收内存空间）。         

总结：  
`Runtime Data Area（运行时数据区）` = JVM内存  -> Java 内存管理    

### 类加载  
![类加载](https://yingvickycao.github.io/img/java/class_load.png)

- 顺序：类先初始化 -> 对象
- 不是一次加载所有类，需要哪个类，再加载哪个类
- 初始化触发时类加载的第一个阶段---加载阶段肯定已经完成了。  类初始化的触发时机定会触发整个类加载过程。

#### 类初始化 VS 实例（对象）初始化
类初始化 - 类加载过程,`init`
实例初始化 - 创建实例时, `cliinit`

### 五种情况将触发类初始化    - `new,getstatic,putstatic,invokestatic`   

为了验证,先设置个基础类B，提供了一个静态字段，一个静态块。静态方法和静态块会在编译期汇集到`<clinit>`类初始化方法中，固可以用这个方法的运行结果引证类的初始化。   

## 线程与进程
- 前提：讨论的是单进程。多进程更复杂，不讨论。  

## 进程
- 1 个进程中有 N个线程。

## 线程
- Stack = 栈 = Java栈 = 虚拟机栈（Java Vitual Machine Stack）= 常常所说的栈。
- 每个线程有自己的Java栈。  
- 1 个线程对应一个 Java 栈。
- 由于每个线程正在执行的方法可能不同，因此每个线程都会有一个自己的Java栈，互不干扰。

![1](https://yingvickycao.github.io/img/java/thread_and_stack.png)

## Java栈
-  每个Java栈有 N 个Stack Frame。

## Stack Frame（栈帧)
- 每个Stack Frame对应一个被调用的函数。
-  Stack Frame 有四部分组成：
1. Local variable table(局部变量表): 用来存储方法中的局部变量（包括在方法中声明的非静态变量以及函数形参）  
- 对于基本数据类型的变量，则直接存储它的值
- 对于引用类型的变量，则存的是指向对象的引用。
- 局部变量表的大小在编译器就可以确定其大小了，因此在程序执行期间局部变量表的大小是不会改变的。=> 大小在编译期间确定，执行期间不变。  

2. Operand Stack (操作数栈)：程序中的所有计算过程都是在借助于操作数栈来完成的。  

3. Dynamic Linking (动态连接)/Reference to runtime constant pool(指向运行时常量池的引用)：
因为在方法执行的过程中有可能需要用到类中的常量，所以必须要有一个引用指向运行时常量。	
	
4. Return Address（返回地址)：		
当一个方法执行完毕之后，要返回之前调用它的地方，因此在栈帧中必须保存一个方法返回地址。	

5、附加信息

## Runtime Data Area(运行时数据区)  

### Program Counter Register(程序计数器)
- = PC寄存器  
- 用来指示执行哪条指令的。  
JVM中的程序计数器的功能跟汇编语言中的程序计数器的功能在逻辑上是等同的。  

- 程序计数器是每个线程所私有的。    
由于在JVM中，多线程是通过线程轮流切换来获得CPU执行时间的，因此，在任一具体时刻，一个CPU的内核只会执行一条线程中的指令。   
为了能够使得每个线程都在线程切换后能够恢复在切换之前的程序执行位置，每个线程都需要有自己独立的程序计数器，并且不能互相被干扰，否则就会影响到程序的正常执行次序。  

- 由于程序计数器中存储的数据所占空间的大小不会随程序的执行而发生改变，因此，对于程序计数器是不会发生内存溢出现象(OutOfMemory)的。

###  Stack
- Java栈是Java方法执行的内存模型。  
- 当线程执行一个方法时，就会随之创建一个对应的栈帧，并将建立的栈帧压栈。当方法执行完毕之后，便会将栈帧出栈。
- 线程当前执行的方法所对应的栈帧必定位于Java栈的顶部。  
- 在Java中，程序员基本不用关系到内存分配和释放的事情，因为Java有自己的垃圾回收机制.这部分空间的分配和释放都是由系统自动实施的。对于所有的程序设计语言来说，栈这部分空间对程序员来说是不透明的。

### Native Method Stack(本地方法栈)
本地方法栈与Java栈的作用和原理非常相似。区别只不过是Java栈是为执行Java方法服务的，而本地方法栈则是为执行本地方法（Native Method）服务的。   
在JVM规范中，并没有对本地方发展的具体实现方法以及数据结构作强制规定，虚拟机可以自由实现它。在HotSopt虚拟机中直接就把本地方法栈和Java栈合二为一。

## Heap(堆)
C语言中，堆是唯一一个程序员可以管理的内存区域， 通过malloc函数和free函数在堆上申请和释放空间。   
Java 语言中，在Java中，程序员基本不用去关心空间释放的问题。Java的垃圾回收机制会自动进行处理。因此这部分空间也是Java垃圾收集器管理的主要区域。  

- Java中的堆是用来存储对象本身的以及数组。  
对象的引用是存放在Java栈中的。  
数组引用是存放在Java栈中的。  
- 堆是被所有线程共享的
- 在JVM中只有一个堆。

### 方法区  
- 它与堆一样，是被线程共享的区域。
- 在方法区中，存储了每个类的信息（包括类的名称、方法信息、字段信息）、`静态变量`、`常量`以及编译器编译后的代码等。
- 在Class文件中除了类的字段、方法、接口等描述信息外，还有一项信息是`常量池`，用来存储编译期间生成的`字面量`和`符号引用`。
- 在方法区中有一个非常重要的部分就是运行时常量池，它是每一个类或接口的常量池的运行时表示形式，在类和接口被加载到JVM后，对应的运行时常量池就被创建出来。  
并非Class文件常量池中的内容才能进入运行时常量池。  
在运行期间也可将新的常量放入运行时常量池中，比如String的intern方法。    
- 在JVM规范中，没有强制要求方法区必须实现垃圾回收。很多人习惯将方法区称为“永久代”，是因为HotSpot虚拟机以永久代来实现方法区，从而JVM的垃圾收集器可以像管理堆区一样管理这部分区域，从而不需要专门为这部分设计垃圾回收机制。不过自从JDK7之后，Hotspot虚拟机便将运行时常量池从永久代移除了。

### 常量池  
- 常量池，有两种形态：静态常量池和运行时常量池  
- 必须要关注编译期的行为，才能更好的理解常量池。
- 运行时常量池中的常量，基本来源于各个class文件中的常量池。
- 程序运行时，除非手动向常量池中添加常量(比如调用intern方法)，否则jvm不会自动添加常量到常量池。
- 常量池：字符串常量池，整型常量池、浮点型常量池等等，但都大同小异。
- 数值类型的常量池不可以手动添加常量，程序启动时常量池中的常量就已经确定了，比如整型常量池中的常量范围：-128~127，只有这个范围的数字可以用到常量池。

1. 静态常量池 （static Constant pool）    
- 即*.class文件中的常量池，class文件中的常量池不仅仅包含字符串(数字)字面量，还包含类、方法的信息，占用class文件绝大部分空间。  
- 用来存储编译期间生成的`字面量`和`符号引用`。
- 编译期间生成的。
- 常量池什么？放置常量的的池，只是这个池是数组。
- **cp_info结构体分为两类：字面量和符号引用**  

2. 运行时常量池
- jvm虚拟机在完成类装载操作后，将class文件中的常量池载入到内存中，并保存在方法区中。  
- 经常说的常量池，就是指方法区中的运行时常量池。     
- 运行期间生成的。
- 值是唯一的。
- 在方法区中，每个类型都对应一个常量池，存放该类型所用到的所有常量.
- 常量池中存储了诸如文字字符串、final变量值、类名和方法名常量  => 有各种常量池组成。 final 变量常量池，方法名常量池，类名常量池等
- 数组  
它们以数组形式通过索引被访问，是外部调用与类联系及类型对象化的桥梁。（存的可能是个普通的字符串，然后经过常量池解析，则变成指向某个类的引用）  

## 静态变量
- 类变量，类的所有实例都共享
- 在方法区有个静态区，静态区专门存放静态变量和静态块。

## 符号引用（Symbolic References）VS 直接引用  

### 符号引用：
- 指的是Class文件文件中的符号引用， 编译期   
- `javac :编译`  
- `javap -c :反编译`   
- `javap -v :反编译， 观察常量池`  

```
javac RuntimeDataAreaDemo.java
javap -c RuntimeDataAreaDemo.class > RuntimeDataAreaDemo.txt
javap -v RuntimeDataAreaDemo.class > RuntimeDataAreaDemo2.txt
```
- 符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定位到目标即可。
- 各种虚拟机实现的内存布局可能有所不同，但是它们能接受的符号引用都是一致的，因为符号引用的字面量形式明确定义在Java虚拟机规范的Class文件格式中。

###  直接引用
- 直接引用是和虚拟机的布局相关的，运行期。
- 同一个符号引用在不同的虚拟机实例上翻译出来的直接引用一般不会相同。如果有了直接引用，那引用的目标必定已经被加载入内存中了。  

## RuntimeDataAreaDemo.java -> test()  
- https://github.com/YingVickyCao/AndroidAboutDemos/blob/master/doc/java_memory.xlsx

说明过程：
1）编译期间，方法区中的静态常量池（.class文件中常量池）存储字面值和符号引用
2）类装载后，拷贝静态常量池到运行时常量池  -> `4`
即把10 和 “hello“，从静态常量池拷贝一份到运行时常量池。

3) a 的值是10，原始类型。     
首先，检查a的值是否位于-128~127。 => 位于。  
然后，检查 10是否在运行时常量池。如果在，则直接入栈并赋给 a。如果不在，则先在运行时常量池写入，然后再入栈并赋给 a。=> 在运行时常量池。    

4) s1 与 a 的不同点： 
s1的值是一个对象，指向运行时常量池的地址。      
a的值是原始类型，从运行时常量池拷贝值到栈。   

5）s3 与 s1的不同： 
1. 首先在Heap中new一块内存0x2005。 
2. 检查“Helo“是否在运行时常量池。如果在，则直接入栈，出栈，传给构造方法。如果不在，则先在运行时常量池写入，然后再入栈，出栈，传给构造方法。    
=> 在运行时常量池，入栈，出栈，传给构造方法（指向了运行时常量池）。     
3. 执行string 构造方法。
4. 将Heap中newed的内存关联到 s3。 

## 总结： 
- 基本类型的变量数据 放在栈里面。
- 对象的引用 放在栈里面的。对象本身放在堆里面。
- 显式的String常量放在常量池，String对象放在堆中。

## References:
- JVM的内存区域划分 http://www.cnblogs.com/dolphin0520/p/3613043.html
- https://www.cnblogs.com/xlyslr/p/5751039.html

- Java常量池的大概理解  https://blog.csdn.net/qq_27093465/article/details/52033327
- 深入讲解java中.class文件中的常量池 https://blog.csdn.net/w3045872817/article/details/73251068
- Class文件中的常量池详解（上）https://blog.csdn.net/wangtaomtk/article/details/52267548
- Java线程：线程栈模型与线程的变量 http://blog.51cto.com/lavasoft/99152
- java -- JVM的符号引用和直接引用 https://www.cnblogs.com/shinubi/articles/6116993.html
- 探秘Java中String、StringBuilder以及StringBuffer https://www.cnblogs.com/dolphin0520/p/3778589.html
- https://blog.csdn.net/xiaoshengfdyh/article/details/46126367?locationNum=5&fps=1

- java反汇编及JVM指令集 https://blog.csdn.net/sum_rain/article/details/39892219
- https://www.cnblogs.com/luyanliang/p/5498584.html
- https://segmentfault.com/a/1190000003871183
- https://segmentfault.com/q/1010000008683475/a-1020000008688535
- http://tieba.baidu.com/p/2035063260  
- http://kingj.iteye.com/blog/1451008
- https://blog.csdn.net/chunyuan314/article/details/72857258
- https://blog.csdn.net/sum_rain/article/details/39892219

- 观察常量池：如何查看javap -v 类名
- 指令 istore_1 , ldc