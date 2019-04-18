

http://how2j.cn/k/j2se-interview/j2se-interview-java/624.html#

https://blog.csdn.net/hope900/article/details/78647466

## 1.多线程

### （1）voliatile和synchonized有什么区别？

https://www.jianshu.com/p/0fc7ebb8d4f8

* volatile特性：

1. 当一个线程要使用volatile变量时，它会直接从主内存中读取，而不使用自己工作内存中的副本。

   当一个线程对一个volatile变量写时，它会将变量的值刷新到共享内存(主内存)中。

2. 禁止指令重新排序优化

3. 不能保证原子性

* synchonized

  synchonized是通过上锁来防治出现并发问题。synchronized作用的代码范围对于不同线程是互斥的，并且线程在释放锁的时候会将共享变量的值刷新到共享内存中。

**区别**：

1. 使用：voliatile 用于修饰变量，synchronized可以修饰对象，类，方法，代码块，语句。

2. 原子性：voliatile只保证变量的可见性，不能用于同步变量，即不保证原子性，多线程并发访问voliatile修饰的变量时也不会产生阻塞。synchronized是原子性的，只有锁定了变量的线程才能进入临界区，从而保证临界区的所有语句全部执行。多线程并发访问sychronized修饰的变量会产生阻塞。

3. 机理：

   当线程对volatile变量读时，会把工作内存中值置为无效。当线程对sychronized变量读时，会在该线程锁定变量时把工作内存中值置为无效。

   当线程对voliatile变量写时，会把值刷新到主内存中。当线程对sychronized变量写时，会在变量解锁时把值刷新到主内存中。

### （2）synchonized和jdk提供的Lock包有什么区别？

​	synchronized是基于jvm底层实现的数据同步，lock是基于Java编写，主要通过硬件依赖CPU指令实现数据同步。与synchronized不同的是lock是纯java手写的，与底层的JVM无关。在java.util.concurrent.locks包中有很多Lock的实现类

​	**1.synchronized**

　　优点：实现简单，语义清晰，便于JVM堆栈跟踪，加锁解锁过程由JVM自动控制，提供了多种优化方案，使用更广泛

　　缺点：悲观的排他锁，不能进行高级功能

　　**2.lock**

　　优点：可定时的、可轮询的与可中断的锁获取操作，提供了读写锁、公平锁和非公平锁　　

　　缺点：需手动释放锁unlock，不适合JVM进行堆栈跟踪

　　**3.相同点**　

　　都是可重入锁

总结来说，Lock和synchronized有以下几点不同：

　　1）Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；

　　2）synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；

　　3）Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；

　　4）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。

　　5）Lock可以提高多个线程进行读操作的效率。

　　在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。

### （3）线程池

​	为了防止线程频繁的创建撤销所带来的内存消耗。线程池中有一些核心线程永远不会被清理，当有一个任务需要执行时，首先判断线程池中的核心线程是否都在执行，如果没有，则创建一个工作线程执行任务，否则将任务放入等待队列，如果队列已满，则判断线程池（核心线程+非核心线程）是否已经满了，如果未满，则创建非核心线程执行该任务，如果满了就调用拒绝策略来管理任务。

https://blog.csdn.net/weixin_40271838/article/details/79998327

## 2.Java集合

### （1）**HashMap**

https://www.cnblogs.com/chengxiao/p/6059914.html

https://segmentfault.com/a/1190000012926722

* **定义**

​        HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap中的链表出现越少，性能才会越好。

* **动态扩容**

​        当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置。

* **Java8和Java7的区别**

  1. 在Java8中当链表过长时使用红黑树保存节点。

  2. Java8扩容时不会重新计算节点的哈希值，因为在计算哈希值时最后会与数组长度取余（与操作来取余），故索引位置要么是原位置，要么是在原位置再移动2次幂的位置，所以无需计算。

  3. Java7中扩容时，索引位置相同的节点会倒置，Java8不会。

  4. Java8中重新定义了Node<K,V>实现了Map.Entry<K,V>

     <https://zhuanlan.zhihu.com/p/21673805>

**多线程并发问题**：

1. 现在假如A线程和B线程同时对同一个数组位置调用addEntry，两个线程会同时得到现在的头结点，然后A写入新的头结点之后，B也写入新的头结点，那B的写入操作就会覆盖A的写入操作造成A的写入操作丢失。

2. 当多个线程同时操作同一个数组位置的时候，也都会先取得现在状态下该位置存储的头结点，然后各自去进行计算操作，之后再把结果写会到该数组位置去，其实写回的时候可能其他的线程已经就把这个位置给修改过了，就会覆盖其他线程的修改。

