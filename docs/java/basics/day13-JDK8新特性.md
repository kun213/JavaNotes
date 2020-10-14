## day13 JDK8新特性【Lambda、函数式接口、Stream流】

## 今日学习内容-2020.10.13

*   Properties集合
*   Lambda表达式
*   函数式接口
*   Stream流

## 一、Lambda表达式

### 1.1 理解函数式编程相对于面向对象的优点

在数学中，**函数**就是有输入量、输出量的一套计算方案，也就是“拿什么东西做什么事情”。相对而言，面向对象过分强调“必须通过对象的形式来做事情”，而函数式思想则尽量忽略面向对象的复杂语法——**强调做什么，而不是以什么形式做**。

### 1.2 掌握Lambda表达式的标准格式

**标准格式：**

Lambda省去面向对象的条条框框，格式由**3个部分**组成：

* 一些参数
* 一个箭头
* 一段代码

Lambda表达式的**标准格式**为：

```
(参数类型 参数名称) -> { 代码语句 }
```

**格式说明：**

* 小括号内的语法与传统方法参数列表一致：无参数则留空；多个参数则用逗号分隔。
* `->`是新引入的语法格式，代表指向动作。
* 大括号内的语法与传统方法体要求基本一致。

**匿名内部类与lambda对比:**

```java
new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println("多线程任务执行！");
			}
}).start();
```

仔细分析该代码中，`Runnable`接口只有一个`run`方法的定义：

- `public abstract void run();`

即制定了一种做事情的方案（其实就是一个方法）：

- **无参数**：不需要任何条件即可执行该方案。
- **无返回值**：该方案不产生任何结果。
- **代码块**（方法体）：该方案的具体执行步骤。

同样的语义体现在`Lambda`语法中，要更加简单：

```java
() -> System.out.println("多线程任务执行！")
```

- 前面的一对小括号即`run`方法的参数（无），代表不需要任何条件；
- 中间的一个箭头代表将前面的参数传递给后面的代码；
- 后面的输出语句即业务逻辑代码。

**参数和返回值:**

下面举例演示`java.util.Comparator<T>`接口的使用场景代码，其中的抽象方法定义为：

* `public abstract int compare(T o1, T o2);`

当需要对一个对象数组进行排序时，`Arrays.sort`方法需要一个`Comparator`接口实例来指定排序的规则。假设有一个`Person`类，含有`String name`和`int age`两个成员变量：

```java
public class Person { 
    private String name;
    private int age;
    
    // 省略构造器、toString方法与Getter Setter 
}
```

**传统写法**

如果使用传统的代码对`Person[]`数组进行排序，写法如下：

```java
public class Demo05Comparator {
    public static void main(String[] args) {
      	// 本来年龄乱序的对象数组
        Person[] array = { new Person("古力娜扎", 19),        	                      new Person("迪丽热巴", 18),     
                          new Person("马尔扎哈", 20) };
      	// 匿名内部类
      Comparator<Person> comp = new Comparator<Person>(){
            @Override
            public int compare(Person o1, Person o2) {
                return o1.getAge() - o2.getAge();
            }
        };
        Arrays.sort(array, comp); // 第二个参数为排序规则，即Comparator接口实例

        for (Person person : array) {
            System.out.println(person);
        }
    }
}
```

这种做法在面向对象的思想中，似乎也是“理所当然”的。其中`Comparator`接口的实例（使用了匿名内部类）代表了“按照年龄从小到大”的排序规则。

**代码分析**

下面我们来搞清楚上述代码真正要做什么事情。

- 为了排序，`Arrays.sort`方法需要排序规则，即`Comparator`接口的实例，抽象方法`compare`是关键；
- 为了指定`compare`的方法体，**不得不**需要`Comparator`接口的实现类；
- 为了省去定义一个`ComparatorImpl`实现类的麻烦，**不得不**使用匿名内部类；
- 必须覆盖重写抽象`compare`方法，所以方法名称、方法参数、方法返回值**不得不**再写一遍，且不能写错；
- 实际上，**只有参数和方法体才是关键**。

