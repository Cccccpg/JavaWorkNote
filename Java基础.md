# 1、基础
## 1.1 JDK JRE JVM的关系

- **JDK（Java Development Kit）**是针对 Java 开发员的产品，是整个 Java 的核 心，包括了 Java 运行环境 JRE、Java 工具和 Java 基础类库。 
- **JRE（Java Runtime Environment）**是运行 Java 程序所必须的环境的集合，包 含 JVM 标准实现及 Java 核心类库。 
- **JVM（Java Virtual Machine）**Java 虚拟机，是整个 java 实现跨平台的最核心 的部分，能够运行以 Java 语言写作的软件程序。 

简单来说， JDK 是 Java 的开发工具；JRE 是 Java 程序运行所需的环境， JVM是Java虚拟机。它们之间的关系是JDK包含JRE和JVM，JRE包含JVM。  
## 1.2 编译过程
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679454706412-2f89e2b0-be59-4aa3-a2d6-aa9f55b25dc0.png#averageHue=%23f3f3f3&clientId=u5dde27fc-2899-4&from=paste&height=112&id=ued306471&name=image.png&originHeight=112&originWidth=616&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36476&status=done&style=none&taskId=u632327b2-2572-44ed-a09f-955861398f6&title=&width=616)
javac还要检查语言规范，编译后转换为.class字节码文件
## 1.3 Java和C++的区别

- Java可以通过虚拟机实现跨平台的特性，C++依赖于特定的平台；
- Java没有指针，它的引用可以理解为安全指针，C++有指针；
- Java支持自动垃圾回收，C++需要手动回收；
- Java不支持多重继承，只能通过实现多个接口来达到相同目的，C++支持多重继承；
# 2、数据类型
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679454472637-292c5d37-a376-45e4-a6cf-8654f9b01215.png#averageHue=%23f7f6f6&clientId=u5dde27fc-2899-4&from=paste&height=492&id=udb85f8d1&name=image.png&originHeight=492&originWidth=819&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50985&status=done&style=none&taskId=u8b60bf88-17d5-4827-93fa-ee804863609&title=&width=819)
## 1.1 自动装箱与拆箱
**装箱：**将基本类型用包装器类型包装起来。
**拆箱：**将包装器类型转换为基本类型。
装箱其实就是调用了包装类的`valueOf()`方法。拆箱其实就是调用了`xxxValue()`方法。比如：
`Integer i = 10`等价于` Integer i = Integer.valueOf(10);  `
`int n = i`等价于`int n = i.intValue();`

---

**有了基本数据类型，为什么还要有包装类？**
Java 是一个面向对象的语言，而基本类型不具备面向对象的特性。 这是一 个设计缺陷，自动装箱与拆箱是为了补救这个缺陷。
（1）包装类里面有一些很 有用的方法和属性，如 HashCode,ParseInt。
（2）基本类型不能赋 null 值，但某些场合又需要。
（3）有些地方不能直接用基本类型，比如集合的泛型里面。
因此，光有基本数据类型是不行的，引入包装类，弥补基本数据类型的缺陷。

---