3. 当多个线程同时检测到总数量超过门限值的时候就会同时调用resize操作，各自生成新的数组并rehash后赋给该map底层的数组table，结果最终只有最后一个线程生成的新数组被赋给table变量，其他线程的均会丢失。而且当某些线程已经完成赋值而其他线程刚开始的时候，就会用已经被赋值的table作为原始数组，这样也会有问题。

   多线程扩容时出现的问题：<https://blog.csdn.net/chisunhuang/article/details/79041656>

### （2）ConcurrentHashMap

分段锁：容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了，这样便可以有效地提高并发效率。

其实可以看出JDK1.8版本的ConcurrentHashMap的数据结构已经接近HashMap，相对而言，ConcurrentHashMap只是增加了同步的操作来控制并发，从JDK1.7版本的ReentrantLock+Segment+HashEntry，到JDK1.8版本中synchronized+CAS+HashEntry+红黑树。

1.数据结构：取消了Segment分段锁的数据结构，取而代之的是数组+链表+红黑树的结构。
2.保证线程安全机制：JDK1.7采用segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。
3.锁的粒度：原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁（Node）。
4.链表转化为红黑树:定位结点的hash算法简化会带来弊端,Hash冲突加剧,因此在链表节点数量大于8时，会将链表转化为红黑树进行存储。
5.查询时间复杂度：从原来的遍历链表O(n)，变成遍历红黑树O(logN)。

### （3）**HashMap和hashtable区别？**

 1、HashMap是非线程安全的，HashTable是线程安全的。 
 2、HashMap的键和值都允许有null值存在，而HashTable则不行。 
 3、因为线程安全的问题，HashMap效率比HashTable的要高。 
 4、Hashtable是同步的，而HashMap不是。因此，HashMap更适合于单线程环境，而Hashtable适合于多线程环境。 

​        一般现在不建议用HashTable, ①是HashTable是遗留类，内部实现很多没优化和冗余。②即使在多线程环境下，现在也有同步的ConcurrentHashMap替代，没有必要因为是多线程而用HashTable。     

## 3.异常

Throwable又派生出Error类和Exception类。

错误：Error类以及他的子类的实例，代表了JVM本身的错误。错误不能被程序员通过代码处理，Error很少出现。因此，程序员应该关注Exception为父类的分支下的各种异常类。

异常：Exception以及他的子类，代表程序运行时发送的各种不期望发生的事件。可以被Java异常处理机制使用，是异常处理的核心。

![](C:\Users\admin\Desktop\董兰天\面试基础知识总结\images\异常.png)

非检查异常（unckecked exception）：Error 和 RuntimeException 以及他们的子类。javac在编译时，不会提示和发现这样的异常，不要求在程序处理这些异常。所以如果愿意，我们可以编写代码处理（使用try…catch…finally）这样的异常，也可以不处理。对于这些异常，我们应该修正代码，而不是去通过异常处理器处理 。这样的异常发生的原因多半是代码写的有问题。如除0错误ArithmeticException，错误的强制类型转换错误ClassCastException，数组索引越界ArrayIndexOutOfBoundsException，使用了空对象NullPointerException等等。

检查异常（checked exception）：除了Error 和 RuntimeException的其它异常。javac强制要求程序员为这样的异常做预备处理工作（使用try…catch…finally或者throws）。在方法中要么用try-catch语句捕获它并处理，要么用throws子句声明抛出它，否则编译不会通过。这样的异常一般是由程序的运行环境导致的。因为程序可能被运行在各种未知的环境下，而程序员无法干预用户如何使用他编写的程序，于是程序员就应该为这样的异常时刻准备着。如SQLException , IOException,ClassNotFoundException 等。

## 4.抽象类和接口

- 区别

1. 一个类可以实现多个接口，但只能继承一个抽象类
2. 抽象类可以有默认的方法实现，而在1.8之前，接口不可以有默认的方法实现。1.8中，接口可以为default类型的方法提供默认实现。
3. 抽象方法可以有**public**、**protected**和**default**这些修饰符，接口方法默认修饰符是**public**。你不可以使用其它修饰符。
4. 抽象类中子类使用**extends**关键字来继承抽象类，如果子类不是抽象类的话，它需要提供抽象类中所有声明的方法的实现；接口中子类使用关键字**implements**来实现接口。它需要提供接口中所有声明的方法的实现。
5. 抽象类可以有构造方法，接口不能有构造方法。
6. 抽象方法可以有main方法并且我们可以运行它；接口没有main方法，因此我们不能运行它。

## 5.Object方法

