## day15 【注解、反射、lombok】

## 今日学习内容-2020.10.17

- 类加载器
- 反射概念
- 反射构造方法
- 反射成员方法
- 注解
- 注解的解析
- lombok的使用

## 一、类的加载器

### 1.1 可以说出类加载器的双亲委派

ClassLoader类定义了方法 `ClassLoader getParent()`：返回父类加载器。

```java
//获取自己定义类的加载器，结果为AppClassLoader
ClassLoader loader = Test.class.getClassLoader();
//获取AppClassLoader的父类加载器，结果为ExtClassLoader
System.out.println(loader.getParent());
//获取ExtClassLoader的父类加载器，结果为null
System.out.println(loader.getParent().getParent());
```

结论： AppClassLoader的父类加载器是ExtClassLoader，ExtClassLoader的父类加载器是Bootstrap。

**注意**：ExtClassLoader是AppClassLoader的父加载器，并不是父类，他们没有继承关系。

**谁用谁加载：**当A类中使用了B类，那么负责加载A类的加载器要去加载B类。

**双亲委派机制**：当AppClassLoader收到一个加载类的请求时，会先让他的父类加载器ExtClassLoader尝试加载，ExtClassLoader也会让他的父类加载器Bootstrap尝试加载，如果Bootstrap能加载，就加载该类。如果Bootstrap不能加载，则ExtClassLoader会进行加载，如果也不能加载，AppClassLoader会进行加载。

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201017223600.jpg)



## 二、反射

### 2.1 如何通过反射技术获取Class字节码对象

| 获取class对象方式       | 作用                         | 应用场景                                                 |
| ----------------------- | ---------------------------- | -------------------------------------------------------- |
| Class.forName("全类名") | 通过指定的字符串路径获取     | 多用于配置文件，将类名定义在配置文件中。读取文件，加载类 |
| 类名.class              | 通过类名的属性class获取      | 多用于参数的传递                                         |
| 对象.getClass()         | 通过对象的getClass()方法获取 | 多用于对象的获取字节码的方式                             |

Student类：

```java
public class Student {
    private String name;
    private int age;
    public Student(){
    }
    public Student(String name,int age) {
        this.name = name;
        this.age = age;
    }
    public void study(){
        System.out.println("学生在学习");
    }

    public void eat(String s,double d){
        System.out.println("带参数方法:"+s+"::"+d);
    }
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

获取Student类的class文件对象：

```java
/**
 *  反射关键对象: class文件对象
 *  类加载器创建,获取使用
 *
 *  获取Student类class文件对象
 *  是java.lang.Class类产生的对象
 *
 *  三种方法:
 *    1: 对象的方法 getClass()获取   (  getClass()定义在Object类 )
 *    2: 使用类的静态属性class获取
 *    3: Class类静态方法 forName()获取
 *
 *    三个中谁最常用  3:forName()
 *    扩展性最好  Class.forName("com.itheima.reflect.Student");参数是字符串String对象
 *    字符串未必提前写好,数据源能不能外来获取, 类名写文件中,IO读
 */
public class ReflectDemo {
    public static void main(String[] args) throws Exception{
        //1: 对象的方法 getClass()获取
        Student stu = new Student();
        System.out.println(stu.getClass());

        //2: 使用类的静态属性class获取
        //每一个数据类型,基本还是引用类型, JVM赋予这个类型一个静态属性,名字就是class
        Class<Student> studentClass = Student.class;
        System.out.println(studentClass);

        //3: Class类静态方法 forName("传递字符串的类名")获取
        //注意: 字符串形式的类名,必须是全限定名 包名.类名
       Class c =Class.forName("com.lk.day15.reflect.Student");
        System.out.println(c);
    }
}
```

### 2.2 如何通过反射技术获取构造方法对象，并创建对象

#### 2.2.1 获取无参构造方法

```java
import java.lang.reflect.Constructor;
/**
 * 反射技术:
 *  1:  获取一个类class文件对象,获取到Class类的对象
 *  2:  Class类的对象中的方法,获取出类的成员(构造,变量,方法)
 *  3:  运行获取出来的成员
 *
 *  反射案例1:
 *    反射解剖,Student类,获取无参数构造方法
 *    运行无参数构造 (对象创建)
 *
 */
public class ReflectDemo02 {
    public static void main(String[] args) throws Exception{
        Class c = Class.forName("com.lk.day15.reflect.Student");
        // 获取Student类中的构造方法Class类的方法
        // 方法 Constructor[] getConstructors()获取类中的所有公共权限的构造方法
       /* Constructor[] cons = c.getConstructors();
        for (Constructor con :cons){
            System.out.println(con);
        }*/

        //获取Student类中的无参数构造方法?
        //Class类的方法  Constructor getConstructor(传递?构造方法的参数列表...可变)
        //不传递参数,获取的是无参数构造
        Constructor con = c.getConstructor();
        //运行构造方法,创建对象,Constructor方法  Object  newInstance(Object...a)传递实际参数
        Object obj = con.newInstance();
        System.out.println(obj);
    }
}
```

#### 2.2.2 获取有参数构造方法

```java
import java.lang.reflect.Constructor;
/**
 *反射案例2:
 *    反射解剖,Student类,获取有参数构造方法
 *    运行有参数构造(String,int) (对象创建)
 **/
