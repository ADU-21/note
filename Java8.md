# Java 8 新特性

[官方文档](http://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)

# [Java Programming Language](http://docs.oracle.com/javase/8/docs/technotes/guides/language/enhancements.html#javase8)

## Lambda 表达式 (Lambda Expression )

Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中）。

形如: ```input -> body ```

在代码中:

```
(arg1, arg2...) -> { body }

(type1 arg1, type2 arg2...) -> { body }
```

示例如下：

```
() -> 42

a -> return a*a

(int a, int b) -> {  return a + b; }

() -> System.out.println("Hello World");

(String s) -> { System.out.println(s); }
```

注意:

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

换句话说，

>*Instead of using*
>**AN ANONYMOUS CLASS**
>*you can use*
>**A LAMBDA EXPRESSION**
>*And if this just calls one method, you can use*
>**A METHOD REFERENCE**

## 函数式接口（Functional Interface）

FI的定义其实很简单：任何接口，如果只包含**唯一**一个抽象方法，那么它就是一个FI。为了让编译器帮助我们确保一个接口满足FI的要求（也就是说有且仅有一个抽象方法，这种类型的接口也称为SAM接口，即Single Abstract Method interfaces）

Java8提供了@FunctionalInterface注解，Java 不会强制要求你使用@FunctionalInterface注解来标记你的接口是函数式接口， 然而，作为API作者， 你可能倾向使用@FunctionalInterface指明特定的接口为函数式接口， 这只是一个设计上的考虑， 可以让用户很明显的知道一个接口是函数式接口。除此之外，FI 还可以很容易被 Lambda 实现。

具体例子参见： [Java 8 函数式接口](http://www.runoob.com/java/java8-functional-interfaces.html)

## 默认方法（Default Methods）

>[Default methods](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) enable new functionality to be added to the interfaces of libraries and ensure binary compatibility with code written for older versions of those interfaces.

>默认方法允许您添加新的功能到现有库的接口中，并能确保与采用旧版本接口编写的代码的二进制兼容性。

当类实现接口的时候，类要实现接口中所有的方法。否则，类必须声明为抽象的类。默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。

默认方法是在接口中的方法签名前加上了 `default` 关键字的实现方法。

For example:

```
interface InterfaceA {
    default void foo() {
        System.out.println("InterfaceA foo");
    }
}

class ClassA implements InterfaceA {
}

public class Test {
    public static void main(String[] args) {
        new ClassA().foo(); // 打印：“InterfaceA foo”
    }
}
```

`ClassA` 类并没有实现 `InterfaceA` 接口中的 `foo` 方法，`InterfaceA` 接口中提供了 `foo` 方法的默认实现，因此可以直接调用 `ClassA` 类的 `foo` 方法。

和其它方法一样，接口默认方法也可以被继承，这样一来接口就面临了多继承问题，如下图所示，Interface C 继承了 Interface A 和 B，如果 A 和 B 中存在相同的签名方法，C 就要处理继承冲突的问题。

```
+---------------+         +------------+
|  Interface A  |         |Interface B |
+-----------^---+         +---^--------+
            |                 |         
            |                 |         
            |                 |         
            +-+------------+--+         
              | Interface C|            
              +------------+
```

接口多继承冲突分为以下三种情况：

- A,B拥有相同签名的默认方法如果接口C没有override这个方法， 则编译出错。这种情况需要在子接口`C`中覆盖override这个方法，并可以使用 `InterfaceName.super.methodName();` 的方式手动调用需要的接口默认方法。（注意方法签名不包括方法的返回值， 也就是仅仅返回值不同的两个方法的签名也是相同的）
- `A`和`B`的默认方法（传入参数）不同， C隐式继承了两个默认方法，不会有冲突。
- 但是有的情况下即使是不同签名的方法也是很难分辨的，比如传入类型为```short``` 和 ```int```，Java会选择最适合的方法， 请参看 [Java规范 15.12.2.5](http://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.12.2.5)

更多多继承问题，参见： [Java 8 默认方法和多继承](http://colobu.com/2014/11/04/Java-8-default-method-and-multiple-inheritance/)

## 重复注解（Repeating Annotations）

自从Java 5中引入[注解](http://www.javacodegeeks.com/2012/08/java-annotations-explored-explained.html)以来，这个特性开始变得非常流行，并在各个框架和项目中被广泛使用。不过，注解有一个很大的限制是：在同一个地方不能多次使用同一个注解。Java 8打破了这个限制，引入了重复注解的概念，允许在同一个地方多次使用同一个注解。

具体参见：[这里](https://my.oschina.net/benhaile/blog/180932)

## 类型注解（Type Annotations）

在java 8之前，注解只能是在声明的地方所使用，比如类，方法，属性；java 8里面，注解可以应用在任何地方，比如：

```
// 创建类实例
new @Interned MyObject();

// 类型映射       
myString = (@NonNull String) str;

// implements 语句中        
class UnmodifiableList<T> implements @Readonly List<@Readonly T> { ... }

// throw exception声明        
void monitorTemperature() throws @Critical TemperatureException { ... }
```

需要注意的是，类型注解只是语法而不是语义，并不会影响java的编译时间，加载时间，以及运行时间，也就是说，编译成class文件的时候并不包含类型注解。

类型注解的作用，参见：[Java 8新特性探究（四）类型注解 复杂还是便捷](https://my.oschina.net/benhaile/blog/179642)

## 加强泛型的目标类型推断

java8里面泛型的目标类型推断主要2个：

1.支持通过方法上下文推断泛型目标类型

2.支持在方法调用链路当中，泛型类型推断传递到最后一个方法

举个例子：

下面的例子在java 7无法正确编译（但现在在java8里面可以编译，因为根据方法参数来自动推断泛型的类型）：

```
List<String> list = new ArrayList<>();
list.add("A");// 由于addAll期望获得Collection<? extends String>类型的参数，因此下面的语句无法通过
list.addAll(new ArrayList<>());
```

具体参见： [Java 8新特性探究（六）泛型的目标类型推断](https://my.oschina.net/benhaile/blog/184390)

# [Collections](http://docs.oracle.com/javase/8/docs/technotes/guides/collections/changes8.html)

Classes in the new `java.util.stream` package provide a Stream API to support functional-style operations on streams of elements. The Stream API is integrated into the Collections API, which enables bulk operations on collections, such as sequential or parallel map-reduce transformations.

## Stream API

### 什么是 Stream

Stream 不是集合元素，它不是数据结构并不保存数据，它是有关算法和计算的，它更像一个高级版本的迭代器(Iterator)，单向，不可往复，数据只能遍历一次，遍历过一次后即用尽了，就好比流水从面前流过，一去不复返。而和迭代器又不同的是，Stream 可以并行化操作，且数据源本身可以是无限的。

## Stream 的构成

当我们使用一个流的时候，通常包括三个基本步骤：

获取一个数据源（source）→ 数据转换→执行操作获取想要的结果。

#### Stream 的构造与转换

##### 构造流的几种常见方法

```
// 1. Individual values
Stream stream = Stream.of("a", "b", "c");
// 2. Arrays
String [] strArray = new String[] {"a", "b", "c"};
stream = Stream.of(strArray);
stream = Arrays.stream(strArray);
// 3. Collections
List<String> list = Arrays.asList(strArray);
stream = list.stream();
```

需要注意的是，对于基本数值型，目前有三种对应的包装类型 Stream：IntStream、LongStream、DoubleStream。当然我们也可以用 Stream<Integer>、Stream<Long> >、Stream<Double>，但是 boxing 和 unboxing 会很耗时，所以特别为这三种基本数值型提供了对应的 Stream。

Java 8 中还没有提供其它数值型 Stream，因为这将导致扩增的内容较多。而常规的数值型聚合运算可以通过上面三种 Stream 进行。

##### 数值流的构造

```
IntStream.of(new int[]{1, 2, 3}).forEach(System.out::println);
IntStream.range(1, 3).forEach(System.out::println);
IntStream.rangeClosed(1, 3).forEach(System.out::println);
```

##### 流转换为其它数据结构

```
// 1. Array
String[] strArray1 = stream.toArray(String[]::new);
// 2. Collection
List<String> list1 = stream.collect(Collectors.toList());
List<String> list2 = stream.collect(Collectors.toCollection(ArrayList::new));
Set set1 = stream.collect(Collectors.toSet());
Stack stack1 = stream.collect(Collectors.toCollection(Stack::new));
// 3. String
String str = stream.collect(Collectors.joining()).toString();
```

流的操作类型分为三种：

- **Intermediate**：一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。

  包括：map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered


- **Terminal**：一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且会生成一个结果，或者一个 side effect。

  包括：forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

- 还有一种操作被称为 **short-circuiting**。用以指：

  - 对于一个 intermediate 操作，如果它接受的是一个无限大（infinite/unbounded）的 Stream，但返回一个有限的新 Stream。
  - 对于一个 terminal 操作，如果它接受的是一个无限大的 Stream，但能在有限的时间计算出结果。

  包括：anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit

### 注意

> * Stream 不是数据结构
> * 所有 Stream 的操作必须以 lambda 表达式为参数
> * 不支持索引访问，你可以请求第一个元素，但无法请求第二个，第三个，或最后一个。
> * 很多 Stream 操作是向后延迟的，一直到它弄清楚了最后需要多少数据才会开始。
> * 可以是无限的，limit(n) 和 findFirst() 这类的 short-circuiting 操作可以对无限的 Stream 进行运算并很快完成。
> * 调用`paralle()`构造并行stream

Some examples: [Java 8 Stream Tutorial Examples](http://winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples/)

**Think Twice Before Using Java 8 Parallel Streams**

小心使用 Stream 的并行功能，Parallel Stream 采用的是 jdk7 中引入的 ForkJoin框架， 使用**分治法(Divide-and-Conquer Algorithm)**管理线程池，这种方案在进程阻塞的情况下会导致事倍功半的效果，相比串行方案还会浪费一些计算资源。具体可参见这篇文章：[深入浅出parallelStream](http://www.jianshu.com/p/bd825cb89e00)

# [Date-Time Package](http://docs.oracle.com/javase/8/docs/technotes/guides/datetime/index.html) 

Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。
在旧版的 Java 中，日期时间 API 存在诸多问题，第三方包又些许存在一些兼容性问题，Java 8 在 java.time 包下提供了很多新的 API，包含以下相应的包：

- java.time包：这是新的Java日期/时间API的基础包，所有的主要基础类都是这个包的一部分，如：LocalDate, LocalTime, LocalDateTime, Instant, Period, Duration等等。所有这些类都是不可变的和线程安全的，在绝大多数情况下，这些类能够有效地处理一些公共的需求。
- java.time.chrono包：这个包为非ISO的日历系统定义了一些泛化的API，我们可以扩展AbstractChronology类来创建自己的日历系统。
- java.time.format包：这个包包含能够格式化和解析日期时间对象的类，在绝大多数情况下，我们不应该直接使用它们，因为java.time包中相应的类已经提供了格式化和解析的方法。
- java.time.temporal包：这个包包含一些时态对象，我们可以用其找出关于日期/时间对象的某个特定日期或时间，比如说，可以找到某月的第一天或最后一天。你可以非常容易地认出这些方法，因为它们都具有“withXXX”的格式。
- java.time.zone包：这个包包含支持不同时区以及相关规则的类。

# Optional 类 — 优雅的空指针处理

身为一名Java程序员，大家可能都有这样的经历：调用一个方法得到了返回值却不能直接将返回值作为参数去调用别的方法。我们首先要判断这个返回值是否为null，只有在非空的前提下才能将其作为其他方法的参数。这正是一些类似[Guava](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/base/Optional.html)的外部API试图解决的问题。一些JVM编程语言比如Scala、Ceylon等已经将对在核心API中解决了这个问题。新版本的Java，比如[Java 8](http://blog.sanaulla.info/tag/java-8/)引入了一个新的[Optional](http://download.java.net/jdk8/docs/api/java/util/Optional.html)类。Optional类的Javadoc描述如下：

>这是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

Optional 类提供了许多方法用于解决空指针的问题，下面列举几个个我认为最常用的方法，完整版可参见这篇文章：[Java 8 Optional类深度解析](http://www.importnew.com/6675.html)

- **ifPresent**

  如果Optional实例有值则为其调用consumer，否则不做处理，For example:

  ```
  //ifPresent方法接受lambda表达式作为参数。
  //lambda表达式对Optional的值调用consumer进行处理。
  name.ifPresent((value) -> {
    System.out.println("The length of the value is: " + value.length());
  });
  ```

- **orElse**

  如果Optional实例有值则将其返回，否则返回orElse方法传入的参数，For example：

  ```
  //如果值不为null，orElse方法返回Optional实例的值。
  //如果为null，返回传入的消息。
  //输出：There is no value present!
  System.out.println(empty.orElse("There is no value present!"));
  //输出：Sanaulla
  System.out.println(name.orElse("There is some value!"));
  ```

- **orElseGet**

  orElseGet与orElse方法类似，区别在于得到的默认值。orElse方法将传入的字符串作为默认值，orElseGet方法可以接受[Supplier接口](http://blog.sanaulla.info/2013/04/02/supplier-interface-in-java-util-function-package-in-java-8/)的实现用来生成默认值，For example：

  ```
  //orElseGet与orElse方法类似，区别在于orElse传入的是默认值，
  //orElseGet可以接受一个lambda表达式生成默认值。
  //输出：Default Value
  System.out.println(empty.orElseGet(() -> "Default Value"));
  //输出：Sanaulla
  System.out.println(name.orElseGet(() -> "Default Value"));
  ```

- **map**

  如果有值，则对其执行调用mapping函数得到返回值。如果返回值不为null，则创建包含mapping返回值的Optional作为map方法返回值，否则返回空Optional，For example：

  ```
  //map方法执行传入的lambda表达式参数对Optional实例的值进行修改。
  //为lambda表达式的返回值创建新的Optional实例作为map方法的返回值。
  Optional<String> upperName = name.map((value) -> value.toUpperCase());
  System.out.println(upperName.orElse("No value found"));
  ```

# [HotSpot JVM](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/)

JVM 为了方便内存回收将堆内存分为**新生代（Young generation）**， **老年代（Old generation）**， **持久代（ permanent generation ）**（如下图所示）具体参考：[成为JavaGC专家（1）—深入浅出Java垃圾回收机制](http://www.importnew.com/1993.html)

![](http://www.importnew.com/wp-content/uploads/2012/12/Figure-1-GC-Area-Data-Flow.png)

Java8 移除了 Permanent Generation，取而代之的是一个叫 MetaSpace（元空间） 的内存空间（如下图所示），MetaSpace 使用的是本地内存（Native heap），带来的最大好处是不会再有  [java.lang.OutOfMemoryError: PermGen](http://javaeesupportpatterns.blogspot.com/2011/02/outofmemoryerror-permgen-patterns-part1.html) 的问题，MetaSpace 回根据使用情况自动扩容，其理论最大值即为操作系统所能提供的内存最大值；另外 MetaSpace 的 GC 扫描只会发生在 MetaSpace 达到 MaxMetaspaceSize 设置的上限的时候，减少 GC 扫描次数在一定程度上优化了 JVM 的性能。具体参照：[Java 8: From PermGen to Metaspace](https://dzone.com/articles/java-8-permgen-metaspace)

![](http://static.oschina.net/uploads/space/2014/0329/195605_gspc_1028150.png)

# Base64 

在Java 8中，Base64编码已经成为Java类库的标准。
Java 8 内置了 Base64 编码的编码器和解码器。
Base64工具类提供了一套静态方法获取下面三种BASE64编解码器：

- 基本：输出被映射到一组字符A-Za-z0-9+/，编码不添加任何行标，输出的解码仅支持A-Za-z0-9+/。
- URL：输出映射到一组字符A-Za-z0-9+_，输出是URL和文件。
- MIME：输出隐射到MIME友好格式。输出每行不超过76字符，并且使用'\r'并跟随'\n'作为分割。编码输出最后没有行分割。


# [JavaFX](http://docs.oracle.com/javase/8/javase-clienttechnologies.htm)

JavaFX主要致力于富客户端开发，以弥补swing的缺陷，主要提供图形库与media库，支持audio,video,graphics,animation,3D等，同时采用现代化的css方式支持界面设计。

具体可参见：[Java 8新特性探究（十三）JavaFX 8新特性以及开发2048游戏](https://my.oschina.net/benhaile/blog/335109)

# 新工具

新的编译工具，如：Nashorn JavaScript引擎 jjs、 类依赖分析器jdeps。具体参见：[官方文档](http://docs.oracle.com/javase/8/docs/technotes/tools/enhancements-8.html)

## Nashorn JavaScript 引擎

Nashorn 一个 javascript 引擎。
从JDK 1.8开始，Nashorn取代Rhino(JDK 1.6, JDK1.7)成为Java的嵌入式JavaScript引擎。Nashorn完全支持ECMAScript 5.1规范以及一些扩展。它使用基于JSR 292的新语言特性，其中包含在JDK 7中引入的 invokedynamic，将JavaScript编译成Java字节码。

更多参见：[Java 8新特性探究（十二）Nashorn ：新犀牛](https://my.oschina.net/benhaile/blog/290276)

# [Security](http://docs.oracle.com/javase/8/docs/technotes/guides/security/enhancements-8.html)

众多安全性上的提提升，如更好的 TSL/SSL 支持，改进加密算法等等，具体参见[官方文档](https://docs.oracle.com/javase/8/docs/technotes/guides/security/enhancements-8.html)



> Refer:
>
> * [成熟的毛毛虫：Java8](https://my.oschina.net/benhaile/blog?sort=time&catalog=410404&p=2&temp=1503637545998)
> * <http://www.runoob.com/java/java8-new-features.html>
> * [深入浅出 Java 8 Lambda 表达式](http://blog.oneapm.com/apm-tech/226.html)
> * [Java 8 Method Reference: How to Use it](https://www.codementor.io/eh3rrera/using-java-8-method-reference-du10866vx)
> * <http://colobu.com/2014/10/28/secrets-of-java-8-functional-interface/>
> * <http://blog.csdn.net/zxhoo/article/details/38349011>
> * <http://ebnbin.com/2015/12/20/java-8-default-methods/>
> * <https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/>
> * <https://nkcoder.github.io/2016/01/24/java-8-stream-api/>
> * http://www.jianshu.com/p/5b800057f2d8
> * <http://www.importnew.com/14140.html>
> * <http://brianway.github.io/2017/03/29/javase-java8/>