**Lambda写法**

```java
public class Demo06ComparatorLambda {
    public static void main(String[] args) {
        Person[] array = {
          	new Person("古力娜扎", 19),
          	new Person("迪丽热巴", 18),
          	new Person("马尔扎哈", 20) };

        Arrays.sort(array, (Person a, Person b) -> {
          	return a.getAge() - b.getAge();
        });

        for (Person person : array) {
            System.out.println(person);
        }
    }
}
```

### 1.3 掌握Lambda表达式的省略格式与规则

#### 1.3.1 省略规则

在Lambda标准格式的基础上，使用省略写法的规则为：

1. 小括号内参数的类型可以省略；
2. 如果小括号内**有且仅有一个参**，则小括号可以省略；
3. 如果大括号内**有且仅有一个语句**，则无论是否有返回值，都可以省略大括号、return关键字及语句分号。

> 备注：掌握这些省略规则后，请对应地回顾本章开头的多线程案例。

**可推导即可省略**

Lambda强调的是“做什么”而不是“怎么做”，所以凡是可以推导得知的信息，都可以省略。例如上例还可以使用Lambda的省略写法：

```java
Runnable接口简化:
1. () -> System.out.println("多线程任务执行！")
Comparator接口简化:
2. Arrays.sort(array, (a, b) -> a.getAge() - b.getAge());
```

#### 1.3.2 Lambda的前提条件

Lambda的语法非常简洁，完全没有面向对象复杂的束缚。但是使用时有几个问题需要特别注意：

1. 使用Lambda必须具有接口，且要求**接口中有且仅有一个抽象方法**。
   无论是JDK内置的`Runnable`、`Comparator`接口还是自定义的接口，只有当接口中的抽象方法存在且唯一时，才可以使用Lambda。
2. 使用Lambda必须具有接口作为方法参数。
   也就是方法的参数或局部变量类型必须为Lambda对应的接口类型，才能使用Lambda作为该接口的实例。

> 备注：有且仅有一个抽象方法的接口，称为“**函数式接口**”。

## 二、函数式接口

### 2.1 如何使用`Consumer<T>`函数式接口

`java.util.function.Consumer<T>`接口则正好相反，它不是生产一个数据，而是**消费**一个数据，其数据类型由泛型参数决定。

**抽象方法：accept**

`Consumer`接口中包含抽象方法`void accept(T t)`，意为消费一个指定泛型的数据。基本使用如：

```java
import java.util.function.Consumer;
/**
 * 函数式接口 java.util.function.Consumer<T>  消费
 * 接口的抽象方法 void accept(T t)
 * 消费:
 *   Consumer<String>
 *          void accept(String t)
 *          输出长度,切割,截取
 */
public class ConsumerDemo {
    public static void main(String[] args) {
        // System.out.println("正常调用------");
       /* MyConsumer mc = new MyConsumer();
        acceptString(mc,"你好");*/

       /* System.out.println("匿名内部类------");
        acceptString(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        },"你好");
    }*/
/**
 * lambda改进实现类
 * 带参数
 * s 参数传递到方法中,方法accept()方法,接口重写方法
 * 接口方法accept,lambda中 {}
 */
        System.out.println("Lambda------");
       acceptString((String s)->{System.out.println(s);},"你好呀");
    }

   public static void acceptString(Consumer<String> consumer,String str){
        consumer.accept(str);
    }
}
/*class MyConsumer implements Consumer<String>{

    @Override
    public void accept(String s) {
        System.out.println(s);
    }
}*/
```

### 2.2 如何使用`Predicate<T>`函数式接口

有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果。这时可以使用`java.util.function.Predicate<T>`接口。

**抽象方法：test**