## 1.2 常量池缓存技术
在 Java 中基本类型的包装类的大部分都实现了常量池技术。比如： Byte,Short,Integer,Long 这 4 种包装类默认创建了数值 [-128，127] 的相 应类型的缓存数据，Character 创建了数值在[0,127]范围的缓存数据， Boolean 直接返回 True Or False 。两种浮点数类型的包装类  Float，Double没有实现常量池技术。
```java
public static void main(String[] args) {
    Integer e = 1;                  // 整型变量，存储在栈中
    Integer f = new Integer(1);     // 整型对象，存储在堆中
    Integer g = new Integer(128);

    System.out.println(e == f);     //这里'=='比较的是他们在内存中的地址，而不是值，所以不同
    System.out.println(e.equals(f));//要比较值，需要用equals方法，比较的是内容
    System.out.println(g == f);

    System.out.println("---------------------------");

    Double a = 1.0;
    Double b = 1.0;
    Double c = 2.0;
    Double d = 2.0;

    System.out.println(a == b);               // 浮点数是以二进制形式存储的，在数值计算时会产生精度误差
    System.out.println((c - 0) == (d - 0));   // 最好的方法是通过与定值作差，进行比较
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679457297213-2049d156-d8cd-423c-964e-f7147320d99e.png#averageHue=%232c2c2c&clientId=u5dde27fc-2899-4&from=paste&height=162&id=u558bbd8b&name=image.png&originHeight=162&originWidth=296&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4820&status=done&style=none&taskId=ue5720ea5-a118-43f1-8ecb-bdb00652b39&title=&width=296)
**包装类里面引入缓存技术的好处是什么？**
有助于节省内存，提高性能
## 1.3 String(不是基本数据类型)
 在 Java 8 中，String 内部使用 char 数组存储数据。并且被声明为 final，因此它不可被继承。  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679466446387-76f0b949-3f0b-44e2-a611-512bce261ac4.png#averageHue=%23312c2b&clientId=u5dde27fc-2899-4&from=paste&height=116&id=uc80783b0&name=image.png&originHeight=116&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18365&status=done&style=none&taskId=uc18aad2d-941b-46cd-9605-dfea9e995c9&title=&width=728)

---

### 1.3.1 为什么String要设计成不可变（不可变性的好处）
**1、可以缓存hash值**
因为String的hash值经常被使用，例如用String作为HashMap的key。String的不可变性可以使得hash值不可变，因此只需要一次计算。
2**、常量池优化**
String在创建对象后，会在**字符串常量池**中进行缓存，如果下次创建同样的对象时，会直接返回缓存的引用。
3**、线程安全**
String的不可变性天生具备线程安全的特性，可以在多个线程中安全地使用。
### 1.3.2 什么是字符串常量池
 字符串常量池位于**方法区**中，专门用来存储字符串常量，可以提高内存的使用率，避免开辟多块空间存储相同的字符串。**在创建字符串时 JVM 会首先检查字符串常量池，如果该字符串已经存在池中，则返回它的引用；如果不存在， 则实例化一个字符串放到池中，并返回其引用。**  
Q：`String str = new String("A"+"B");`会创建多少对象？
A：会创建两个对象：一个是"AB"，另一个是new String()对象，其中包含值"AB"。具体来说，"A"和"B"都是字符串常量，它们会在编译时会被JVM优化，被合并为一个字符串常量"AB"。然后，使用这个常量来创建一个新的String对象。因此，总共会创建两个对象。
### 1.3.3 String、StringBuffer、StringBuilder之间区别
**1、可变性**
String不可变；
StringBuffer和StringBuilder是可变的。
**2、线程安全性**
**String**由于不可变性，所以是**线程安全**的；
**StringBuffer**对方法加了同步锁或者对调用的方法加了同步锁，所以是**线程安全**的；
**StringBuilder**没有加同步锁，不是线程安全的。
**3、性能**
StringBuilder > StringBuffer > String
**4、各自适合使用的场景**
**String：**操作**少量数据**；
**StringBuffer：多线程**操作字符串缓冲区下操作大量数据；
**StringBuilder：单线程**操作字符串缓冲区下操作大量数据；
# 3、关键字
## 3.1 static
作用：方便在没有创建对象时，调用方法和变量、优化程序性能。
### 3.1.1 static变量
用 static 修饰的变量被称为**静态变量**，也被称为类变量，可以直接通过类名来访问它。
静态变量被所有的对象共享，在内存中只有一个副本，**仅当在类初次加载时会被初始化**，而非静态变量在创建对象的时候被初始化，并且存在多个副本，各个对象拥有的副本互不影响。  
### 3.1.2 static方法
static 方法不依赖于任何对象就可以进行访问，**在 static 方法中不能访问类的非静态成员变量和非静态成员方法**，因为非静态成员方法/变量都是必须依赖具体的对象才能够被调用，但是在非静态成员方法中是可以访问静态成员方法/变量的。  
```java
public class Test {
    //静态变量
    public static String s1 = "s1";
    //非静态变量
    public String s2 = "s2";
    
    //非静态方法
    public void fun1(){
        System.out.println(s1);
        System.out.println(s2);
    }

    //静态方法
    public static void fun2(){
        System.out.println(s1);
        System.out.println(s2); //直接报错，静态方法中不能调用非静态变量s2
    }
}
```
### 3.1.3 static代码块
静态代码块的主要用途是可以用来**优化程序的性能**，因为它只会在类加载时加载一次，很多时候会将一些**只需要进行一次的初始化操作都放在 static 代码 块中进行**。
如果程序中有多个 static 块，在类初次被加载的时候，**会按照 static 块的顺序来执行每个 static 块。**  
```java
public class Test {
    static {
       System.out.println("Hello world!");
    }

    public static void main(String[] args) {
        Test t = new Test();
    }
}
```
### 3.1.4 初始化顺序
静态变量和静态语句块**优先于**实例变量和普通语句块，静态变量和静态语句块的初始化顺序**取决于它们在代码中的顺序**。
如果存在继承关系的话，初始化顺序为：

1. **父类**中的**静态**变量和**静态**代码块
2. **子类**中的**静态**变量和**静态**代码块
3. **父类**中的**实例**变量和**普通**代码块
4. 父类的构造函数
5. **子类**中的**实例**变量和**普通**代码块
6. 子类的构造函数

**总结：静态优于普通，父类优于子类**
## 3.2 final

- 类：被修饰的类**不可以被继承**
- 方法：被修饰的方法**不可以被重写**
- 变量：被修饰的变量是基本类型，变量的数值**不能改变**；被修饰的变量是引用类型，变量便不能再引用其他对象，但变量所引用的对象本身是可改变的。
```java
public class Test {
    int a = 1;