public class ReflectDemo03 {
    public static void main(String[] args) throws Exception {
        //Class类的方法,获取有参数的构造,String,int
        //传递构造方法参数列表中,数据类型的class文件对象  getConstructor(Class...p)
        Class c = Class.forName("com.lk.day15.reflect.Student");
        Constructor con = c.getConstructor(String.class, int.class);
        //运行构造方法,创建对象,Constructor方法 newInstance(传递构造方法的实际参数 Object...o)
        Object obj = con.newInstance("张三",20);
        System.out.println(obj);
    }
}
```

#### 2.2.4 反射获取构造方法的简单方法

```java
/**
 * 反射案例3:
 *   反射获取Student类的无参数构造方法执行
 *   使用简单的方式,减少代码量
 *
 *   但是前提:
 *     被反射的类,必须具有public权限的无参数构造
 */
public class ReflectDemo04 {
    public static void main(String[] args) throws Exception{
        Class c = Class.forName("com.lk.day15.reflect.Student");
        //Class类中,定义方法 Object newInstance() 运行无参数构造,创建对象
        Object obj = c.newInstance();
        System.out.println(obj);
    }
}
```

### 2.3 如何通过反射获取成员方法对象，并且调用方法

#### 2.3.1 反射获取无参数成员方法

```java
/**
 * 反射案例4:
 *   反射获取Student类的无参数的成员方法
 *   public void study()
 */
public class ReflectDemo06 {
    public static void main(String[] args) throws Exception{
        Class c = Class.forName("com.lk.day15.reflect.Student");
        Object obj = c.newInstance();
        //Class类的方法 Method[] getMethods() 获取到被反射对象,所有的公共权限方法,包括继承的
       /* Method[] methods = c.getMethods();
        for (Method method : methods){
            System.out.println(method);
        }*/
       
        //Class类的方法 Method getMethod(String 方法名,方法参数列表) 获取指定的成员方法
        Method study = c.getMethod("study");
        //运行成员方法, Method类的方法  Object invoke(Object obj,  Object...p);传递对象  调用
        study.invoke(obj);
    }
}
```

#### 2.3.2 反射获取无参数成员方法

```java
/**
 * 反射案例5:
 *   反射获取Student类的有参数的成员方法
 *   public void eat(String,double)
 */