```java
 1 registerNatives()    //私有方法
 2 getClass()          //返回此 Object 的运行类。
 3 hashCode()          //用于获取对象的哈希值。
 4 equals(Object obj)     //用于确认两个对象是否“相同”。
 5 clone()               //创建并返回此对象的一个副本。 
 6 toString()           //返回该对象的字符串表示。   
 7 notify()            //唤醒在此对象监视器上等待的单个线程。   
 8 notifyAll()         //唤醒在此对象监视器上等待的所有线程。   
 9 wait(long timeout)    //在其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者超过指定的时间    量前，导致当前线程等待。   
10 wait(long timeout, int nanos)    //在其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者其    他某个线程中断当前线程，或者已超过某个实际时间量前，导致当前线程等待。
11 wait()    //用于让当前线程失去操作权限，当前线程进入等待序列
12 finalize()    //当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。
```

## 6.包装类

1.自动装箱、拆箱



2.内部cach

        Integer和Long类的内部都定义了一个cach用来存放-128到127之间的数，当我们赋值的数在这个区间时，使用的是方法区常量池中缓存数据，因此使用==比较时相等。范围之外的数就会新建一个对象，因此对象地址不同。
        
```java
    Long a=128;
    Long b=128;
    System.out.println(a==b);//false
    Long a=127;
    Long b=127;
    System.out.println(a==b);//true
```

源码解析：

```java
    private static class LongCache {
            private LongCache(){}
    
            static final Long cache[] = new Long[-(-128) + 127 + 1];
    
            static {
                for(int i = 0; i < cache.length; i++)
                    cache[i] = new Long(i - 128);
            }
        }
    public static Long valueOf(long l) {
            final int offset = 128;
            if (l >= -128 && l <= 127) { // will cache
                return LongCache.cache[(int)l + offset];
            }
            return new Long(l);
        }
```



## 

## 设计模式

http://www.runoob.com/design-pattern/singleton-pattern.html

https://blog.csdn.net/qq_33326449/article/details/78946364

共23种，分为三类：创建型（单例模式、工厂模式……）、结构型模式（代理模式、适配器模式……）、行为型模式（策略模式、观察者模式……）

### **1.单例模式**

**简介：**

该类只有一个对象被创建，类本身创建自己的对象。

- 1、单例类只能有一个实例。
- 2、单例类必须自己创建自己的唯一实例。
- 3、单例类必须给所有其他对象提供这一实例。

**实现：**

1. 饿汉式

   ```java
   public class Singleton {  
       private static Singleton instance = new Singleton();  
       private Singleton (){}  
       public static Singleton getInstance() {  
       return instance;  
       }  
   }
   ```

2. 懒汉式

   ```java
   public class Singleton {  
       private static Singleton instance;  
       private Singleton (){}  
     
       public static Singleton getInstance() {  
       if (instance == null) {  
           instance = new Singleton();  
       }  
       return instance;  
       }  
   }
   ```

   线程安全的单例模式

   ```java
   public class Singleton {  
         private static Singleton instance;  
         private Singleton (){}   
         public static synchronized Singleton getInstance(){    //对获取实例的方法进行同步
           if (instance == null)     
             instance = new Singleton(); 
           return instance;
         }
     }  
   ```

   

### **2.工厂模式**

**简介：**

定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。

**实现：**