    public static void main(String[] args) {
        final int b = 1;
        b = 2;//直接报错，因为b变量被final修饰了，不能改变
        final Test t = new Test();
        t.a = 2;//不报错，因为可以改变引用类型变量所指的对象
    }
}
```
### 3.2.1 final、finally、finalize之间区别

- **final**：主要用于修饰类、变量、方法；
- **finally：**一般用在try-catch中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。  
- **finalize：**属于Object类中的一个方法， 该方法一般由垃圾回收器来调用，当我们调用 System.gc()方法的时候，由垃圾回收器调用 finalize()， 回收垃圾。但 finalize()方法**不一定会被执行**。  
### 3.2.2 为什么finalize方法不一定会被执行？
因为JVM并不保证会在任何时刻都执行垃圾回收操作，所以也就无法保证**finalize()**方法会被调用。另外，**finalize()**方法的执行也可能被延迟或者被中断，这可能会导致**finalize()**方法不被执行。
因此，我们不能依赖于**finalize()**方法来进行重要的清理工作，尤其是对于需要确保资源正确释放的程序。相反，应该使用**try-with-resources**语句或者显式地在程序中调用**close()**方法来确保资源得到正确释放。

## 3.3 this
### 3.3.1 引用当前类的实例变量
主要用于形参与成员变量重名的时候，用this来区分
```java
String name;
int age;

public void Person(String name, int age){
    this.name = name;
    this.age = age;
}
```
### 3.3.2 调用当前类方法
```java
public class Test {
    public void fun1(){
        System.out.println("fun1");
    }

    public void fun2(){
        this.fun1();//其实这里this可以省略
    }

    public static void main(String[] args) {
        Test test = new Test();
        test.fun2();
    }
}
```
### 3.3.3 调用当前类的构造函数
**注意！**`this()`一定要放在构造函数的第一行，否则编译不通过。
```java
public class Person {
    private String name;
    private int age;

    public Person(){
    }

    public Person(String name){
        this.name = name;
    }

    public Person(String name, int age){
        this(name);//一定要放在构造函数的第一行
        this.age = age;
    }
}
```
### 3.3.4 可以通过this访问静态成员变量吗？
可以。this代表当前对象，可以访问静态成员变量，而静态方法中是不能访问非静态变量，也不能用this引用。
## 3.4 super
1、 super 可以用来**引用直接父类的实例变量**。和 this 类似，主要用于区分父类和子类中相同的字段；
2、 super 可以用来**调用直接父类构造函数**。(**注意：super()一定要放在构造函数的第一行**) ；
3、 super 可以用来**调用直接父类方法**。  
## 3.5 this和super的区别
**相同点：**

1. 都必须在构造函数的第一行调用；
2. 都指的是对象，均不可以在static环境中使用。

**不同点：**

1. super是对**父类构造函数**的调用，而this是对**重载构造函数**的调用；
2. super在继承了父类的子类的构造函数中使用，属于**不同类间使用**，而this是在**同一类**的不同构造函数中使用。

```java
public class Main {
    public static void main(String[] args) {
        Child child = new Child("Father", "Child");
        child.test();
    }
}

class Father{
    protected String name;

    public Father(String name){
        this.name = name;
    }

    public void say(){
        System.out.println("hello，child");
    }
}

class Child extends Father{
    private String name;

    public Child(String name1, String name2){
        super(name1);//直接调用父类的构造函数
        this.name = name2;
    }