`Predicate`接口中包含一个抽象方法：`boolean test(T t)`。用于条件判断的场景，条件判断的标准是传入的Lambda表达式逻辑，只要字符串长度大于5则认为很长。

```java
import java.util.function.Predicate;
/**
 *  java.util.function.Predicate 接口
 *  抽象方法:
 *    Predicate<String>
 *    boolean test(String t);
 */
public class PredicateDemo {
    public static void main(String[] args) {
       /* System.out.println("方法调用-----------");
     boolean b = getBoolean(new MyPredicate(),"464654");
        System.out.println(b);
    }*/

      /*System.out.println("匿名内部类------------");
      boolean b = getBoolean(new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.length()>5;
            }
        },"464646");
        System.out.println(b);
    }*/
       
       //lambda表达式,判断字符串长度是否大于5,返回true
     System.out.println("Lambda----------");
     boolean b = getBoolean( s->s.length()>5,"46461231");
        System.out.println(b);
    }
    public static boolean getBoolean(Predicate<String> predicate,String s){
        return predicate.test(s);
    }
}

/*class MyPredicate implements Predicate<String>{
    @Override
    public boolean test(String s) {
        return s.length()<5;
    }
}*/
```

## 三、Stream流

### 3.1 掌握常用的流操作

#### 3.1.1 获取流方式

`java.util.stream.Stream<T>`是Java 8新加入的最常用的流接口。（这并不是一个函数式接口。）

获取一个流非常简单，有以下几种常用的方式：

- 所有的`Collection`集合都可以通过`stream`默认方法获取流；
- `Stream`接口的静态方法`of`可以获取数组对应的流。

```java
/**
 *  java.util.stream.Stream 流对象的接口(流水线)
 *  获取到实现类
 *
 *  集合:
 *    Collection接口,JDK8定义方法  Stream接口类型 stream()
 *
 *  数组:
 *    Arrays静态方法 Stream stream()
 *
 *  接口Stream定义静态方法 of(T... values)
 *
 */
public class StreamDemo01 {
    public static void main(String[] args) {
        //根据Collection获取流
        List<String> list = new ArrayList<String>();
        list.add("CodeBull");
        list.add("你好");
        Stream<String> stream = list.stream();
        stream.forEach(s-> System.out.println(s));

        //根据数组获取流
        String[] array = {"76","646","5","4654"};
        Stream<String> stringStream = Stream.of(array);
        stringStream.forEach(s-> System.out.println(s));
    }
}
```

#### 3.1.2 常用方法

流模型的操作很丰富，这里介绍一些常用的API。这些方法可以被分成两种：

- **终结方法**：返回值类型不再是`Stream`接口自身类型的方法，因此不再支持类似`StringBuilder`那样的链式调用。本小节中，终结方法包括`count`和`forEach`方法。
- **非终结方法**：返回值类型仍然是`Stream`接口自身类型的方法，因此支持链式调用。（除了终结方法外，其余方法均为非终结方法。）

> 备注：本小节之外的更多方法，请自行参考API文档。

##### forEach : 逐一处理

虽然方法名字叫`forEach`，但是与for循环中的“for-each”昵称不同

```java
void forEach(Consumer<? super T> action);
```

```java
import java.util.stream.Stream;
/**
 *  Stream接口中的方法:
 *    foreach()集合中的元素一一进行了操作
 *    函数式接口 Consumer 是方法foreach的参数
 *    传递此接口实现类
 *
 *    函数式接口 Consumer 消费
 *    抽象方法accept
 */
public class StreamDemo02 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("张三丰");
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        //集合方法，获取Stream流对象
        Stream<String> stream = list.stream();
        // void accept(String t);
        //stream操作的集合泛型是String
        //遍历名s,代表了集合中的元素,s传递到accept方法体
        stream.forEach(s-> System.out.println(s));

        /*list.stream().forEach((String s)->{
            System.out.println(s);
        });*/
    }
```

在这里，lambda表达式`(String str)->{System.out.println(str);}`就是一个Consumer函数式接口的示例。