![](http://www.runoob.com/wp-content/uploads/2014/08/factory_pattern_uml_diagram.jpg)

1. 创建一个接口:

```java
public interface Shape {
   void draw();
}
```

2. 创建实现接口的实体类。

```java
//Rectangle.java
public class Rectangle implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
//Square.java
public class Square implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
//Circle.java
public class Circle implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Circle::draw() method.");
   }
}
```

3. 创建一个工厂，生成基于给定信息的实体类的对象。

```java
//ShapeFactory.java
public class ShapeFactory {
    
   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
}
```

4. 使用该工厂，通过传递类型信息来获取实体类的对象。

```java
//FactoryPatternDemo.java
public class FactoryPatternDemo {
 
   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();
      //获取 Circle 的对象，并调用它的 draw 方法
      Shape shape1 = shapeFactory.getShape("CIRCLE");
      //调用 Circle 的 draw 方法
      shape1.draw();
      //获取 Rectangle 的对象，并调用它的 draw 方法
      Shape shape2 = shapeFactory.getShape("RECTANGLE");
      //调用 Rectangle 的 draw 方法
      shape2.draw();
      //获取 Square 的对象，并调用它的 draw 方法
      Shape shape3 = shapeFactory.getShape("SQUARE");
      //调用 Square 的 draw 方法
      shape3.draw();
   }
}
```

### **3.代理模式**

**简介：**

在代理模式（Proxy Pattern）中，一个类代表另一个类的功能。这种类型的设计模式属于结构型模式。

意图：为其他对象提供一种代理以控制对这个对象的访问。

**实现：**

在代理类中创建原类的对象，并调用其方法。

![](http://www.runoob.com/wp-content/uploads/2014/08/proxy_pattern_uml_diagram.jpg)

1. 创建一个接口。

   ```java
   //Image.java
   public interface Image {
      void display();
   }
   ```

2. 创建实现接口的实体类。

   ```java
   //被代理的类RealImage.java
   public class RealImage implements Image {
    
      private String fileName;
    
      public RealImage(String fileName){
         this.fileName = fileName;
         loadFromDisk(fileName);
      }
    
      @Override
      public void display() {
         System.out.println("Displaying " + fileName);
      }
    
      private void loadFromDisk(String fileName){
         System.out.println("Loading " + fileName);
      }
   }
   //代理类ProxyImage.java
   public class ProxyImage implements Image{
    
      private RealImage realImage;
      private String fileName;
    
      public ProxyImage(String fileName){
         this.fileName = fileName;
      }
    
      @Override
      public void display() {
         if(realImage == null){
            realImage = new RealImage(fileName);
         }
         realImage.display();
      }
   }
   ```

3. 当被请求时，使用 *ProxyImage* 来获取 *RealImage* 类的对象。

   ```java
   ProxyPatternDemo.java
   public class ProxyPatternDemo {
      
      public static void main(String[] args) {
         Image image = new ProxyImage("test_10mb.jpg");
    
         // 图像将从磁盘加载   //？不是很懂
         image.display(); 
         System.out.println("");
         // 图像不需要从磁盘加载
         image.display();  
      }
   }
   ```

### **4.适配器模式**

**简介：**

​	适配器模式（Adapter Pattern）是作为两个不兼容的接口之间的桥梁。这种类型的设计模式属于结构型模式，它结合了两个独立接口的功能。这种模式涉及到一个单一的类，该类负责加入独立的或不兼容的接口功能。

意图：将一个类的接口转换成客户希望的另外一个接口。

**实现：**

Ps2转USB的适配器：我们想让Ps2接口的设备具备USB的功能，于是写一个适配器实现Ps2的接口并集成USB的功能，就可以让Ps2接口的设备跟USB一样，具备USB的功能。

```java
//ps2接口：Ps2
1 public interface Ps2 {
2     void isPs2();
3 }

//Ps2接口实现类：Ps2er
1 public class Ps2er implements Ps2 {
2 
3     @Override
4     public void isPs2() {
5         System.out.println("Ps2口");
6     }
7 
8 }

//USB接口：Usb
1 public interface Usb {
2     void isUsb();
3 }

//USB接口实现类：Usber
1 public class Usber implements Usb {
2 
3     @Override
4     public void isUsb() {
5         System.out.println("USB口");
6     }
7 
8 }

//适配器：Adapter
 1 public class Adapter implements Ps2 {
 2     
 3     private Usb usb;
 4     public Adapter(Usb usb){
 5         this.usb = usb;
 6     }
 7     @Override
 8     public void isPs2() {
 9         usb.isUsb();
10     }
11 
12 }
```

### **5.策略模式**

**简介：**

将算法封装成一个个的类（即策略），在主程序中，根据传入的策略对象不同，调用不同的算法。

**实现：**

![](http://www.runoob.com/wp-content/uploads/2014/08/strategy_pattern_uml_diagram.jpg)

1. 创建一个接口。

   ```java
   //Strategy.java
   public interface Strategy {
      public int doOperation(int num1, int num2);
   }
   ```

2. 创建实现接口的实体类。

   ```java
   //OperationAdd.java
   public class OperationAdd implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 + num2;
      }
   }
   //OperationSubstract.java
   public class OperationSubstract implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 - num2;
      }
   }
   //OperationMultiply.java
   public class OperationMultiply implements Strategy{
      @Override
      public int doOperation(int num1, int num2) {
         return num1 * num2;
      }
   }
   ```

3. 创建 *Context* 类。

   ```java
   //Context.java
   public class Context {
      private Strategy strategy;
    
      public Context(Strategy strategy){
         this.strategy = strategy;
      }
    
      public int executeStrategy(int num1, int num2){
         return strategy.doOperation(num1, num2);
      }
   }
   ```

4. 使用 *Context* 来查看当它改变策略 *Strategy* 时的行为变化。

   ```java
   //StrategyPatternDemo.java
   public class StrategyPatternDemo {
      public static void main(String[] args) {
         Context context = new Context(new OperationAdd());    
         System.out.println("10 + 5 = " + context.executeStrategy(10, 5));
    
         context = new Context(new OperationSubstract());      
         System.out.println("10 - 5 = " + context.executeStrategy(10, 5));
    
         context = new Context(new OperationMultiply());    
         System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
      }
   }
   ```