    public void test(){
        System.out.println(this.name);
        System.out.println(super.name);//引用直接父类的实例变量
        super.say();//调用直接父类的方法
    }
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679472094788-36c86836-6d2e-436a-851d-b94eace7c068.png#averageHue=%232f2e2d&clientId=u5dde27fc-2899-4&from=paste&height=86&id=u05754640&name=image.png&originHeight=86&originWidth=140&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2954&status=done&style=none&taskId=u2447f303-4426-40d3-9e32-cda76013940&title=&width=140)
# 4、面向对象
## 4.1 面向对象和面向过程的区别
面向对象和面向过程是两种编程的思想。
**面向对象**的编程方式使得每一个类 都只做一件事，像雇佣了一群职员，每个人做一件小事，各司其职，最终合作共赢。
**面向过程**会让一个类越来越全能，就像一个管家一样，一个做了所有的事。 
面向对象：优点：易维护、易复用、易扩展。
缺点：性能比面向过程低。 
面向过程：优点：性能比面向对象高。
缺点：但没有面向对象易维护、易复用、易扩展，开销比较大，比较消耗资源。  
## 4.2 封装、继承、多态

- **封装**：封装就是隐藏对象的属性和实现细节，仅对外公开接口，控制在程序中属性的读和修改的访问级别。（private / get/set 方法）。就好像我们看不到挂在墙上的空调的内部的零件信息（也就是属性），但是可以通过遥控器（方法）来控制空调。
- **继承**：继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。 通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。
  - 子类**拥有**父类对象所有的属性和方法（<u>包括私有属性和私有方法</u>），但是父类中的私有属性和方法子类是**无法访问**，**只是拥有**。
  - 子类可以拥有自己属性和方法，即**子类可以对父类进行扩展**。
  - 子类可以用自己的方式实现父类的方法。

- **多态**：表示一个对象具有多种的状态，具体表现为父类的引用指向子类的实例。
  - 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
  - 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
  - 多态不能调用“只在子类存在但在父类不存在”的方法；
  - 如果子类重写了父类的方法，**真正执行的是子类覆盖的方法**，如果子类没有覆盖父类的方法，**执行的是父类的方法**。


在 Java 中实现多态的三个必要条件：**继承、重写、向上转型**。继承和重写很好理解，向上转型是指在多态中需要将子类的引用赋给父类对象。  
```java
public class Main {
    public static void main(String[] args) {
        Father father = new Child();    //向上转型，将子类引用赋予父类对象
        father.run();
    }
}

class Father{
    public void run(){
        System.out.println("Father run");
    }
}

class Child extends Father{ //继承
    @Override
    public void run(){      //重载
        System.out.println("Child run");
    }
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679473489104-b6d00382-f0e2-4d74-9a0e-e12187c6dbca.png#averageHue=%23312f2e&clientId=u5dde27fc-2899-4&from=paste&height=29&id=ubf63cc7e&name=image.png&originHeight=29&originWidth=114&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1376&status=done&style=none&taskId=ubaf7ac9d-269f-44eb-bf56-7f13949b3b1&title=&width=114)
# 5、重载和重写的区别
在Java中，方法重载（overloading）和方法重写（overriding）**都是实现多态的方式**。

---

**重载：**是指在**同一个类中**定义两个或多个方法，它们具有相同的名称，但是参数列表不同。当程序调用这个方法时，Java编译器根据调用时提供的参数类型和数量来确定使用哪个方法。**重载的方法不能根据返回类型进行区分。**

----

**重写：**是指在一个子类中定义一个与父类中**同名**、**同参数**的方法，这个方法会覆盖父类中的方法。当程序使用父类的对象调用这个方法时，实际上会调用子类中的方法。子类中重写的方法**返回值类型要 ≤ 父类**， **抛出的异常 ≤ 父类**，**访问修饰符 ≥ 父类**；如果父类中该方法访问修饰符为 `private/final/static`则子类中就不能重写。  

**方法的重写要遵循“两同两小一大”**

---

重载就是同一个类中多个同名方法根据不同的传参来执行不同的逻辑处理。

重写就是子类对父类的重新改造，外部样子不能变，内部逻辑可以改变。

# 6、抽象类和接口的对比
抽象类：用来捕捉子类的通用特性的；
接口：抽象方法的集合。

## 6.1 两者异同
**相同点**：

1. 都不能实例化；
2. 都包含抽象方法，其子类都必须对这些方法进行重写。
3. 都可以有默认实现的方法；

**不同点**：

1. 接口中只能有抽象方法，抽象类中可以有非抽象的方法。
2. 接口中变量只能是`public/static/final `类型，抽象类则可以有其他变量。
3. 一个类可以实现多个接口，但是只能继承一个抽象类。
4. 接口的方法默认是 public ，而抽象方法可以有 `public、protected、default`，但不能用 private  

## 6.2 接口应用场景

- 类与类之前需要特定的接口进行协调，而不在乎其如何实现。
- 作为能够实现特定功能的标识存在，也可以是什么接口方法都没有的纯粹标识。
- 需要将一组类视为单一的类，而调用者只通过接口来与这组类发生联系。
- 需要实现特定的多项功能，而这些功能之间可能完全没有任何联系。  
## 6.3 抽象类应用场景
一句话，**在既需要统一的接口，又需要实例变量或缺省的方法的情况下，就可以使用它。**
1、定义了一组接口，但又不想强迫每个实现类都必须实现所有的接口。可以用 abstract class 定义一组方法体，甚至可以是空方法体，然后由子类选择自己所感兴趣的方法来覆盖。 
2、某些场合下，只靠纯粹的接口不能满足类与类之间的协调，还必需类中表示状态的变量来区别不同的关系。abstract 的中介作用可以很好地满足这一点。 
3、规范了一组相互协调的方法，其中一些方法是共同的，与状态无关的，可以共享的，无需子类分别实现；而另一些方法却需要各个子类根据自己特定的状 态来实现特定的功能。  
# 7、内部类
 内部类包含：**成员内部类**、**局部内部类**、**匿名内部类**和**静态内部类**。  
## 7.1 成员内部类
定义：位于另一个类的内部，成员内部类可以**无条件**访问外部类的**所有成员属性**和**成员方法**（**包括 private 成员和静态成员**）。
```java
public class Outer {
    private double a = 0;
    public static int b = 1;
    
