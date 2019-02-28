

http://how2j.cn/k/j2se-interview/j2se-interview-java/624.html#

https://blog.csdn.net/hope900/article/details/78647466

## 1.多线程

###（1）voliatile和synchonized有什么区别？

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

###（2）synchonized和jdk提供的Lock包有什么区别？

​	synchronized是基于jvm底层实现的数据同步，lock是基于Java编写，主要通过硬件依赖CPU指令实现数据同步。与synchronized不同的是lock是纯java手写的，与底层的JVM无关。在java.util.concurrent.locks包中有很多Lock的实现类

​	**1.synchronized**

　　优点：实现简单，语义清晰，便于JVM堆栈跟踪，加锁解锁过程由JVM自动控制，提供了多种优化方案，使用更广泛

　　缺点：悲观的排他锁，不能进行高级功能

　　**2.lock**

　　优点：可定时的、可轮询的与可中断的锁获取操作，提供了读写锁、公平锁和非公平锁　　

　　缺点：需手动释放锁unlock，不适合JVM进行堆栈跟踪

　　**3.相同点**　

　　都是可重入锁

###（3）线程池

​	为了防止线程频繁的创建撤销所带来的内存消耗。线程池中有一些核心线程永远不会被清理，当有一个任务需要执行时，首先判断线程池中的核心线程是否都在执行，如果没有，则创建一个工作线程执行任务，否则将任务放入等待队列，如果队列已满，则判断线程池（核心线程+非核心线程）是否已经满了，如果未满，则创建非核心线程执行队列中的任务，如果满了就调用拒绝策略来管理任务。

https://blog.csdn.net/weixin_40271838/article/details/79998327

## 2.Java集合

###（1）**HashMap的原理是什么？**

https://www.cnblogs.com/chengxiao/p/6059914.html

https://segmentfault.com/a/1190000012926722

​        HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap中的链表出现越少，性能才会越好。

**动态扩容**：当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置。

**多线程并发问题**：

1. 现在假如A线程和B线程同时对同一个数组位置调用addEntry，两个线程会同时得到现在的头结点，然后A写入新的头结点之后，B也写入新的头结点，那B的写入操作就会覆盖A的写入操作造成A的写入操作丢失。
2. 当多个线程同时操作同一个数组位置的时候，也都会先取得现在状态下该位置存储的头结点，然后各自去进行计算操作，之后再把结果写会到该数组位置去，其实写回的时候可能其他的线程已经就把这个位置给修改过了，就会覆盖其他线程的修改。
3. 当多个线程同时检测到总数量超过门限值的时候就会同时调用resize操作，各自生成新的数组并rehash后赋给该map底层的数组table，结果最终只有最后一个线程生成的新数组被赋给table变量，其他线程的均会丢失。而且当某些线程已经完成赋值而其他线程刚开始的时候，就会用已经被赋值的table作为原始数组，这样也会有问题。

**线程安全的Map**：ConcurrentHashMap分段锁：容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了，这样便可以有效地提高并发效率。

###（2）**HashMap和hashtable区别？**

 1、HashMap是非线程安全的，HashTable是线程安全的。 
 2、HashMap的键和值都允许有null值存在，而HashTable则不行。 
 3、因为线程安全的问题，HashMap效率比HashTable的要高。 
 4、Hashtable是同步的，而HashMap不是。因此，HashMap更适合于单线程环境，而Hashtable适合于多线程环境。 

​        一般现在不建议用HashTable, ①是HashTable是遗留类，内部实现很多没优化和冗余。②即使在多线程环境下，现在也有同步的ConcurrentHashMap替代，没有必要因为是多线程而用HashTable。     

## 设计模式

http://www.runoob.com/design-pattern/singleton-pattern.html

https://blog.csdn.net/qq_33326449/article/details/78946364

共23种，分为三类：创建型（单例模式、工厂模式……）、结构型模式（代理模式、适配器模式……）、行为型模式（策略模式、观察者模式……）

###**1.单例模式**

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

   

###**2.工厂模式**

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

###**3.代理模式**

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

###**4.适配器模式**

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

###**5.策略模式**

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

##Spring

- Spring的AOP、IOC作用是什么？如何实现的。
      Spring是一个java web的框架，面试官特别喜欢问。但是问spring基本上也只围绕着这几个点，第一个是AOP、IOC的作用是什么，这个问题查一下就知道了。第二个是AOP、IOC是通过什么实现的？AOP是通过代理模式来实现的，IOC是通过单例模式+工厂模式来实现的。问得比较多的是AOP的实现方式，你如果回答代理模式一般就够了。作为拓展，你可以回答里面用到了动态代理，动态代理有两种方式，一种是jdk提供的，一种是cglib。。。然后你和面试官比较一下两种动态代理的区别，我觉得也是会有加分的。