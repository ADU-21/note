# Java 8 新特性

[官方文档](http://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)

## Lambda 表达式 (Lambda Expression )

Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中）。

Like this: ```input -> body ```

More like this:

```
(arg1, arg2...) -> { body }

(type1 arg1, type2 arg2...) -> { body }
```

For example：

```
() -> 42

a -> return a*a

(int a, int b) -> {  return a + b; }

() -> System.out.println("Hello World");

(String s) -> { System.out.println(s); }
```

Notice:

lambda 表达式只能引用 final 或 final 局部变量，这就是说不能在 lambda 内部修改定义在域外的变量，否则会编译错误。

## 方法引用 (Method References)

适用场景：当一个Lambda表达式调用了一个已存在的方法

- 构造器引用：它的语法是Class::new，或者更一般的Class< T >::new
- 静态方法引用：它的语法是Class::static_method
- 特定类的任意对象的方法引用：它的语法是Class::method
- 特定对象的方法引用：它的语法是instance::method

For example:

```
// 使用Lambda表达式
(a, b) -> {return a * b};

// 使用Lambda表达式，调用已存在的方法
(a, b) -> Calculator.calculate(a, b);

// 使用方法引用
Calculator::calculate;
```

[一个完整的例子](https://my.oschina.net/luoyezhuifeng/blog/801343) ([原文链接](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html))

不适用：当需要往引用的方法传其它参数的时候，如下示例：

```
IsReferable demo = () -> ReferenceDemo.commonMethod("Argument in method.");
```

In other words:

>*Instead of using*
>**AN ANONYMOUS CLASS**
>*you can use*
>**A LAMBDA EXPRESSION**
>*And if this just calls one method, you can use*
>**A METHOD REFERENCE**

## 函数式接口（Functional Interface）

java.util.function

For example: java.util.function.Predicate 

Predicate <T> 接口是一个函数式接口，它接受一个输入参数 T，返回一个布尔值结果。

```
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;
 
public class Java8Tester {
   public static void main(String args[]){
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        
      // Predicate<Integer> predicate = n -> true
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // n 如果存在则 test 方法返回 true
        
      System.out.println("输出所有数据:");
        
      // 传递参数 n
      eval(list, n->true);
        
      // Predicate<Integer> predicate1 = n -> n%2 == 0
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // 如果 n%2 为 0 test 方法返回 true
        
      System.out.println("输出所有偶数:");
      eval(list, n-> n%2 == 0 );
        
      // Predicate<Integer> predicate2 = n -> n > 3
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // 如果 n 大于 3 test 方法返回 true
        
      System.out.println("输出大于 3 的所有数字:");
      eval(list, n-> n > 3 );
   }
    
   public static void eval(List<Integer> list, Predicate<Integer> predicate) {
      for(Integer n: list) {
        
         if(predicate.test(n)) {
            System.out.println(n + " ");
         }
      }
   }
}
```

## 默认方法

简单说，默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。

```
default
```

## Stream API

Stream 使用一种类似用 SQL 语句从数据库查询数据的直观方式来提供一种对 Java 集合运算和表达的高阶抽象。

- Stream（流）是一个来自数据源的元素队列并支持聚合操作
    - 元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
    - **数据源** 流的来源。 可以是集合，数组，I/O channel， 产生器generator 等。
    - **聚合操作** 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。
- 和以前的Collection操作不同， Stream操作还有两个基础的特征：
    - **Pipelining**: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
    - **内部迭代**： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式， 通过访问者模式(Visitor)实现。

### stream() − 为集合创建串行流

- 参照[菜鸟教程](http://www.runoob.com/java/java8-streams.html)

### parallelStream() − 为集合创建并行流。

**存在争议，好好看看**

## 新工具

新的编译工具，如：Nashorn引擎 jjs、 类依赖分析器jdeps。

## Date Time API （了解）

Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。
在旧版的 Java 中，日期时间 API 存在诸多问题，其中有：

- 非线程安全 − java.util.Date 是非线程安全的，所有的日期类都是可变的，这是Java日期类最大的问题之一。
- 设计很差 − Java的日期/时间类的定义并不一致，在java.util和java.sql的包中都有日期类，此外用于格式化和解析的类在java.text包中定义。java.util.Date同时包含日期和时间，而java.sql.Date仅包含日期，将其纳入java.sql包并不合理。另外这两个类都有相同的名字，这本身就是一个非常糟糕的设计。
- 时区处理麻烦 − 日期类并不提供国际化，没有时区支持，因此Java引入了java.util.Calendar和java.util.TimeZone类，但他们同样存在上述所有的问题。

Java 8 在 java.time 包下提供了很多新的 API。以下为两个比较重要的 API：

- Local(本地) − 简化了日期时间的处理，没有时区的问题。
- Zoned(时区) − 通过制定的时区处理日期时间。

新的java.time包涵盖了所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作。

## Optional 类 

- Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。
- Optional 是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。
- Optional 类的引入很好的解决空指针异常。

## Nashorn JavaScript 引擎

Nashorn 一个 javascript 引擎。
从JDK 1.8开始，Nashorn取代Rhino(JDK 1.6, JDK1.7)成为Java的嵌入式JavaScript引擎。Nashorn完全支持ECMAScript 5.1规范以及一些扩展。它使用基于JSR 292的新语言特性，其中包含在JDK 7中引入的 invokedynamic，将JavaScript编译成Java字节码。

## Base64 (看看)

在Java 8中，Base64编码已经成为Java类库的标准。
Java 8 内置了 Base64 编码的编码器和解码器。
Base64工具类提供了一套静态方法获取下面三种BASE64编解码器：

- 基本：输出被映射到一组字符A-Za-z0-9+/，编码不添加任何行标，输出的解码仅支持A-Za-z0-9+/。
- URL：输出映射到一组字符A-Za-z0-9+_，输出是URL和文件。
- MIME：输出隐射到MIME友好格式。输出每行不超过76字符，并且使用'\r'并跟随'\n'作为分割。编码输出最后没有行分割。






> * Refer:
> * <http://www.runoob.com/java/java8-new-features.html>
> * [Java 8 Method Reference: How to Use it](https://www.codementor.io/eh3rrera/using-java-8-method-reference-du10866vx)