##### filter：过滤

可以通过`filter`方法将一个流转换成另一个子集流。方法声明：

```java
Stream<T> filter(Predicate<? super T> predicate);
```

该接口接收一个`Predicate`函数式接口参数（可以是一个Lambda）作为筛选条件。

**基本使用**

Stream流中的`filter`方法基本使用的代码如：

```java
/**
 * Stream对象的方法 filter 过滤
 * 过滤掉不需要的集合元素
 *
 * 过滤:只要姓张的
 *
 * filter方法的参数,是函数式接口 Predicate (判断)方法test
 * 方法返回true,不过滤元素,要
 *
 * 配合: 实现过滤,看不到过滤后的结果,流中的元素逐一操作,方法foreach
 */
public class StreamDemo03 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("周芷诺");
        list.add("张无忌");
        list.add("张三丰");

        //Stream流对象的方法 filter
        //s表示集合中的每个元素,传递到方法test中
        list.stream()
                .filter(s ->  s.startsWith("张") )
                .forEach(s -> System.out.println(s));
    }
```

在这里通过Lambda表达式来指定了筛选的条件：必须姓张。

##### count：统计个数

正如旧集合`Collection`当中的`size`方法一样，流提供`count`方法来数一数其中的元素个数：

```java
long count();
```

该方法返回一个long值代表元素个数（不再像旧集合那样是int值）。基本使用：

```java
/**
 * Stream接口方法count()
 * 返回long,返回Stream对象中元素的个数
 *
 * 此流终结,使用完毕,不能在继续使用Stream对象的方法
 * void foreach()终结此流
 */
public class StreamDemo07 {
    public static void main(String[] args) {
    Stream<String> stream = Stream.of("3", "5", "1", "2");
        long count = stream.count();
        System.out.println(count);
    }
```

##### limit：取用前几个，skip：跳过前几个

- `limit`方法可以对流进行截取，只取用前n个。
- `skip`方法跳过前几个元素，取用跳过后的元素

```java
//参数是一个long型，如果集合当前长度大于参数则进行截取;否则不进行操作。
Stream<T> limit(long maxSize):获取Stream流对象中的前n个元素,返回一个新的Stream流对象
    
//如果流的当前长度大于n，则跳过前n个；否则将会得到一个长度为0的空流。
Stream<T> skip(long n): 跳过Stream流对象中的前n个元素,返回一个新的Stream流对象
  
```

代码实现：

```java
import java.util.stream.Stream;
/**
 *  Stream接口方法
 *    limit(long n)  取出前几个元素
 *    skip(long n)   跳过前几个元素
 */
public class StreamDemo04 {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        list.add(5);
        //获取流对象
        Stream<Integer> stream = list.stream();
        //取出前4个元素,跳过前2个
        stream.limit(3).skip(2).forEach(s-> System.out.println(s));
    }

```

##### concat：组合

如果有两个流，希望合并成为一个流，那么可以使用`Stream`接口的静态方法`concat`：

```java
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b): 把参数列表中的两个Stream流对象a和b,合并成一个新的Stream流对象
```

> 备注：这是一个静态方法，与`java.lang.String`当中的`concat`方法是不同的。

该方法的基本使用代码如：

```java
import java.util.stream.Stream;

public class Demo20StreamConcat {
    public static void main(String[] args) {
        Stream<String> streamA = Stream.of("张无忌");
        Stream<String> streamB = Stream.of("张翠山");
        Stream<String> result = Stream.concat(streamA, streamB);
    }
}
```

##### collect：流转集合

从Stream流对象转成集合对象，使用`Stream`接口方法`collect:`

```java
public static void main(String[] args) {
    Stream stream = Stream.of("aa","bb","cc","dd","ee","ee");
    Set<String> list = ( Set<String> )stream.collect(Collectors.toSet());
    for (String s :list){
        System.out.println(s);
    }
}
```

