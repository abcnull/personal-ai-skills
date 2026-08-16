---
name: knl-lan-java
description: 当用户想要了解 java 语法，这篇文档可能可以提供帮助
disable-model-invocation: true
---

# knl-lan-java

此文主要讲解了 java 语法的一部分容易遗忘的知识内容

## 基础

javac 用于将 java 源文件编译为 class 字节码文件
java 用于运行 class 字节码文件

```shell
$ javac HelloWorld.java
$ java HelloWorld
Hello World
```

判断对象是否是特定类型

```java
if (obj instanceof User) {
    // 对象是 Type 类型
}
```

## main

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

## 类型和变量

```java
int d = 3, e = 4, f = 5;
char x = 'x';
Character ch = new Character('a');
```

关于包装类型：装箱是指将基本数据类型转换为对应的包装类型，拆箱是指将包装类型转换为基本数据类型。拆箱是指自动将包装类型转换为基本数据类型。

## String 字符串

String

```java
// String
String str = "Hello World";
String str2 = new String("Hello World");
```

和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。StringBuilder 和 StringBuffer 之间的最大不同在于 StringBuilder 的方法不是线程安全的

- StringBuilder：可变，更快，非线程安全
- StringBuffer：可变，更慢，线程安全

```java
// 多数情况下建议使用 StringBuilder 类
StringBuilder sb = new StringBuilder("Hello World");
sb.append("!");
System.out.println(sb);
```

## 数组

```java
// 初始化数组
int[] arr = new int[5];
arr[0] = 1;
arr[1] = 2;

// 初始化数组
double[] myList = {1.9, 2.9, 3.4, 3.5};

// 多维数组
String[][] s = new String[2][];
s[0] = new String[2];
s[1] = new String[3];
s[0][0] = new String("Good");
s[1][0] = new String("to");
s[1][1] = new String("you");

// Arrays
Arrays.sort(arr); // 排序 void
Arrays.toString(arr); // 转换为字符串
Arrays.equals(arr, arr2); // 比较数组是否相等
```

## 方法

- 重写 @Override：java 方法支持重写父类方法
- 重载 @Overload：java 方法支持重载同一个类中的方法，方法名相同，参数不同
- 构造方法 Constructor：支持重载，没有返回类型，自动调用

## Scanner 获取用户输入

```java
import java.util.Scanner;
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据

        // next方式接收字符串
        System.out.println("next方式接收：");
        // 判断是否还有输入
        if (scan.hasNext()) {
            String str1 = scan.next();
            System.out.println("输入的数据为：" + str1);
        }
        scan.close();
    }
}
```

## 异常

- 受检查异常：在编译时强制要求程序员处理，这些异常在编译时不能被简单地忽略。比如打开一个不存在的文件
- 运行时异常：继承自 RuntimeException 类，这些异常在编译时不强制要求处理，通常是由程序中的错误引起的，如运行时候出现了 NPE
- 错误：错误不是异常，比如栈溢出时候，这个错误发生了

try catch

```java
// 多重捕获
try {
} catch (Exception e) {
} catch (SQLException | IOException e) {
    // 多异常合并捕获
} finally {
    // 无论是否抛出异常，都会执行的代码
}

// try-with-resources，自动关闭资源，避免泄露。前提是资源已经实现了 Closeable 接口
try (InputStream in = new FileInputStream("test.txt")) {
    // 读取文件内容
} catch (IOException e) {
    // 处理异常
}
```

## 抽象类 abstract

抽象类不能实例化对象，所以抽象类必须被继承，才能被使用。抽象类中可以包含抽象方法和非抽象方法。但是继承了抽象类的类，必须实现抽象类中的抽象方法，否则自己本身也是抽象类。一个类可以继承一个类。

## 接口 interface

类是继承，而接口是实现。接口中只能包含抽象方法，不能包含非抽象方法。无构造方法，不能包含成员变量，除了 static 和 final 变量。一个类可以实现多个接口。

## 枚举

```java
// 枚举
enum Color
{
    RED, GREEN, BLUE;
}

// 遍历
for (Color myVar : Color.values()) {
    System.out.println(myVar);
}
```

## 反射

```java
// 获取 Class
// 1. 从对象中获取 Class
Class<?> clazz = Person.class;
// 2. 从类名中获取 Class
Class<?> clazz = Class.forName("java.xxx.Person");

// 反射动态创建对象
Class<?> clazz = Class.forName("java.xxx.Person");
Object obj = clazz.getDeclaredConstructor().newInstance();

// 通过反射访问和修改类的字段
Field field = clazz.getDeclaredField("name");
field.setAccessible(true); // 如果字段是私有的，需要设置为可访问
Object value = field.get(personInstance); // 获取字段值
field.set(personInstance, "New Name"); // 设置字段值

// 调用方法
// 调用无参数的方法
Method method = clazz.getMethod("sayHello");
method.invoke(personInstance);
// 调用有参数的方法
Method methodWithArgs = clazz.getMethod("greet", String.class);
methodWithArgs.invoke(personInstance, "World");

// 用反射获取和调用构造函数
Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
Object obj = constructor.newInstance("John", 30);

// 获取接口和父类
// 获取接口
Class<?>[] interfaces = clazz.getInterfaces();
for (Class<?> i : interfaces) {
    System.out.println("Interface: " + i.getName());
}
// 获取父类
Class<?> superClass = clazz.getSuperclass();
System.out.println("Superclass: " + superClass.getName());
```