    public Outer(double a){
        this.a = a;
    }
    
    class Inner{    //内部类
        public void fun(){
            System.out.println(a);//访问private成员
            System.out.println(b);//访问静态成员
        }
    }
}
```
1、当成员内部类拥有和外部类**同名**的成员变量或者方法时，**默认情况下访问的是成员内部类的成员**。如果要访问外部类的同名成员，需要以下面的形式进行访问：外部类.this.成员变量  
2、在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问。  
3、成员内部类是依附外部类而存在的，如果要创建成员内部类的对象，前提是必须存在一个外部类的对象。

```java
public class Outer {
    private double a = 0;
    public static int b = 1;
    public Outer(){
    }

    public Outer(double a){
        this.a = a;
        Inner inner = new Inner();  //创建内部类对象
        inner.fun();                //调用内部类方法
    }

    class Inner{    //内部类
        int b = 2;

        public void fun(){
            System.out.println(a);
            System.out.println(b);              //访问内部类成员变量的b
            System.out.println(Outer.this.b);   //访问外部类的成员变量b
        }
    }
}

class Test{
    public static void main(String[] args) {
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();  //创建内部类对象
    }
}
```
## 7.2 局部内部类
定义：是定义在一个**方法**或者一个**作用域**里面的类。
它和成员内部类的区别在于**局部内部类**的**访问仅限于方法内或者该作用域内**。定义在**实例方法**中的局部类**可以访问外部类的所有变量和方法**，定义在**静态方法**中的局部类**只能访问外部类的静态变量和方法**。  

```java
public class Outer {
    private int outer_a = 1;
    private static int static_b = 2;

    public void test1(){
        int inner_c = 3;
        class Inner {
            private void fun(){
                System.out.println(outer_a);
                System.out.println(static_b);
                System.out.println(inner_c);
            }
        }
        Inner inner = new Inner();  //创建局部内部类
        inner.fun();
    }

    public static void test2(){
        int inner_d = 3;
        class Inner{
            private void fun(){
                //编译错误
                //定义在静态方法中的局部内部类不能访问外部类的实例变量
                System.out.println(outer_a);
                System.out.println(static_b);
                System.out.println(inner_d);
            }
        }
        Inner inner = new Inner();  //创建局部内部类
        inner.fun();
    }
}
```
## 7.3 匿名内部类
定义：没有名字的内部类，在日常开发中使用较多。
**注意：**使用匿名内部类的前提条件是**必须继承一个父类或者实现一个接口。**
```java
interface Person{
    public void fun();
}

public class Demo {
    public static void main(String[] args) {
        new Person(){	//匿名内部类
            public void fun(){
                System.out.println("hello");
            }
        }.fun();
    }
}
```
## 7.4 静态内部类
 静态内部类是不需要依赖于外部类的，并且它**不能使用外部类的非 static 成员变量或者方法 **。
## 7.5 内部类优点
（1）内部类不为同一包的其他类所见，**具有很好的封装性**； 
（2）匿名内部类可以很方便的定义回调。 
（3）每个内部类都能独立的继承一个接口的实现，所以无论外部类是否已经继承了某个(接口的)实现，对于内部类都没有影响。 
（4）内部类有效实现了“多重继承”，优化 java 单继承的缺陷。  
# 8、hashCode和equals
## 8.1 equals
先看String中的equals源码
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679553851940-4b13da2a-f053-4daf-9905-1d2bae657321.png#averageHue=%23302c2b&clientId=u1d8470d1-5a2b-4&from=paste&height=552&id=uad402c65&name=image.png&originHeight=552&originWidth=504&originalType=binary&ratio=1&rotation=0&showTitle=false&size=49948&status=done&style=none&taskId=u810c1e2f-e604-4f74-a2e0-866b105097d&title=&width=504)
equals 方法会依次比较**引用地址、对象类型、值的内容**是否相同，都相同才会返回true。所以`equals`方法比`==`比较的范围更大、内容更多。
用`==`判断为true的两个值，用`equals`判断不一定为true。
```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b);      // true
System.out.println(a.equals(b)); // true

Integer c = 200;
Integer d = 200;
System.out.println(c == d);      // false，因为200超出了常量池缓存的范围，所以此时为false
System.out.println(c.equals(d)); // true

