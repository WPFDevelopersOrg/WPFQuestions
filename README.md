# WPF 面试题参考 **基础**


## 目录

1）[值类型与引用类型](#值类型与引用类型)   

2）[装箱与拆箱](#装箱与拆箱)   

3）[string与字符串操作](#string与字符串操作)   

4）[类与接口](#类与接口)   

5）[常量、字段、属性、特性与委托](#常量、字段、属性、特性与委托)   

6）[GC与内存管理](#GC与内存管理)   

7）[多线程编程与线程同步](#多线程编程与线程同步)   

8）[线程同步](#线程同步)   

9）[WPF](#WPF)   

--------------------------------

## <a id="值类型与引用类型">值类型与引用类型</a>

### **1.值类型和引用类型的区别？**

值类型包括简单类型、结构体类型和枚举类型，引用类型包括自定义类、数组、接口、委托等。

1、赋值方式：将一个值类型变量赋给另一个值类型变量时，将复制包含的值。这与引用类型变量的赋值不同，引用类型变量的赋值只复制对象的引用（即内存地址，类似`C++`中的指针），而不复制对象本身。

2、继承：值类型不可能派生出新的类型，所有的值类型均隐式派生自 `System.ValueType`。但与引用类型相同的是，结构也可以实现接口。

3、`null`：与引用类型不同，值类型不可能包含 `null` 值。然而，可空类型功能允许将 `null` 赋给值类型。

4、每种值类型均有一个隐式的默认构造函数来初始化该类型的默认值，值类型初始会默认为0，引用类型默认为`null`。

5、值类型存储在栈中，引用类型存储在托管堆中。

### **2. 结构和类的区别？**

结构体是值类型，类是引用类型，主要区别如题1。
其他的区别：
- 结构不支持无惨构造函数，不支持析构函数，并且不能有`protected`修饰；
- 结构常用于数据存储，类`class`多用于行为；
- `class`需要用`new`关键字实例化对象，`struct`可以不适用`new`关键字；
- `class`可以为抽象类，`struct`不支持抽象；

### **3. delegate是引用类型还是值类型？enum、int[]和string呢？**

enum枚举是值类型，其他都是引用类型。

### **4. 堆和栈的区别？**

线程堆栈：
- 简称栈 Stack托管堆： 简称堆 Heap值类型大多分配在栈上，引用类型都分配在堆上；
- 栈由操作系统管理，栈上的变量在其作用域完成后就被释放，效率较高，但空间有限。堆受CLR的GC控制；
- 栈是基于线程的，每个线程都有自己的线程栈，初始大小为1M。堆是基于进程的，一个进程分配一个堆，堆的大小由GC根据运行情况动态控制；

### **5.“结构”对象可能分配在堆上吗？什么情况下会发生，有什么需要注意的吗？**

结构是值类型，有两种情况会分配在对上面：结构作为class的一个字段或属性，会随class一起分配在堆上面；装箱后会在堆中存储，尽量避免值类型的装箱，值类型的拆箱和装箱都有性能损失，下一篇会重点关注；

### **6. 理解参数按值传递？以及按引用传递？**

按值传递：对于值类型传递的它的值拷贝副本，而引用类型传递的是引用变量的内存地址，他们还是指向的同一个对象。按引用传递：通过关键字out和ref传递参数的内存地址，值类型和引用类型的效果是相同的。

### **7. out 和 ref的区别与相同点？**

out 和 ref都指示编译器传递参数地址，在行为上是相同的；他们的使用机制稍有不同，ref要求参数在使用之前要显式初始化，out要在方法内部初始化；out 和 ref不可以重载，就是不能定义Method(ref int a)和Method(out int a)这样的重载，从编译角度看，二者的实质是相同的，只是使用时有区别；

### **8. 有几种方法可以判定值类型和引用类型？**

简单来说，继承自System.ValueType的是值类型，反之是引用类型。

### **9. C#支持哪几个预定义的值类型？C#支持哪些预定义的引用类型？**

值类型：整数、浮点数、字符、bool和decimal引用类型：Object，String

### **10. 说说值类型和引用类型的生命周期？**

值类型在作用域结束后释放。引用类型由GC垃圾回收期回收。这个答案可能太简单了，更详细的答案在后面的文章会说到。

### **11. 如果结构体中定义引用类型，对象在内存中是如何存储的？例如下面结构体中的class类 User对象是存储在栈上，还是堆上？**

```C#
public struct MyStruct 
{ 
　　public int Index; 
　　public User User; 
}
```



MyStruct存储在栈中，其字段User的实例存储在堆中，MyStruct.User字段存储指向User对象的内存地址。

 
## <a id="装箱与拆箱">装箱与拆箱</a>


### **1.什么是拆箱和装箱？**

装箱就是值类型转换为引用类型，拆箱就是引用类型（被装箱的对象）转换为值类型。

### **2.什么是箱子？**

就是引用类型对象。

### **3.箱子放在哪里？**

托管堆上。

### **4.装箱和拆箱有什么性能影响？**

装箱和拆箱都涉及到内存的分配和对象的创建，有较大的性能影响。

### **5.如何避免隐身装箱？**

编码中，多使用泛型、显示装箱。

### **6.箱子的基本结构？**

上面说了，箱子就是一个引用类型对象，因此它的结构，主要包含两部分：值类型字段值；引用类型的标准配置，引用对象的额外空间：TypeHandle和同步索引块，关于这两个概念在本系列后面的文章会深入探讨。

### **7.装箱的过程？**

1.在堆中申请内存，内存大小为值类型的大小，再加上额外固定空间（引用类型的标配：TypeHandle和同步索引块）；2.将值类型的字段值（x=1023）拷贝新分配的内存中；3.返回新引用对象的地址（给引用变量object o）

### **8.拆箱的过程？**

1.检查实例对象（object o）是否有效，如是否为null，其装箱的类型与拆箱的类型（int）是否一致，如检测不合法，抛出异常；2.指针返回，就是获取装箱对象（object o）中值类型字段值的地址；3.字段拷贝，把装箱对象（object o）中值类型字段值拷贝到栈上，意思就是创建一个新的值类型变量来存储拆箱后的值；

 
## <a id="string与字符串操作">string与字符串操作</a>


### **1.字符串是引用类型类型还是值类型？**

引用类型。

### **2.在字符串连加处理中，最好采用什么方式，理由是什么？**

少量字符串连接，使用String.Concat，大量字符串使用StringBuilder，因为StringBuilder的性能更好，如果string的话会创建大量字符串对象。

### **3.使用 StringBuilder时，需要注意些什么问题？**

少量字符串时，尽量不要用，StringBuilder本身是有一定性能开销的；大量字符串连接使用StringBuilder时，应该设置一个合适的容量；

 

## <a id="类与接口">类与接口</a>

### **1. 所有类型都继承System.Object吗？**

基本上是的，所有值类型和引用类型都继承自System.Object，接口是一个特殊的类型，不继承自System.Object。

### **2. 解释virtual、sealed、override和abstract的区别**

- virtual申明虚方法的关键字，说明该方法可以被重写

- sealed说明该类不可被继承

- override重写基类的方法

- abstract申明抽象类和抽象方法的关键字，抽象方法不提供实现，由子类实现，抽象类不可实例化。

### **3. 接口和类有什么异同？**

不同点：

1、接口不能直接实例化。

2、接口只包含方法或属性的声明，不包含方法的实现。

3、接口可以多继承，类只能单继承。

4、类有分部类的概念，定义可在不同的源文件之间进行拆分，而接口没有。（这个地方确实不对，接口也可以分部，谢谢@xclin163的指正）

5、表达的含义不同，接口主要定义一种规范，统一调用方法，也就是规范类，约束类，类是方法功能的实现和集合

相同点：

1、接口、类和结构都可以从多个接口继承。

2、接口类似于抽象基类：继承接口的任何非抽象类型都必须实现接口的所有成员。

3、接口和类都可以包含事件、索引器、方法和属性。

### **4. 抽象类和接口有什么区别？**

1、继承：接口支持多继承；抽象类不能实现多继承。

2、表达的概念：接口用于规范，更强调契约，抽象类用于共性，强调父子。抽象类是一类事物的高度聚合，那么对于继承抽象类的子类来说，对于抽象类来说，属于"Is A"的关系；而接口是定义行为规范，强调“Can Do”的关系，因此对于实现接口的子类来说，相对于接口来说，是"行为需要按照接口来完成"。

3、方法实现：对抽象类中的方法，即可以给出实现部分，也可以不给出；而接口的方法（抽象规则）都不能给出实现部分，接口中方法不能加修饰符。

4、子类重写：继承类对于两者所涉及方法的实现是不同的。继承类对于抽象类所定义的抽象方法，可以不用重写，也就是说，可以延用抽象类的方法；而对于接口类所定义的方法或者属性来说，在继承类中必须重写，给出相应的方法和属性实现。

5、新增方法的影响：在抽象类中，新增一个方法的话，继承类中可以不用作任何处理；而对于接口来说，则需要修改继承类，提供新定义的方法。

6、接口可以作用于值类型（枚举可以实现接口）和引用类型；抽象类只能作用于引用类型。

7、接口不能包含字段和已实现的方法，接口只包含方法、属性、索引器、事件的签名；抽象类可以定义字段、属性、包含有实现的方法。

### **5. 重载与覆盖的区别？**

重载：当类包含两个名称相同但签名不同(方法名相同,参数列表不相同)的方法时发生方法重载。用方法重载来提供在语义上完成相同而功能不同的方法。

覆写：在类的继承中使用，通过覆写子类方法可以改变父类虚方法的实现。

主要区别：

1、方法的覆盖是子类和父类之间的关系，是垂直关系；方法的重载是同一个类中方法之间的关系，是水平关系。 2、覆盖只能由一个方法，或只能由一对方法产生关系；方法的重载是多个方法之间的关系。 3、覆盖要求参数列表相同；重载要求参数列表不同。 4、覆盖关系中，调用那个方法体，是根据对象的类型来决定；重载关系，是根据调用时的实参表与形参表来选择方法体的。

### **6. 在继承中new和override相同点和区别？**

看下面的代码，有一个基类A，B1和B2都继承自A，并且使用不同的方式改变了父类方法Print（）的行为。测试代码输出什么？为什么？

```c#
        static void Main(string[] args)
        {
            B1 b1 = new B1(); B2 b2 = new B2();
            b1.Print(); b2.Print();      //输出 B1、B2

            A ab1 = new B1(); A ab2 = new B2();
            ab1.Print(); ab2.Print();   //输出 B1、A
            Console.ReadLine();
        }

        public class A
        {
            public virtual void Print() { Console.WriteLine("A"); }
        }
        public class B1 : A
        {
            public override void Print() { Console.WriteLine("B1"); }
        }
        public class B2 : A
        {
            public new void Print() { Console.WriteLine("B2"); }
        }
```

​       



 

### **7.class中定义的静态字段是存储在内存中的哪个地方？为什么会说它不会被GC回收？**

随类型对象存储在内存的加载堆上，因为加载堆不受GC管理，其生命周期随AppDomain，不会被GC回收。

 

## <a id="常量、字段、属性、特性与委托">常量、字段、属性、特性与委托</a>

### **1. const和readonly有什么区别？**

const关键字用来声明编译时常量，readonly用来声明运行时常量。都可以标识一个常量，主要有以下区别： 1、初始化位置不同。const必须在声明的同时赋值；readonly即可以在声明处赋值，也可以在构造方法里赋值。 2、修饰对象不同。const即可以修饰类的字段，也可以修饰局部变量；readonly只能修饰类的字段 。 3、const是编译时常量，在编译时确定该值，且值在编译时被内联到代码中；readonly是运行时常量，在运行时确定该值。 4、const默认是静态的；而readonly如果设置成静态需要显示声明 。 5、支持的类型时不同，const只能修饰基元类型或值为null的其他引用类型；readonly可以是任何类型。

```C#
 public class Program
    {
        public readonly int PORT;
        static void Main(string[] args)
        {
            B a = new B();
            Console.WriteLine(a.PORT);
            Console.WriteLine(B.PORT2);
            Console.WriteLine(a.PORT3.ccc);
            Console.ReadLine();
        }
    }

    class B
    {
        //readonly即可以在声明处赋值，也可以在构造方法里赋值
        public readonly int PORT;
        //const必须在声明的同时赋值
        public const int PORT2 = 10;

        //错误public const A PORT3 =  new A()    const只能修饰基元类型或值为null的其他引用类型

        //readonly可以是任何类型
        public readonly A PORT3 = new A();
        public B()
        {

            PORT = 11;
        }
    }
    class A
    {
        public string ccc = "aaaa";
    }

 
```



### **2. 字段与属性有什么异同？**

- 属性提供了更为强大的，灵活的功能来操作字段

- 出于面向对象的封装性，字段一般不设计为Public

- 属性允许在set和get中编写代码

- 属性允许控制set和get的可访问性，从而提供只读或者可读写的功能 （逻辑上只写是没有意义的）

- 属性可以使用override 和 new

### **3. 静态成员和非静态成员的区别？**

- 静态变量使用 static 修饰符进行声明，静态成员在加类的时候就被加载（上一篇中提到过，静态字段是随类型对象存放在Load Heap上的），通过类进行访问。

- 不带有static 修饰符声明的变量称做非静态变量，在对象被实例化时创建，通过对象进行访问 。

- 一个类的所有实例的同一静态变量都是同一个值，同一个类的不同实例的同一非静态变量可以是不同的值 。

- 静态函数的实现里不能使用非静态成员，如非静态变量、非静态函数等。

### **4. 特性是什么？如何使用？**

特性与属性是完全不相同的两个概念，只是在名称上比较相近。Attribute特性就是关联了一个目标对象的一段配置信息，本质上是一个类，其为目标元素提供关联附加信息，这段附加信息存储在dll内的元数据，它本身没什么意义。运行期以反射的方式来获取附加信息。

### **5. C#中的委托是什么？事件是不是一种委托？**

什么是委托？简单来说，委托类似于 C或 C++中的函数指针，允许将方法作为参数进行传递。

- C#中的委托都继承自System.Delegate类型；

- 委托类型的声明与方法签名类似，有返回值和参数；

- 委托是一种可以封装命名（或匿名）方法的引用类型，把方法当做指针传递，但委托是面向对象、类型安全的；

事件可以理解为一种特殊的委托，事件内部是基于委托来实现的。

 

## <a id="GC与内存管理">GC与内存管理</a>

### **1. 简述一下一个引用对象的生命周期？**

- new创建对象并分配内存

- 对象初始化

- 对象操作、使用

- 资源清理（非托管资源）

- GC垃圾回收

### **2. GC进行垃圾回收时的主要流程是？**

① 标记：先假设所有对象都是垃圾，根据应用程序根Root遍历堆上的每一个引用对象，生成可达对象图，对于还在使用的对象（可达对象）进行标记（其实就是在对象同步索引块中开启一个标示位）。

② 清除：针对所有不可达对象进行清除操作，针对普通对象直接回收内存，而对于实现了终结器的对象（实现了析构函数的对象）需要单独回收处理。清除之后，内存就会变得不连续了，就是步骤3的工作了。

③ 压缩：把剩下的对象转移到一个连续的内存，因为这些对象地址变了，还需要把那些Root跟指针的地址修改为移动后的新地址。

### **6. GC在哪些情况下回进行回收工作？**

- 内存不足溢出时（0代对象充满时）

- Windwos报告内存不足时，CLR会强制执行垃圾回收

- CLR卸载AppDomian，GC回收所有

- 调用GC.Collect

- 其他情况，如主机拒绝分配内存，物理内存不足，超出短期存活代的存段门限

**7. using() 语法是如何确保对象资源被释放的？如果内部出现异常依然会释放资源吗？**3

using() 只是一种语法形式，其本质还是try…finally的结构，可以保证Dispose始终会被执行。

### **8. 解释一下C#里的析构函数？为什么有些编程建议里不推荐使用析构函数呢？**

C#里的析构函数其实就是终结器Finalize，因为长得像C++里的析构函数而已。

有些编程建议里不推荐使用析构函数要原因在于：第一是Finalize本身性能并不好；其次很多人搞不清楚Finalize的原理，可能会滥用，导致内存泄露，因此就干脆别用了

### **9. Finalize() 和 Dispose() 之间的区别？**

Finalize() 和 Dispose()都是.NET中提供释放非托管资源的方式，他们的主要区别在于执行者和执行时间不同：

- finalize由垃圾回收器调用；dispose由对象调用。

- finalize无需担心因为没有调用finalize而使非托管资源得不到释放，而dispose必须手动调用。

- finalize不能保证立即释放非托管资源，Finalizer被执行的时间是在对象不再被引用后的某个不确定的时间；而dispose一调用便释放非托管资源。

- 只有class类型才能重写finalize，而结构不能；类和结构都能实现IDispose。

另外一个重点区别就是终结器会导致对象复活一次，也就说会被GC回收两次才最终完成回收工作，这也是有些人不建议开发人员使用终结器的主要原因。

### **10. Dispose和Finalize方法在何时被调用？**

- Dispose一调用便释放非托管资源；

- Finalize不能保证立即释放非托管资源，Finalizer被执行的时间是在对象不再被引用后的某个不确定的时间；

### **11. .NET中的托管堆中是否可能出现内存泄露的现象?**

是的，可能会。比如：

- 不正确的使用静态字段，导致大量数据无法被GC释放；

- 没有正确执行Dispose()，非托管资源没有得到释放；

- 不正确的使用终结器Finalize()，导致无法正常释放资源；

- 其他不正确的引用，导致大量托管对象无法被GC释放；

### **12. 在托管堆上创建新对象有哪几种常见方式？**

- new一个对象；

- 字符串赋值，如string s1=”abc”；

- 值类型装箱；

## <a id="多线程编程与线程同步">多线程编程与线程同步</a>

### **1. 描述线程与进程的区别？**

- 一个应用程序实例是一个进程，一个进程内包含一个或多个线程，线程是进程的一部分；

- 进程之间是相互独立的，他们有各自的私有内存空间和资源，进程内的线程可以共享其所属进程的所有资源；

### **2. lock为什么要锁定一个参数，可不可锁定一个值类型？这个参数有什么要求？**

lock的锁对象要求为一个引用类型。它可以锁定值类型，但值类型会被装箱，每次装箱后的对象都不一样，会导致锁定无效。

对于lock锁，锁定的这个对象参数才是关键，这个参数的同步索引块指针会指向一个真正的锁（同步块），这个锁（同步块）会被复用。

### **3. 多线程和异步有什么关系和区别？**

多线程是实现异步的主要方式之一，异步并不等同于多线程。实现异步的方式还有很多，比如利用硬件的特性、使用进程或纤程等。在.NET中就有很多的异步编程支持，比如很多地方都有Begin**、End**的方法，就是一种异步编程支持，它内部有些是利用多线程，有些是利用硬件的特性来实现的异步编程。

### **4. 线程池的优点有哪些？又有哪些不足？**

优点：减小线程创建和销毁的开销，可以复用线程；也从而减少了线程上下文切换的性能损失；在GC回收时，较少的线程更有利于GC的回收效率。

缺点：线程池无法对一个线程有更多的精确的控制，如了解其运行状态等；不能设置线程的优先级；加入到线程池的任务（方法）不能有返回值；对于需要长期运行的任务就不适合线程池。

### **5. Mutex和lock有何不同？一般用哪一个作为锁使用更好？**

Mutex是一个基于内核模式的互斥锁，支持锁的递归调用，而Lock是一个混合锁，一般建议使用Lock更好，因为lock的性能更好。

### **6.Thread与Task的区别**

Thread 类主要用于实现线程的创建以及执行。

Task 类表示以异步方式执行的单个操作。

#### 1、Task 是基于 Thread 的，是比较高层级的封装，Task 最终还是需要 Thread 来执行

#### 2、Task 默认使用后台线程执行，Thread 默认使用前台线程

```C#
static void Main(string[] args)
{
    Thread thread = new Thread(obj => { Thread.Sleep(3000); });
    thread.Start();
}
```



上面代码，主程序在3秒后结束。

```C#
static void Main(string[] args)
{
    Task<int> task = new Task<int>(() => 
    {
        Thread,Sleep(3000);
        return 1;
    });
    task.Start();
}

```



而这段代码，会瞬间结束。

#### 3、Task 可以有返回值，Thread 没有返回值

虽然 Thread 可以通过 Start 方法参数来进行返回值处理，但十分不便。

```C#
public void Start (object parameter);
static void Main(string[] args)
{  
    Task task = new Task(LongRunningTask);
    task.Start();
    Console.WriteLine(task.Result);
}
private static int LongRunningTask()
{
    Thread.Sleep(3000);
    return 1;
}

```



#### 4、Task 可以执行后续操作，Thread 不能执行后续操作

```C#
static void Main(string[] args)
{
    Task task = new Task(LongRunningTask);
    task.Start();
    Task childTask = task.ContinueWith(SquareOfNumber);
    Console.WriteLine("Sqaure of number is :"+ childTask.Result);
    Console.WriteLine("The number is :" + task.Result);
}
private static int LongRunningTask()
{
    Thread.Sleep(3000);
    return 2;
}
private static int SquareOfNumber(Task obj)
{
    return obj.Result * obj.Result;
}

```



#### 5、Task 可取消任务执行，Thread 不行

```C#
static void Main(string[] args)
{
    using (var cts = new CancellationTokenSource())
    {
        Task task = new Task(() => { LongRunningTask(cts.Token); });
        task.Start();
        Console.WriteLine("Operation Performing...");
        if(Console.ReadKey().Key == ConsoleKey.C)
        {
            Console.WriteLine("Cancelling..");
            cts.Cancel();
        }                
        Console.Read();
    }
}
private static void LongRunningTask(CancellationToken token)
{
    for (int i = 0; i < 10000000; i++)
    {
        if(token.IsCancellationRequested)
        {
            break;
        }
        else
        {                  
            Console.WriteLine(i);
        }               
    }          
}

```



#### 6、异常传播

Thread 在父方法上获取不到异常，而 Task 可以。

## **线程间互相通信**

C#线程间互相通信主要用到两个类：AutoResetEvent和ManualResetEvent.

### **一、AutoResetEvent**

　　AutoResetEvent 允许线程通过发信号互相通信，线程通过调用 AutoResetEvent 上的 WaitOne 来等待信号。 如果 AutoResetEvent 为非终止状态，则线程会被阻止，并等待当前控制资源的线程通过调用 Set 来通知资源可用。

吃快餐的时候都会排队付款，收银员发送收款通知，客户依次付钱

AutoResetEvent类一次只能通知一个等待的线程，且通知一次过后会立即将AutoResetEvent对象的状态置为false,也就是如果有两个客户都在等待收银员通知，AutoResetEvent对象的set方法只能通知到第一个客户

### **二、ManualResetEvent**

　　在AutoResetEvent中，如果要通知两个线程，则Set方法要被执行两次，也以快餐店的例子做了举例，但如果有一天客户1中彩票了，要请部门的10个同事吃饭，也就是说只要Set一次，所有在等待的线程都会解除等待，相当于收银员只收一次钱，10个人都可以通过收银去吃饭，这时我们就要用到ManualResetEvent类，它的用法和AutoResetEvent基本一样，区别就在于它是一量Set方法发出通知后，要再次阻塞的话就需要手动去修改，也就是调用Reset方法

### **总结**

　　AutoResetEvent和ManualResetEvent的主要区别就在于：AutoResetEvent一次只能通知一个等待线程，通知后自动关闭； 而ManualResetEvent一次可通知很多个等待的线程，但要关闭需要调用Reset方法手动关闭

## <a id="线程同步">线程同步</a>

### **线程同步的几种方法：**

### **1、阻塞**

当线程调用Sleep，Join，EndInvoke，线程就处于阻塞状态（Sleep使调用线程阻塞，Join、EndInvoke使另外一个线程阻塞），会立即从cpu退出。（阻塞状态的线程不消耗cpu）

### **2、加锁（lock）**

加锁使多个线程同一时间只有一个线程可以调用该方法，其他线程被阻塞。

#### **同步对象的选择：**

- 使用

**引用类型**，值类型加锁时会装箱，产生一个新的对象。

- 使用

**private**修饰，使用public时易产生死锁。**（使用lock（this），lock(typeof(实例))时，该类也应该是private）**。

- string不能作为锁对象。

- 不能在lock中使用

await关键字

#### **锁是否必须是静态类型？**

如果被锁定的方法是静态的，那么这个锁必须是静态类型。这样就是在全局锁定了该方法，不管该类有多少个实例，都要排队执行。

如果被锁定的方法不是静态的，那么不能使用静态类型的锁，因为被锁定的方法是属于实例的，只要该实例调用锁定方法不产生损坏就可以，不同实例间是不需要锁的。这个锁只锁该实例的方法，而不是锁所有实例的方法.*

```C#
class ThreadSafe
{
 private static object _locker = new object();
 
  void Go()
  {
    lock (_locker)
    {
      ......//共享数据的操作 （Static Method）,使用静态锁确保所有实例排队执行
    }
  }
​
private object _locker2=new object();
  void GoTo()
  {
    lock(_locker2)
    //共享数据的操作，非静态方法，是用非静态锁，确保同一个实例的方法调用者排队执行
  }
}
```



#### **同步对象可以兼作它lock的对象**

如：

```C#
class ThreadSafe
{
 private List <string> _list = new List <string>(); 
  void Test()
  {
    lock (_list)
    {
      _list.Add ("Item 1");
    }
  }
}
```



### **3、Monitors**

lock其实是Monitors的简洁写法。

```C#
lock (x)  
{  
    DoSomething();  
}  
```



两者其实是一样的。

```C#
System.Object obj = (System.Object)x;  
System.Threading.Monitor.Enter(obj);  
try  
{  
    DoSomething();  
}  
finally  
{  
    System.Threading.Monitor.Exit(obj);  
} 
```



### **4、互斥锁（Mutex）**

互斥锁是一个互斥的同步对象，同一时间有且仅有一个线程可以获取它。可以实现进程级别上线程的同步。

```C#
class Program
    {
      //实例化一个互斥锁
        public static Mutex mutex = new Mutex();

        static void Main(string[] args)
        {
            for (int i = 0; i < 3; i++)
            {
              //在不同的线程中调用受互斥锁保护的方法
                Thread test = new Thread(MutexMethod);
                test.Start();
            }
            Console.Read();
        }

        public static void MutexMethod()
        {
           Console.WriteLine("{0} 请求获取互斥锁", Thread.CurrentThread.Name);
           mut.WaitOne();
           Console.WriteLine("{0} 已获取到互斥锁", Thread.CurrentThread.Name);     
           Thread.Sleep(1000);
           Console.WriteLine("{0} 准备释放互斥锁", Thread.CurrentThread.Name);
            // 释放互斥锁
           mut.ReleaseMutex();
           Console.WriteLine("{0} 已经释放互斥锁", Thread.CurrentThread.Name);
        }
    }
```



#### **互斥锁可以在不同的进程间实现线程同步**

使用互斥锁实现一个一次只能启动一个应用程序的功能。

```c#
    public static class SingleInstance
    {
        private static Mutex m;
​
        public static bool IsSingleInstance()
        {
            //是否需要创建一个应用
            Boolean isCreateNew = false;
            try
            {
               m = new Mutex(initiallyOwned: true, name: "SingleInstanceMutex", createdNew: out isCreateNew);
            }
            catch (Exception ex)
            {
               
            }
            return isCreateNew;
        }
    }
```



#### 互斥锁的带有三个参数的构造函数

1. initiallyOwned: 如果initiallyOwned为true，互斥锁的初始状态就是被所实例化的线程所获取，否则实例化的线程处于未获取状态。

1. name:该互斥锁的名字，在操作系统中只有一个命名为name的互斥锁mutex，如果一个线程得到这个name的互斥锁，其他线程就无法得到这个互斥锁了，必须等待那个线程对这个线程释放。

1. createNew:如果指定名称的互斥体已经存在就返回false，否则返回true。

### **5、信号和句柄**

lock和mutex可以实现线程同步，确保一次只有一个线程执行。但是线程间的通信就不能实现。如果线程需要相互通信的话就要使用AutoResetEvent,ManualResetEvent，通过信号来相互通信。它们都有两个状态，终止状态和非终止状态。只有处于非终止状态时，线程才可以阻塞。

#### **AutoResetEvent****：**

AutoResetEvent 构造函数可以传入一个bool类型的参数，false表示将AutoResetEvent对象的初始状态设置为非终止。如果为true标识终止状态，那么WaitOne方法就不会再阻塞线程了。但是因为该类会自动的将终止状态修改为非终止，所以，之后再调用WaitOne方法就会被阻塞。

WaitOne 方法如果AutoResetEvent对象状态非终止，则阻塞调用该方法的线程。可以指定时间，若没有获取到信号，返回false

set 方法释放被阻塞的线程。但是一次只可以释放一个被阻塞的线程。

```C#
class ThreadSafe 
{  
    static AutoResetEvent autoEvent;  

    static void Main()  
    {  
        //使AutoResetEvent处于非终止状态
        autoEvent = new AutoResetEvent(false);  

        Console.WriteLine("主线程运行...");  
        Thread t = new Thread(DoWork);  
        t.Start();  

        Console.WriteLine("主线程sleep 1秒...");  
        Thread.Sleep(1000);  

        Console.WriteLine("主线程释放信号...");  
        autoEvent.Set();  
    }  

     static void DoWork()  
    {  
        Console.WriteLine("  t线程运行DoWork方法，阻塞自己等待main线程信号...");  
        autoEvent.WaitOne();  
        Console.WriteLine("  t线程DoWork方法获取到main线程信号，继续执行...");  
    }  

}  

//输出
//主线程运行...
//主线程sleep 1秒...
//  t线程运行DoWork方法，阻塞自己等待main线程信号...
//主线程释放信号...
//  t线程DoWork方法获取到main线程信号，继续执行...
```



#### **ManualResetEvent**

ManualResetEvent和AutoResetEvent用法类似。

AutoResetEvent在调用了Set方法后，会自动的将信号由释放（终止）改为阻塞（非终止），一次只有一个线程会得到释放信号。而ManualResetEvent在调用Set方法后不会自动的将信号由释放（终止）改为阻塞（非终止），而是一直保持释放信号，使得一次有多个被阻塞线程运行，只能手动的调用Reset方法，将信号由释放（终止）改为阻塞（非终止）,之后的再调用Wait.One方法的线程才会被再次阻塞。

```C#
public class ThreadSafe
{
    //创建一个处于非终止状态的ManualResetEvent
    private static ManualResetEvent mre = new ManualResetEvent(false);

    static void Main()
    {
        for(int i = 0; i <= 2; i++)
        {
            Thread t = new Thread(ThreadProc);
            t.Name = "Thread_" + i;
            t.Start();
        }

        Thread.Sleep(500);
        Console.WriteLine("\n新线程的方法已经启动，且被阻塞，调用Set释放阻塞线程");

        mre.Set();

        Thread.Sleep(500);
        Console.WriteLine("\n当ManualResetEvent处于终止状态时，调用由Wait.One方法的多线程，不会被阻塞。");

        for(int i = 3; i <= 4; i++)
        {
            Thread t = new Thread(ThreadProc);
            t.Name = "Thread_" + i;
            t.Start();
        }

        Thread.Sleep(500);
        Console.WriteLine("\n调用Reset方法，ManualResetEvent处于非阻塞状态，此时调用Wait.One方法的线程再次被阻塞");
  

        mre.Reset();

        Thread t5 = new Thread(ThreadProc);
        t5.Name = "Thread_5";
        t5.Start();

        Thread.Sleep(500);
        Console.WriteLine("\n调用Set方法，释放阻塞线程");

        mre.Set();
    }


    private static void ThreadProc()
    {
        string name = Thread.CurrentThread.Name;

        Console.WriteLine(name + " 运行并调用WaitOne()");

        mre.WaitOne();

        Console.WriteLine(name + " 结束");
    }
}


//Thread_2 运行并调用WaitOne()
//Thread_1 运行并调用WaitOne()
//Thread_0 运行并调用WaitOne()

//新线程的方法已经启动，且被阻塞，调用Set释放阻塞线程

//Thread_2 结束
//Thread_1 结束
//Thread_0 结束

//当ManualResetEvent处于终止状态时，调用由Wait.One方法的多线程，不会被阻塞。

//Thread_3 运行并调用WaitOne()
//Thread_4 运行并调用WaitOne()

//Thread_4 结束
//Thread_3 结束

///调用Reset方法，ManualResetEvent处于非阻塞状态，此时调用Wait.One方法的线程再次被阻塞

//Thread_5 运行并调用WaitOne()
//调用Set方法，释放阻塞线程
//Thread_5 结束
```



### **6、Interlocked**

如果一个变量被多个线程修改，读取。可以用Interlocked。

计算机上不能保证对一个数据的增删是原子性的，因为对数据的操作也是分步骤的：

1. 将实例变量中的值加载到寄存器中。

1. 增加或减少该值。

1. 在实例变量中存储该值。

Interlocked为多线程共享的变量提供原子操作。Interlocked提供了需要原子操作的方法：

- public static int Add (ref int location1, int value); 两个参数相加，且把结果和赋值该第一个参数。

- public static int Increment (ref int location); 自增。

- public static int CompareExchange (ref int location1, int value, int comparand);

location1 和comparand比较，被value替换. value 如果第一个参数和第三个参数相等，那么就把value赋值给第一个参数。 comparand 和第一个参数对比。

------

### **7、ReaderWriterLock**

如果要确保一个资源或数据在被访问之前是最新的。那么就可以使用ReaderWriterLock.该锁确保在对资源获取赋值或更新时，只有它自己可以访问这些资源，其他线程都不可以访问。即排它锁。但用改锁读取这些数据时，不能实现排它锁。

lock允许同一时间只有一个线程执行。而ReaderWriterLock允许同一时间有多个线程可以执行**读操作**，或者只有一个有排它锁的线程执行**写操作**。

```C#
    class Program
    {
        // 创建一个对象
        public static ReaderWriterLock readerwritelock = new ReaderWriterLock();
        static void Main(string[] args)
        {
            //创建一个线程读取数据
            Thread t1 = new Thread(Write);
           // t1.Start(1);
            Thread t2 = new Thread(Write);
            //t2.Start(2);
            // 创建10个线程读取数据
            for (int i = 3; i < 6; i++)
            {
                Thread t = new Thread(Read);
              //  t.Start(i);
            }

            Console.Read();

        }

        // 写入方法
        public static void Write(object i)
        {
            // 获取写入锁，20毫秒超时。
            Console.WriteLine("线程：" + i + "准备写...");
            readerwritelock.AcquireWriterLock(Timeout.Infinite);
            Console.WriteLine("线程：" + i + " 写操作" + DateTime.Now);
            // 释放写入锁
            Console.WriteLine("线程：" + i + "写结束...");
            Thread.Sleep(1000);
            readerwritelock.ReleaseWriterLock();

        }

        // 读取方法
        public static void Read(object i)
        {
            Console.WriteLine("线程：" + i + "准备读...");

            // 获取读取锁，20毫秒超时
            readerwritelock.AcquireReaderLock(Timeout.Infinite);
            Console.WriteLine("线程：" + i + " 读操作" + DateTime.Now);
            // 释放读取锁
            Console.WriteLine("线程：" + i + "读结束...");
            Thread.Sleep(1000);

            readerwritelock.ReleaseReaderLock();

        }
    }
//分别屏蔽writer和reader方法。可以更清晰的看到 writer被阻塞了。而reader没有被阻塞。

//屏蔽reader方法
//线程：1准备写...
//线程：1 写操作2017/7/5 17:50:01
//线程：1写结束...
//线程：2准备写...
//线程：2 写操作2017/7/5 17:50:02
//线程：2写结束...

//屏蔽writer方法
//线程：3准备读...
//线程：5准备读...
//线程：4准备读...
//线程：5 读操作2017/7/5 17:50:54
//线程：5读结束...
//线程：3 读操作2017/7/5 17:50:54
//线程：3读结束...
//线程：4 读操作2017/7/5 17:50:54
//线程：4读结束...
```



## <a id="WPF">WPF</a>

### **1.WPF由哪两部分组成？**

wpf 由两个主要部分 组成：引擎和编程框架。

1 引擎。wpf引擎是基于窗体的应用程序 图形 视频 音频和文档提供了一个单一的运行时库。重要的是WPF基于矢量的呈现引擎使应用程序可以灵活地利用高DPI监视器，支持图形的硬件加速。

2 框架。WPF框架为媒体 用户界面设计和文档提供大量的解决方案。wpf的设计考虑了可扩展性，使开发人员可以完全在WPF引擎的基础上创建自己的控件，也可以通过对现有WPF控件进行再分类来创建自己的控件。

### **2.什么是WPF？**

WPF英文全称是Windows Presentation Foundation，中文为Windows表现层基础。是微软最初在.NET Framework3.5平台推出的一个图形平台。

### **3. Silverlight 和 WPF的异同？**

它们的运行方式是不同的：

#### 1 silverlight是基于浏览器插件的，在浏览器中运行。

#### 2 WPF可以编写Web程序或者桌面应用程序，可以直接编译为独立运行的exe文件。

实现功能是不同的：

#### 1 WPF支持直接在XAML中绑定触发来触发动画，而silverlight就只能通过托管代码或者javascript来进行。

#### 2 WPF直接支持3D效果和3D镜头变换，但是silverlight就不支持。

它们也有相似的地方：

silverlight 原名WPF/E 就是WPF Everywhere 是WPF的一个子集。因此它们在很多语法实现上非常类似。

**4.如何理解WPF体系结构？**

WPF使用多层架构，类似于三层结构，最顶层部分为托管代码API，此层用于为开发人员编写WPF应用程序提供较高层次的服务，基于C#托管代码编写。转换.NET代码到DirectX的工作由中间层milcore.dll实现。

中间层milcore是用非托管代码实现，因为它需要与DirectX紧密集成，对性能敏感，就是消耗的资源比较多，对性能影响较大。

### **5. 在WPF中Binding的作用及实现语法？**

典型的Binding具有四个重要组成部分：Binding目标对象（binding target object） 目标对象属性（target property） Binding数据源（binding source） Path(用于指明要从数据源中取得的值，就是我们通常写的属性名称）。

### **6.什么是XML扩展XAML?有何优点？**

1 XAML是eXtensible Application Markup Language的英文缩写，相应于中文称为可扩展应用程序标记语言，它是微软公司为构建应用程序用户界面而创建的一种新的描述性语言。

2 XAML是XML语言的一个衍生物，它的语法与XML语言完全一致，它的功能就是专门用来设计和实现程序的UI。

3 XAML一个很大的优点就是由于WPF支持WEB开发，那么WEB开发和桌面开发的转换是很简单，修改的地方很短，同时UI与逻辑完全分离，所以逻辑代码也几乎不用改动。

### **7. 解释什么是依赖属性，它和以前的属性有什么不同？为什么在WPF会使用它？**

1 Windows Presentation Foundation (WPF) 提供了一组服务，这些服务可用于扩展公共语言运行时 (CLR) 属性的功能，这些服务通常统称为 WPF 属性系统。由 WPF 属性系统支持的属性称为依赖项属性。

2 它和以往属性的不同之处有

(1)依赖属性是一种特定类型的属性。这种属性的特殊之处在于，其属性值受到 Windows 运行时中专用属性系统的跟踪和影响。

(2)依赖属性的用途是提供一种系统的方式，用来基于其他输入（在应用运行时其内部出现的其他属性、事件和状态）计算属性的值。

(3)依赖属性代表或支持编程模型的某种特定功能，用于定义 Windows 运行时应用，这种模型使用 XAML 编写 UI，使用 C#、Microsoft Visual Basic 或 Visual C++ 组件扩展 (C++/CX) 编写代码。

一般的属性没有这么复杂。

3 WPF使用它是有不少优点的

（1）优化了属性的存储，直接减少了不必要的内存使用。

（2）有属性变化通知 限制 验证等。

（3）可以储存多个值，配合Expression及Animation等，打造出更灵活的使用方法。

### **8. WPF中什么是样式？**

首先明白WPF中样式属于资源中重要的一种。

同时样式也是属性值的集合，能被应用到一个合适的元素中，或者说能将一组属性应用到多个元素。

WPF中样式可以设置任何依赖属性。

WPF中样式也支持触发器，通过属性的改变，触发一组活动，包括改变某个控件的样式。

WPF中元素只能使用一个样式。

样式有继承的特性，样式可以继承样式。

### **9. WPF中什么是模板 ？**

WPF中模板是用于定义或重定义控件结构，或者说对象的外观。

WPF中模板有两类，一个是控件模板（ControlTemplate) 另一个是数据模板（DataTemplate），它们都派生自FrameworkTemplate抽象类。

总共有三大模板 ControlTemplate，ItemsPanelTemplate，DataTemplate。

1 ControlTemplate 主要用途是更改控件的外观。它有两个重要属性：VisualTree（视觉树）内容属性和Triggers触发器，对于触发器可以不用过多考虑，触发器可有可无。VisualTree就是呈现我们所画的控件。Triggers可以对我们的视觉树上的元素进行一些变化。

2 ItemsPanelTemplate 是个特殊的空间模板，主要用来标明多条目控件如何显示它所包含的多项数据。也可以说是指定用于项的额布局的面板。多用于多个内容控件的目标。多为Panel属性或者Panel结尾的属性。

3 DataTemplate 主要用于数据的呈现。也被称为显示绑定数据对象的模板。

[WPF](https://so.csdn.net/so/search?q=WPF&spm=1001.2101.3001.7020)中有控件模板和数据模板，控件模板可以让我们自定义控件的外观，而数据模板定义了数据的显示方式，也就是数据对象的可视结构，但是这里有一个问题需要考虑，数据是如何显示出来的？虽然数据模板定义了数据的可视结构，但是我们清楚的知道，只有控件才是可视的，数据一般是被控件承载，这里需要另外的一个对象ContentPresenter。

### **10. 绑定（Binding ）的基础用法**

WPF 里分三种：Binding,PriorityBinding,MultiBinding,这三种Binding的基类都是BindingBase，而BindingBase又继承于MarkupExtension。

常见的使用Binding方法是：

1 针对于继承于FrameworkElement控件。 SetBinding(DependencyProperty dp,String path),SetBinding(DependencyProperty dp,BindingBase binding),其中FrameworkElement中SetBinding只对DependencyProperty有效。

2 另一种是 BindingOperations.SetBinding(currentFolder,TextBlock.TextProperty,binding);

BindingOperations.SetBinding的原型是

public static BindingExpressionBase SetBinding(DependencyObject target,DependencyProperty dp,BindingBase binding)

3 清除Binding:

BindingOperations.ClearBinding(currentFolder,TextBlock.TextProperty);//删除currentFolder上的TextBlock.TextProperty绑定

BindingOperations.ClearAllBindings(currentFolder);//删除currentFolder上的所有绑定。

直接对Dependency Property赋值也可以解除binding，不过只对单向binding有效。

### **11. 解释这几个类的作用及关系: Visual, UIElement, FrameworkElement, Control 。**

它们四个的关系：从System.Windows.Controls命名空间中看，依次的继承关系是：

Visual继承UIElement,UIElement继承FrameworkElement,FrameworkElement继承Control。

1 Visual主要作用是为WPF提供2D呈现支持，主要包括输出显示，坐标转换，区域剪切等。

2 UIElement的主要作用是构建WPF元素和基本呈现特征的基类。例如其中定义很多与输入和焦点有关的特性，例如键盘事件，鼠标，还有一些与WPF事件模型有关的API。

3 FrameworkElement的主要作用是为定义的WPF元素添加一些功能。例如，布局定义 逻辑树 对象生命周期事件 支持数据绑定和动态资源引用 支持样式和动画。

4 Control的主要作用是为自定义应用程序控件提供基础。因为它是创建自定义应用程序控件的基类,作用就是可以重写Control类所提供的属性，方法，事件等，为自定义控件添加自定义逻辑。构建WPF应用程序页面的Window类也派生自它。

### **12. 视觉树 VS 逻辑树?**

1 逻辑树是视觉树的子集，也就是视觉树基本上是逻辑树的一种扩展。

2 WPF通过逻辑树来解决依赖项属性继承和资源的问题，使用视觉树来处理渲染，事件路由，资源定位等问题。

3 逻辑树可以认为是XAML所见的，而视觉树包含了XAML元素内部的结构。

4 逻辑树的查找可以通过LogicalTreeHelper辅助类，视觉树的查找可以通过VisualTreeHelper辅助类，其中需要注意的是对ContentElement元素的查找，无法直接通过VisualTreeHelper进行查找，ContentElement元素并不继承Visual，而ContentElement元素的使用时需要一个ContentElement载体FrameworkContentElement。

六 属性变更通知(INotifyPropertyChanged 和 ObservableCollection)

1 INotifyPropertyChanged向客户端发出某一属性值更改的通知。

2 ObservableCollection类，它是实现 INotifyCollectionChanged 接口的数据集合的内置实现。表示一个动态数据集合，在添加项、移除项或刷新整个列表时，此集合将提供通知

### **13. 解释一下ResourceDictionary ？**

提供包含元素和 WPF 应用程序的其他元素使用的 WPF 资源的一个哈希表/字典实现。

有利于项目中资源共享。

### **14. 路由事件的哪三种方式/策略（冒泡 直接 隧道）？**

WPF中的路由事件是沿着VisualTree传递的，作用是用来调用应用程序的元素树上的各种监听器上的处理程序。

（1）冒泡，这种事件处理方式是从源元素向上级流过去，直到到达根节点即顶层节点，一般为最外层的控件。

（2）直接，这种处理方式是在源上处理，主要用在源元素上处理。通常setter和trigger中有所体现，我个人认为VisualState可视状态可能也是直接事件处理，也是依赖属性的状态改变。和Trigger有一定的重复，但是VisualState是通过生硬的动画间接实现依赖属性的改变。

（3）隧道，又称作Preview事件，和冒泡事件处理方式相反的。元素树的根位置调用事件处理程序，依次向下直到源元素位置。

隧道事件和冒泡事件一般成对出现。同一事件，执行时首先是隧道事件，然后是冒泡事件。

### **15. 解释Routed Events(路由事件) 与 Commands(命令)？**

Event 和 Command 是程序内部通信基础，Routed Events 能够发起多重控件，并且能有序和用户沟通。

Commands是.NET Framework 提供的核心构架，来激活和去除高级别任务。

由此衍生的Animation是events的更进一步。让你能够以友好互动的方式使用Event架构，来使用多重控件。

### **16.模板**

WPF模板有三种：

ControlTemplate、

DataTemplate、

ItemsPanelTemplate，

它们都继承自FrameworkTemplate抽象类。在这个抽象类中有一个FrameworkElementFactory类型的VisualTree变量，通过该变量可以设置或者获取模板的根节点，包含了你想要的外观元素树。

（1） 控件模板。控件模板可以将自定义模板应用到某一特定类型的所有控件，或是控件的某一实例。决定控件外观的是ControlTemplate，它决定了控件“长成什么样子”，因此控件模板由ControlTemplate类表示。控件模板实际在资源集合或资源字典中定义。例子详见：[通过设计ControlTemplate，制作圆角文本框与圆角按钮](https://www.cnblogs.com/zhouhb/p/3284780.html)（http://www.cnblogs.com/zhouhb/p/3284780.html）。（2） 数据模板。在WPF中，决定数据外观的是DataTemplate，即DataTemplate是数据内容的表现形式，一条数据显示成什么样子，是简单的文本还是直观的图形，就是由DataTemplate决定的。例子详见：[DataTemplate应用](https://www.cnblogs.com/zhouhb/p/3284827.html)（http://www.cnblogs.com/zhouhb/p/3284827.html）。

（3）ItemsPanelTemplate模板。ItemsPanelTemplate 指定用于项的布局的面板。如下面的例子通过设置ListBox的ItemsPanelTemplate，使得每一项从左至右按顺序显示。

#### **1、ControlTemplate**

ControlTemplate:控件模板主要有两个重要属性：VisualTree内容属性和Triggers触发器。所谓VisualTree(视觉树),就是呈现我们所画的控件。Triggers可以对我们的视觉树上的元素进行一些变化。一般用于单内容控件。

#### **2、ItemsPanelTemplate**

ItemsPanelTemplate在MSDN的解释是：ItemsPanelTemplate 指定用于项的布局的面板。 [GroupStyle](http://msdn.microsoft.com/zh-cn/library/system.windows.controls.groupstyle.aspx) 具有一个类型为 ItemsPanelTemplate 的 [Panel](http://msdn.microsoft.com/zh-cn/library/system.windows.controls.groupstyle.panel.aspx) 属性。 [ItemsControl](http://msdn.microsoft.com/zh-cn/library/system.windows.controls.itemscontrol.aspx) 类型具有一个类型为ItemsPanelTemplate 的 [ItemsPanel](http://msdn.microsoft.com/zh-cn/library/system.windows.controls.itemscontrol.itemspanel.aspx) 属性。

我们先讲ItemTemplate。它一般用在多个内容控件的模板。比如ListBox。

#### **3、DataTemplate和HierarchicalDataTemplate**

DataTemplate就是显示绑定数据对象的模板。

WPF中的数据模板(DataTemplate) 在WPF中我们可以为自己的数据定制显示方式，也就是说虽然某数据数据是一定 的，但我们可以做到让它的表现方式多种多样



HierarchicalDataTemplate继承于DataTemplate，它专门对TreeViewItem 或 MenuItem的一些数据对象的绑定。

### **17.触发器**

WPF中的触发器，是对WPF事件的一种封装，WPF中的触发器，有多种方式，总结起来可分为：

- 控件触发（FrameworkElement.Triggers集合）
- 样式触发（Styles.Triggers集合）
- 控件模板触发（ControlTemplate.Triggers集合）
- 数据模板触发（DataTemplate.Triggers集合）

触发器种类

System.Windows.DataTrigger 数据触发器

System.Windows.EventTrigger 事件触发器

System.Windows.MultiDataTrigger 多数据触发器

System.Windows.MultiTrigger 多属性触发器

System.Windows. Trigger 属性触发器

#### **1、属性触发器 Trigger**

顾名思义，属性触发器是由属性的改变而引起的，所以属性触发器有几个关键属性： 

Property：获取或设置返回的值进行比较的属性 [Value](https://msdn.microsoft.com/zh-cn/library/system.windows.trigger.value.aspx) 触发器的属性。 比较是引用相等性检查。（只有依赖属性才有用）

Value：获取或设置要与该元素的属性值进行比较的值。 比较是引用相等性检查

Setter：获取一套 [Setter](https://msdn.microsoft.com/zh-cn/library/system.windows.setter.aspx) 对象，描述要应用当满足指定的条件的属性值。

Setter： 属性是一个SetterBaseCollection 的集合。

#### **Setter类：**

Property：获取或设置的属性与其 Value 将应用。**（只有依赖属性才有用）**

Value：获取或设置要应用于指定此属性的值 Setter。

以下代码，指定了属性触发器的使用，由于属性触发一般应用与样式定义中。 

​    ![0](https://note.youdao.com/yws/res/d/WEBRESOURCEa1826240762bf6d88f0d0963734953cd)

``` xaml
<Button Content="触发器" x:Name="btn1" Click="Button_Click" >
<Button.Style>
<Style>
<Setter Property="Button.Width" Value="80"></Setter>
<Setter Property="Button.Height" Value="100"></Setter>
<Style.Triggers>
<Trigger Property="Button.IsMouseOver" Value="true">
<Trigger.Setters>
<Setter Property="Button.Width" Value="200"></Setter>
</Trigger.Setters>
</Trigger>
</Style.Triggers>
</Style>
</Button.Style>
</Button> 
```



#### **2、多属性触发器MultiTrigger**

多属性触发器，是之一个或者多个样式发生变化时候，才会触发的触发器，其有一个重要属性Condition，用于统计条件，MultiTrigger.Conditions是一个Condition的集合，做一个简单案例，就能说明：

```xaml
 <Style>
      <Setter Property="Button.Width" Value="80"></Setter>
      <Setter Property="Button.Height" Value="100"></Setter>
      <Style.Triggers>
              <MultiTrigger>
               <MultiTrigger.Conditions>
                        <Condition Property="Control.IsFocused" Value="true"></Condition>
                        <Condition Property="Control.IsMouseOver" Value="true"></Condition>
                   </MultiTrigger.Conditions>
               <Setter Property="Button.Width" Value="300"></Setter>
              </MultiTrigger>
       </Style.Triggers>
 </Style>
```


#### **3、事件触发器EventTrigger**

事件触发器，是由控件的操作触发的信息，EventTrigger 属性里面，几个关键属性是：

RoutedEvent：获取或设置将激活该触发器的 RoutedEvent。

Actions：获取事件发生时要应用的操作的集合。

主要用于指定路由事件和Actions,那么Actions是什么呢？其定义如下：

```C#
public TriggerActionCollection Actions { get; } 
```



它是一个TriggerAction的集合

**System.Windows.Interactivity.TriggerAction**

​     [Microsoft.Expression.Interactivity.Core.CallMethodAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.core.callmethodaction(v=expression.40).aspx)

​     [Microsoft.Expression.Interactivity.Media.PlaySoundAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.media.playsoundaction(v=expression.40).aspx)

​     [Microsoft.Expression.Interactivity.Media.StoryboardAction](https://msdn.microsoft.com/zh-cn/library/microsoft.expression.interactivity.media.storyboardaction(v=expression.40).aspx)

​     [System.Windows.Interactivity.InvokeCommandAction](https://msdn.microsoft.com/zh-cn/library/system.windows.interactivity.invokecommandaction(v=expression.40).aspx)

所以事件触发器，只能调用CallMethodAction，PlaySoundAction，StoryboardAction，InvokeCommandAction，所以事件触发器一般应用与动画中，我们以StoryboardAction为例，我们知道StoryboardAction是Storyboard的一个封装，其主要是封装了动作BeginStoryboard，该动作相当于调用BeginAnimation方法，我们案例如下： 

```xaml
   <Button Content="触发器"   x:Name="btn1"    Click="Button_Click" >
            <Button.Style>
                <Style>
                    <Setter Property="Button.Width" Value="80"></Setter>
                    <Setter Property="Button.Height" Value="80"></Setter>
                    <Style.Triggers>
                        <Trigger Property="Button.IsMouseOver" Value="true">
                            <Trigger.Setters>
                                <Setter Property="Button.Width" Value="300"></Setter>
                            </Trigger.Setters>
                        </Trigger>
                    </Style.Triggers>
                </Style>
            </Button.Style>
        </Button>
```



#### **4、数据触发DataTrigger**

数据触发器，是使用在数据改变的触发，DataTrigger也有两个重要的属性

Binding：获取或设置产生数据对象的属性值的绑定。

Value：获取或设置要与此数据对象的属性值进行比较的值。

案例代码如下：

```xaml
<Style>
     <Style.Triggers>
        <DataTrigger Binding="{Binding RelativeSource={RelativeSource Self},Path=Text}" Value="8">
            <Setter Property="Button.Foreground" Value="Sienna"></Setter>
            <Setter Property="Button.FontWeight" Value="Bold"></Setter>
            <Setter Property="Button.FontSize" Value="30"></Setter>
         </DataTrigger>
     </Style.Triggers>
</Style>
```

#### 5、多数据触发器MultiDataTrigger

多数据触发器，是对数据触发器的扩展，需要同时满足多个数据改变的情况下才执行。 

我们对5种触发器，在4个触发环境中，我进行了简单的分类，虽然有些触发器在有的触发方式中可以使用，但并不代表一定有作用，这个在使用中根据情况进行区分，分类如下： 

| 触发方式     | 可使用的触发器                                             | 说明                     |
| ------------ | ---------------------------------------------------------- | ------------------------ |
| 控件触发     | 必须使用EventTrigger                                       | 用于调整界面，实现动画等 |
| 样式触发     | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | 可使用所有触发器         |
| 控件模板触发 | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | 可使用所有触发器         |
| 数据模板触发 | TriggerEventTriggerMultiTriggerDataTriggerMultiDataTrigger | 可使用所有触发器         |