## 集合类

- ArrayList：可变大小的数组，支持随机访问和遍历
- LinkedList：双向链表，支持随机访问和遍历
- HashSet：无序的哈希表，不允许重复元素，支持快速查找和插入
- TreeSet：有序的哈希表，底层基于红黑树实现，不允许重复元素，支持快速查找和插入
- HashMap：键值对的哈希表，不允许重复键，支持快速查找和插入
- TreeMap：有序的键的哈希表，底层基于红黑树实现，不允许重复键，支持快速查找和插入

```java
// list
List<String> list=new ArrayList<String>();
list.add("Hello");

// map
Map<String, String> map = new HashMap<String, String>();
map.put("1", "value1");
```

集合类

```java
// list 排序
Collections.sort(list);
```

## Object

如果没有明确继承一个父类，那么它就会自动继承 java.lang.Object

## 泛型

```java
// T
public class Box<T> {
  private T t;
  public void add(T t) {
    this.t = t;
  }
  public T get() {
    return t;
  }
}

// ?
public static void getData(List<?> data) {
    System.out.println("data :" + data.get(0));
}
```

## 修饰符

- public : 对所有类可见。类、接口、变量、方法
- protected : 对同一包内的类和所有子类可见。变量、方法。 注意：不能修饰类（外部类）。
- default (即默认，什么也不写）: 在同一包内可见，不使用任何修饰符。类、接口、变量、方法。
- private : 在同一类内可见。变量、方法。 注意：不能修饰类（外部类）

## 条件

```java
// swich case。若 case 中不加 break，则会继续执行下一个 case
switch (a) {
    case 1:
        System.out.println("a is 1");
        break;
    case 2:
        System.out.println("a is 2");
        break;
    default:
        System.out.println("a is not 1 or 2");
}
```

## 循环

```java
// map 循环遍历
for (Map.Entry<String, String> entry : map.entrySet()) {
    System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
}
```

## 多线程

方式：

- 实现 Runnable 接口
- 继承 Thread 类
- 通过 Callable 和 Future 创建线程

```java
// Runnable
class RunnableDemo implements Runnable {
   private Thread t;
   private String threadName;
   RunnableDemo(String name) {
   }
   // 核心：多线程运行逻辑
   public void run() {
   }
   public void start() {
        if (t == null) {
            t = new Thread (this, threadName);
            t.start ();
      }
   }
}
public class TestThread {
   public static void main(String args[]) {
      RunnableDemo R1 = new RunnableDemo( "Thread-1");
      R1.start();
      RunnableDemo R2 = new RunnableDemo( "Thread-2");
      R2.start();
   }
}
```

```java
// 继承 Thread
class ThreadDemo extends Thread {
   private Thread t;
   private String threadName;
   ThreadDemo(String name) {
   }
   // 核心：多线程运行逻辑
   public void run() {
   }
   public void start () {
      if (t == null) {
         t = new Thread (this, threadName);
         t.start ();
      }
   }
}
public class TestThread {
   public static void main(String args[]) {
      ThreadDemo T1 = new ThreadDemo( "Thread-1");
      T1.start();
      ThreadDemo T2 = new ThreadDemo( "Thread-2");
      T2.start();
   }
}
```

ExecutorService 和 CompletableFuture 创建线程

```java
// ExecutorService 示例：使用线程池执行任务
ExecutorService executor = Executors.newFixedThreadPool(2);
executor.submit(() -> {
    System.out.println("任务1执行中: " + Thread.currentThread().getName());
});
executor.submit(() -> {
    System.out.println("任务2执行中: " + Thread.currentThread().getName());
});
executor.shutdown();

// CompletableFuture 示例：异步执行任务
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "异步任务结果";
});
String result = future.get(); // 获取结果
System.out.println(result);
```

## lambda 表达式
```java
// 无参数
Runnable r = () -> System.out.println("Hello");
r.run();

// 单参数
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(item -> System.out.println(item));

// 多参数
Comparator<String> comp = (a, b) -> a.length() - b.length();

// 与 Stream 结合
list.stream()
    .filter(s -> s.startsWith("a"))
    .map(s -> s.toUpperCase())
    .forEach(System.out::println);
```

## java 9 新增特性
```java
// 集合工厂方法，创建不可变集合
List<String> list = List.of("a", "b", "c");
Set<Integer> set = Set.of(1, 2, 3);
Map<String, Integer> map = Map.of("key1", 1, "key2", 2);
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
```