String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);      // false
System.out.println(s1.equals(s2)); // true
```
## 8.2 hashCode
hashCode 方法返回对象的散列码，返回值是 int 类型的散列码。散列码的作用是确定该对象在哈希表中的索引位置。  
关于hashCode有一些约定：

1. 两个对象的值相等，则hashCode一定相同。
2. 两个对象有相同的hashCode值，它们不一定相等。
3. hashCode()方法默认是对堆上的对象产生独特值，如果没有重写 hashCode()方法，则该类的两个对象的 hashCode 值肯定不同。  
## 8.3 为什么重写equals方法后，hashCode方法也要重写？
以 `HashSet` 为例，`HashSet` 的特点是存储元素时**无序**且**唯一**，在向 `HashSet` 中添加对象时，首先会计算对象的 `HashCode` 值来确定对象的存储位置，如果该位置没有其他对象，直接将该对象添加到该位置；如果该存储位置有存储其他对象（此时新添加的对象和该存储位置的对象的HashCode值相同），则会调用 `equals` 方法判断两 个对象是否相同，如果相同，则添加对象失败，如果不相同，则会将该对象重新散列到其他位置。
**所以重写 equals 方法后，hashCode 方法不重写的话，会导致所有对象的 HashCode 值都不相同，都能添加成功，那么 HashSet 中会出现很多重复元素。 **
# 9、Java中只存在值传递
```java
public class Demo {
    public static void main(String[] args) {
        int a = 1;
        printValue(a);
        System.out.println("a:" + a);
    }