public class ReflectDemo07 {
    public static void main(String[] args) throws Exception {
        Class c = Class.forName("com.lk.day15.reflect.Student");
        Object obj = c.newInstance();
        //获取指定的方法,argMethod,参数列表String,double
        Method method = c.getMethod("argMethod", String.class, double.class);
        //运行方法argMethod
        method.invoke(obj, "你好", 211.22);
    }
}
```

## 三、注解

### 3.1 如何自定义注解

#### 3.1.1 定义格式

```java
元注解
public @interface 注解名称{
	属性列表;
}
```

注解本质上就是一个接口，该接口默认继承Annotation接口。

```java
public @interface MyAnno extends java.lang.annotation.Annotation {}
```

任何一个注解，都默认的继承Annotation接口。

#### 3.1.2 注解的属性

1. **属性的作用**

   - 可以让用户在使用注解时传递参数，让注解的功能更加强大。

2. **属性的格式**

   - **格式1：数据类型 属性名();**
   - **格式2：数据类型 属性名() default 默认值;**

3. **属性定义示例**

   ```java
   public @interface Student {
     String name(); // 姓名
     int age() default 18; // 年龄
     String gender() default "男"; // 性别
   } 
   // 该注解就有了三个属性：name，age，gender
   ```

4. **属性适用的数据类型**

   - 八种基本数据类型（**int,float,boolean,byte,double,char,long,short**)。
   - String类型，Class类型，枚举类型，注解类型。
   - 以上所有类型的一维数组。

#### 3.1.3 特殊属性value

1. **当注解中只有一个属性且名称是value，在使用注解时给value属性赋值可以直接给属性值，无论value是单值元素还是数组类型。**

   ```java
   // 定义注解Book
   public @interface Book {
       // 书名
       String value();
   }
   // 使用注解Book
   public class BookShelf {
       @Book("西游记")
       public void showBook(){
       }
   }
   或
   public class BookShelf {
       @Book(value="西游记")
       public void showBook(){
       }
   }
   ```

2. **如果注解中除了value属性还有其他属性，且至少有一个属性没有默认值，则在使用注解给属性赋值时，value属性名不能省略。**

   ```java
   // 定义注解Book
   public @interface Book {
       // 书名
       String value();
       // 价格
       double price() default 100;
       // 多位作者
       String[] authors();
   }
   // 使用Book注解：正确方式
   @Book(value="红楼梦",authors = "曹雪芹")
   public class BookShelf {
     // 使用Book注解：正确方式
       @Book(value="西游记",authors = {"吴承恩","白求恩"})
       public void showBook(){
       }
   }
   // 使用Book注解：错误方式
   public class BookShelf {
       @Book("西游记",authors = {"吴承恩","白求恩"})
       public void showBook(){
       }
   }
   // 此时value属性名不能省略了。
   ```

### 3.2 如何使用自定义注解

#### 3.2.1 元注解

```java
/**
 * 自定义的注解 Book
 * 定义属性
 *
 * JDK的元注解,比喻注解的总管
 * 管理其他的注解
 *
 * 元注解对我们的注解进行控制
 * 1: 控制我们的注解,可以写在哪里,(类,方法,变量上,包...)
 * 2: 控制我们的注解的生命周期
 *
 * JDK的2个元注解
 *
 * @Target  指示其他注解,出现的位置
 *    ElementType[] value(); 数组,可以赋值多个
 *    ElementType是数据类型,是枚举
 *    枚举的属性,都是静态修饰,直接类名调用
 *      TYPE, 其他注解可以写在类上
 *      FIELD,其他注解可以写在成员变量
 *      METHOD,其他注解可以写在方法上
 *      PARAMETER,其他注解可以写在方法参数上
 *      CONSTRUCTOR,其他注解可以写在构造方法上
 *
 *  @Retention 指示其他注解的生命周期
 *     RetentionPolicy value(); 不是数组,赋值一个
 *     RetentionPolicy数据类型
 *     枚举的属性,都是静态修饰,直接类名调用
 *     CLASS  注解存在于编译后的class文件中
 *     SOURCE 注解仅存在于源码中java文件中
 *     RUNTIME 运行时期的内存中
 */
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Book {
    String bookName();
    double price();
    String[] author();
}
```

#### 3.2.2 注解解析

概念：通过Java技术获取注解数据的过程则称为注解解析。

**注解Book**

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Book {
    // 书名
    String value();
    // 价格
    double price() default 100;
    // 作者
    String[] authors();
}
```

**BookStore类**

```java
@Book(value = "三国演义",authors ={ "曹雪芹","dfs"},price = 33.6)
public class BookStore {
}
```

**TestAnnotation类**

```java
/**
 *  注解解析:
 *    获取注解上的属性值
 *    @Book(bookName = "三国演义",price = 33.6, author = {"asss","sss"})
 *
 *    接口 AnnotatedElement 方法:
 *       boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)
 *       解释:
 *         返回值是布尔值: 返回的true,该对象上有注解,返回的是false,该对象没有注解
 *
 *         Class<? extends Annotation> annotationClass
 *         传递class文件对象  ?不知道, 肯定?继承Annotation
 *         Annotation接口,是所有注解的顶级接口,可以传递任意的注解!!
 *
 *
 *       <T extends Annotation> getAnnotation(Class<T> annotationClass)
 *       返回注解
 *       解释:
 *         参数传递是任意注解的class文件对象
 *
 *         传递什么类型,返回什么类型
 *
 *      实现类: Class类, Constructor构造方法, Field成员变量, Method成员方法,
 *      结论: 注解解析,和哪个技术密切相关  反射
 *
 *      需求:
 *        获取BookShelf类上,注解Book的属性值
 *
 *    注解的解析思想:
 *      1: 反射带有注解的类
 *      2: 反射方法
 *      3: 判断方法上是否有注解
 *      4: 获取这个注解
 *      5: 获取注解的属性值
 */

public class AnnotationJieXi {
    @Test
    public void test() throws Exception{
        //反射BookShelf
        Class c = Class.forName("com.lk.day15_annotation.our.BookTest");
        // /反射获取方法sellBook
        Method method = c.getMethod("sellBook");
        //方法判断是否有注解
        //参数,传递注解的class文件对象
        boolean b = method.isAnnotationPresent(Book.class);
        if(b){
            Book book = method.getAnnotation(Book.class);
             //获取出Book注解的属性
            String bookName = book.bookName();
            double price = book.price();
            String[] author = book.author();
        System.out.println(bookName+price+Arrays.toString(author));
        }
    }
}
```

## 四、lombok

### 4.1 能够说出lombok的作用

Lombok能以注解形式来简化java代码，提高开发效率。Lombok能通过注解的方式，在编译时自动为属性生成构造器、getter/setter、equals、hashcode、toString方法。

### 4.2 可以使用@Data注解

### @Data

- 作用：生成get/set，toString，hashCode，equals，无参构造方法
- 注解只能写在类上。