    public static void printValue(int b){
        b = 2;
        System.out.println("b:" + b);
    }
}
```
运行结果：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679554597433-29c2e45a-eded-4e98-bad9-e5c46d1f0285.png#averageHue=%232e2d2d&clientId=u1d8470d1-5a2b-4&from=paste&height=54&id=u0a9872d4&name=image.png&originHeight=54&originWidth=67&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1322&status=done&style=none&taskId=u70694ad4-5259-41fb-88e8-b339c154a3e&title=&width=67)
可以看到将 a 的值传到 `printValue` 方法中，并将其值改为 2。但方法调用 结束后，a 的值还是 1，并未发生改变，所以这种情况下为值传递。  
**值传递：**是指在调用函数时将实际参数**复制一份**传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。 
**引用传递：**是指在调用函数时将实际参数的地址**直接传递**到函数中，那么在 函数中对参数所进行的修改，将影响到实际参数。
可以明显看出，值传递和引用传递的区别在于向方法中传递的是**实参的副本**还是**实参地址**。
# 10、IO流
IO流主要可以分为**输入流**和**输出流。**
按照操作单元划分，可以划分为**字节流**和**字符流**。
按照流的角色划分，可以划分为**节点流**和**处理流**。
Java IO流的40多个类都是从四个抽象类基类派生出来的：
`InputStream`：字节输入流
`OutputStream`：字节输出流
`Reader`：字符输入流
`Writer`：字符输出流
## 10.1 有了字节流为什么还需要字符流？
虽然字节流是信息处理的最小单位，但字符流是JVM转换得到，这个过程**比较耗时**，并且还**容易出现乱码问题**，因此Java在IO中就提供了可直接操作字符的字符流。
# 11、常见IO模型
在操作系统中， 为了保证操作系统的稳定性和安全性，一个进程的地址空间划分为**用户空间 （User space）**和 **内核空间（Kernel space ）**。对于一次 IO 访问，数据会先被拷贝到内核的缓冲区中，然后才会从内核的缓冲区拷贝到应用程序的地址空间。
当发起 I/O 调用后，会经历两个步骤：  
（1）内核等待 I/O 设备准备好数据。 
（2）内核将数据从内核空间拷贝到用户空间。  
由于存在这两个步骤，所以Linux产生了下面五种IO模型（BIO，NIO，IO多路复用，AIO，信号驱动IO），Java中前三种模型比较常见。
## 11.1 BIO（Blocking IO）
BIO属于**同步阻塞IO模型**，在该模型中， 应用程序发起 `read()`调用后，会一直阻塞，直到内核把数据拷贝到用户空间。
![](https://cdn.nlark.com/yuque/0/2021/png/1283027/1629204693529-5dc2ed49-c461-4992-9e1d-3f80cf6597c7.png#averageHue=%23faf5f1&from=url&id=S8LSl&originHeight=476&originWidth=888&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
在客户端连接数量不高的情况下，这种模式是没问题的。但是，当面对十万甚至百万级连接的时候，传统的BIO模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。  
## 11.2 NIO（Non-blocking/New IO）
NIO属于**同步非阻塞IO模型**，  在 Java 1.4 中就引入了 NIO 的概念， 对应于 java.nio 包，提供了 `Channel`,`Selector`,`Buffer` 等抽象类。
它是一种支持面向缓冲的，基于通道的 I/O 操作方法。对于**高负载**、**高并发**的（网络） 应用，应使用 NIO 。在**同步非阻塞 IO** 模型中，应用程序会**一直发起 read()调用**，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间。
相比于**同步阻塞 IO 模型**，同步非阻塞 IO 模型确实有了很大改进。通过轮询操作，避免了一直阻塞。但是，这种 IO 模型同样存在问题：应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是**十分消耗 CPU 资源的**。
![](https://cdn.nlark.com/yuque/0/2021/png/1283027/1629204717413-32393d5c-5ad4-4691-b9ca-7c6363952e72.png#averageHue=%23f9f3ef&from=url&id=O71LR&originHeight=510&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## 11.3 IO多路复用
**IO 多路复用模型**是通过一种机制，让一个进程可以**监视**多个文件描述符（套接字描述符）一旦某个文件描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作，这样就不需要每个用户进程不断的询问内核数据准备好了没。**通过减少无效的系统调用，减少了对 CPU 资源的消耗。**常用的 IO 多路复用方式有 `select`、`poll` 和 `epoll ` 。
![](https://cdn.nlark.com/yuque/0/2021/png/1283027/1629205042586-b34f7478-47b6-4403-8a28-7e3e324a6b00.png#averageHue=%23faf5f2&from=url&id=sihK4&originHeight=540&originWidth=908&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
IO 多路复用模型中，

- 首先将进行IO操作的**socket添加到select**中；
- 然后**阻塞等待select系统调用返回**，
- 当**数据到达时，socket就被激活**，**select函数返回**，**用户发起read请求**，即**可获取数据**	

select函数**避免了NIO轮询等待**，**创建多个socket**，通过不断**调用select读取被激活的socket**，**实现在同一个线程内同时处理多个IO请求**。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679558205592-ff04ccea-f308-4f38-bf89-766ca146fa9e.png#averageHue=%23f7f7f7&clientId=u1d8470d1-5a2b-4&from=paste&height=316&id=uca25be5e&name=image.png&originHeight=316&originWidth=927&originalType=binary&ratio=1&rotation=0&showTitle=false&size=24468&status=done&style=none&taskId=u25358300-cc5a-4d0e-898a-ec8b248948a&title=&width=927)
Java 中的 NIO ，有一个非常重要的**选择器 ( Selector )** 的概念，也可以被称为 **多路复用器**。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。
## 11.4 AIO（Asynchronous IO）
AIO就是**异步IO模型，**AIO 也就是 NIO 2。Java 7 中引入了 NIO 的改进版 NIO 2,它是异步 IO 模型。
异步 IO 是基于**事件**和**回调机制**实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。
![](https://cdn.nlark.com/yuque/0/2021/png/1283027/1630136724091-98d0575d-a275-477c-8c7d-9cb4ca1831a2.png#averageHue=%23faf5f2&from=url&id=kIBJw&originHeight=534&originWidth=1202&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
## 11.5 BIO、NIO、AIO的区别
举个生活中简单的例子，你妈妈让你烧水
**同步阻塞BIO**：  小时候你比较笨啊，在哪里傻等着水开（**傻傻等待数据的到达**）
**优点：实现简单；           		缺点：线程阻塞，并发能力差**
**同步非阻塞NIO：  **等你稍微大一点，你知道烧水的空隙可以去玩，只需时不时来看看水开了没有（**轮询**）
**优点：线程不需要阻塞； 		缺点：每个线程都需要多次轮询**
**异步非阻塞AIO** ： 后来你家用上水开会发声的壶，你只需听到响声就知水开了，期间可以随便玩（**通知**）
**优点：非阻塞，不需要轮询**
# 12、Java反射机制
 Java 反射机制指在运行状态中，对于任意一个类，都能够获取这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。
这种动态获取类信息以及动态调用对象的方法的功能称为 **Java 语言的反射机制**。  
```java
class Person{
    public String name = "Jack";
    public Person(){
    }
}

public class Demo {
    public static void main(String[] args) throws ClassNotFoundException {
        //方法1：通过对象实例instance.getClass()获取
        Person person1 = new Person();
        Class c1 = person1.getClass();

        //方法2：知道具体的类名的情况下可以使用类名.class
        Class c2 = Person.class;

        //方法3：通过Class.forName()传入类的路径获取
        //但这种方法有可能会抛出ClassNotFoundException异常
        Class c3 = Class.forName("Test.Person");

        //方法4：通过类加载器xxxClassLoader.loadClass()传入类的路径获取
        //需要先创建一个类加载器
        Class c4 = ClassLoader.getSystemClassLoader().loadClass("Test.Person");

        System.out.println(c1.equals(c2));	//true
        System.out.println(c1.equals(c3));	//true
        System.out.println(c1.equals(c4));	//true
    }
}
```
因为在一个类在 JVM 中只会有一个 Class 实例，所以对 c1、c2、c3、c4 进行 equals 比较时返回的都是 true 。
## 12.1 反射机制优缺点
**优点**：可以让代码更加灵活、为各种框架提供开箱即用的功能提供了便利
**缺点**：增加了安全问题。比如可以无视泛型参数的安全检查；反射的性能也要稍差点。
## 12.2 为什么反射的性能会差？

1. 反射需要**动态解析类的信息**，包括访问修饰符、字段、方法、参数、注解等，因此**需要进行大量的运行时检查和解析**，会比直接调用代码的执行速度慢。
2. 反射机制在执行时会**涉及到许多动态分配对象的操作**，这些操作会**占用大量的内存**，并且**需要进行垃圾回收**，导致额外的性能损耗。
3. 反射方法**的调用通常比直接调用方法慢很多**，因为它需要进行许多额外的操作，如方法解析、参数类型检查、安全检查等。
4. 反射方法的调用通常**不能进行编译时优化**，因此会导致运行时性能低下。
# 13、Java异常
在Java中，所有的异常都有一个共同的祖先java.lang包中的 Throwable 类。
Throwable 类有两个重要的子类 **Exception**（异常）和 **Error**（错误）。
Exception **能被程序本身处理**( try/catch )，Error 是**⽆法处理**的(只能尽量避免)。
Exception 和 Error 二者都是 Java 异常处理的重要⼦类，各自都包含⼤量⼦类。  
## 13.1 常见异常
SQLException 操作数据库异常 
IOException 输入输出异常 
ConcurrentModificationException 并发修改异常 
NullPointException 空指针异常 
ArrayOutOfBoundsException 数组下标越界异常 
ClassCastException 强制类型转换异常 
Virtual MachineError  JVM运行错误
StackOverFlowError 栈溢出错误 
OutOfMemoryError 堆空间不足错误 
# 14、Java序列化
序列化就是将对象写入到IO流中。
**为什么要序列化？**
将 Java 对象转换成字节序列，这些字节序列更加便于通过网络传输或存储在磁盘上，在需要时可以通过反序列化恢复成原来的对象。  
# 15、深拷贝与浅拷贝
**浅拷贝：** 对基本数据类型进行值传递，对引用数据类型，**复制一个引用**指向原始引用的对象，就是让复制的引用和原始引用指向同一个对象。  
**深拷贝：** 对基本数据类型进行值传递，对引用数据类型，**创建一个新的对象**， 并**复制**其内容，两个引用指向两个对象，但对象内容相同。  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679623081652-52078d27-a1ae-45f2-a1f4-380158f6314b.png#averageHue=%23f8f8f8&clientId=u4417e0f8-1416-4&from=paste&height=273&id=u2606af47&name=image.png&originHeight=273&originWidth=791&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18817&status=done&style=none&taskId=ua6c57ac4-9ac1-4878-83c1-75fc078dd49&title=&width=791)
## 15.1 深拷贝实现方式
重载`clone`方法
```java
class Address implements Cloneable{
    private String city;
    public Address(String name){
        this.city = name;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        //这里的super.clone为浅拷贝
        return super.clone();
    }

    public String getName() {
        return city;
    }

    public void setName(String address) {
        this.city = address;
    }
}

class Person implements Cloneable{
    private String name;
    private int age;
    private Address address;
    public Person(String name, int age, Address address){
        this.name = name;
        this.age = age;
        this.address = address;
    }

    @Override
    //深拷贝
    public Object clone() throws CloneNotSupportedException{
        Person person = (Person) super.clone();
        //调用address.clone重新赋值，因为address为引用类型
        person.address = (Address)address.clone();
        return person;
    }
    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("天津");
        Person person = new Person("张三",23,address);
        System.out.println("拷贝前的地址：" + person.getAddress().getName());
        //深拷贝
        Person clonePerson = (Person)person.clone();
        clonePerson.getAddress().setName("北京");
        System.out.println("拷贝后的地址：" + clonePerson.getAddress().getName());
    }
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679624312548-77de3d3e-4229-41b4-8834-a13f9b90b6d2.png#averageHue=%23373737&clientId=u4417e0f8-1416-4&from=paste&height=62&id=u65e984a7&name=image.png&originHeight=62&originWidth=185&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1040&status=done&style=none&taskId=u8bfa93e9-66bb-40db-b9ee-d36c17e1e71&title=&width=185)
# 16、常见的Object方法
`Object clone()`：创建与该对象的类相同的新对象。  
`boolean equals(Object)`：比较两对象是否相等。  
`void finalize()`：当垃圾回收器确定不存在对该对象的更多引用时，对象垃圾回收器调用该方法。 
`Class getClass()`：返回一个对象运行时的实例类。 
`int hashCode()`：返回该对象的散列码值。 
`void notify()`：唤醒等待在该对象的监视器上的一个线程。 
`void notifyAll()`：唤醒等待在该对象的监视器上的全部线程。 
`String toString()`：返回该对象的字符串表示。 
`void wait()`：在其他线程调用此对象的notify() 方法或 notifyAll()方法前，导致当前线程等待。  









