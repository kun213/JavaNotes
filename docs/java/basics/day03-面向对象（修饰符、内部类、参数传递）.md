## day03 面向对象【修饰符，内部类，参数传递】

### 今日学习内容-2020.9.18

- final
- static
- 访问权限
- 内部类
- 引用类型参数

### 一、final关键字
**fina**  不可改变。可以用于修饰类、方法和变量。

- 类：被修饰的类，不能被继承。
- 方法：被修饰的方法，不能被重写。
- 变量：被修饰的变量，不能被重新赋值。

#### 1.1 final修饰的类的特点

 修饰类: 最终类,没有子类,不能被其他的类继承

​               有的类的功能,已经非常完美了,不需要子类进行扩展

​               学习过最终类: String,Scanner,System

格式：

```java
final class 类名 {
  
}
```

#### 1.2 final修饰的方法的特点

 修饰方法: 最终方法,不能被子类重写

​                  父类的某些方法已经非常完美,不需要子类扩展

 补充: 无论方法上是否有final修饰符,调用没有区别

格式：

```java
修饰符 final 返回值类型 方法名(参数列表){
    //方法体
}
```

#### 1.3 final修饰的变量的特点

- 局部变量

​      修饰局部变量: 

1. 一次赋值终身不变
2.   方法中定义的变量,加上修饰符final,变量值固定
3.   只能赋值一次
4. final修饰符固定的是变量保存的地址!!

- 成员变量

​     修饰成员变量：

​     成员变量具有默认值

1. ​    字符串（String）,默认null
2. ​    int,默认是0

final修饰成员变量,固定的不是默认值

固定的是我们自己赋的值

成员变量的赋值方式

1: 直接=赋值

2: 构造方法

3: set方法

set方法,可以调多次,

构造方法,在new的时候,只能调用一次

**深入:**  成员变量属于对象, 内存跟随对象在堆中存储

  一旦对象创建完毕了,成员变量的值也要确定了

   set方法是创建对象后的事情,用对象.set()

### 二、static关键字

**static**

1. 被static修饰的属于类,不属于对象
2. 优先于对象存在
3. static修饰的成员,可以作为共享的数据(只要是根据static修饰的成员所在的类创建出来的对象,都可以共享这个数据)

#### 2.1 static关键字修饰的变量调用方式

定义格式：

```java
static 数据类型 变量名； 
```

调用格式：

```java
类名.静态成员
```

非静态成员只能被对象调用

静态成员可以被类名调用,也能被对象调用

强制使用类名调用

#### 2.2 static关键字修饰的方法调用方式

定义格式：

```java
修饰符 static 返回值类型 方法名 (参数列表){ 
	// 执行语句 
}
```

举例：在Student类中定义静态方法

```java
public static void showNum() {
  System.out.println("num:" +  numberOfStudent);
}
```

调用格式：

```
类名.静态方法名（参数）
```

- **静态方法调用的注意事项：**
  - 静态方法可以直接访问类变量和静态方法。
  - 静态方法**不能直接访问**普通成员变量或成员方法。反之，成员方法可以直接访问类变量或静态方法。
  - 静态方法中，不能使用**this**和**super**关键字。

#### 2.3 静态代码块的格式

 静态代码块 :

写在类中, **static{}**

在使用类的成员的时候,执行,仅仅一次

只要你用这个类了,静态代码块就会执行,无论建立对象,调用方法,调用变量

JDBC:  JVM加载数据库驱动程序

**补充：**

- 子类和父类的多态程序 (对象的多态性)
- 静态在内存中,优先于对象, 静态和对象无关!!
- 静态成员变量: 编译还是运行,看父类中的成员变量
- 多态: 只有非静态的方法,编译是父类,运行时子类
- 深入: javac编译器,检测代码语法  fu.show();
- 知道方法是静态的,编译的时候,直接将fu对象,改写为**类名.静态方法**;
  

### 三、权限修饰符

#### 3.1 权限修饰符作用范围

|              | public | protected | default（空的） | private |
| ------------ | ------ | --------- | ----------- | ------- |
| 同一类中         | √      | √         | √           | √       |
| 同一包中(子类与无关类) | √      | √         | √           |         |
| 不同包的子类       | √      | √         |             |         |
| 不同包中的无关类     | √      |           |             |         |

可见，public具有最大权限。private则是最小权限。

编写代码时，如果没有特殊的考虑，建议这样使用权限：

- 成员变量使用`private` ，隐藏细节。
- 构造方法使用` public` ，方便创建对象。
- 成员方法使用`public` ，方便调用方法。

### 四、内部类

#### 4.1 内部类的概念

将一个类A定义在另一个类B里面，里面的那个类A就称为**内部类**，B则称为**外部类**。

定义格式:

```java
class 外部类 {
    class 内部类{

    }
}
```

在描述事物时，若一个事物内部还包含其他事物，就可以使用内部类这种结构。比如，汽车类`Car` 中包含发动机类`Engine` ，这时，`Engine `就可以使用内部类来描述，定义在成员位置。

#### 4.2 成员内部类

代码举例：

```java
class Car { //外部类
    class Engine { //内部类

    }
}
```
创建内部类对象格式：

```java
外部类名.内部类名 对象名 = new 外部类型().new 内部类型()；
```

#### 4.3 匿名内部类

匿名内部类是内部类的简化写法。它的本质是一个`带具体实现的` `父类或者父接口的` `匿名的` **子类对象**。

定义接口,实现类实现接口,重写方法

匿名内部类,简化实现类的书写

格式：

```java
new 父类名或者接口名(){
    // 方法重写
    public void method() {
        // 执行语句
    }
};
```

#### 4.4 匿名内部类实现重写抽象方法

代码：

```java
public static void main(String[] args) {
        /**
         * 匿名内部类,就是接口实现类的对象
         * 对象多态性:
         *   接口实现,方法重写,父类或者接口引用 = new 实现类对象()
         *   父类或者接口引用 = new 实现类对象()
         */
        MyInterface  my = new MyInterface(){
            public void show(){
                System.out.println("实现类重写方法show");
            }
            public void show2(){
                System.out.println("实现类重写方法show2");
            } };
        my.show();
        my.show2();
    }
```

###       五、引用类型传递

#### 5.1 类名作为方法参数和返回值

- 类名作为方法的形参

  方法的形参是类名，其实需要的是该类的对象

  实际传递的是该对象的【地址值】

- 类名作为方法的返回值

  方法的返回值是类名，其实返回的是该类的对象

  实际传递的，也是该对象的【地址值】

- 示例代码：

  ```java
  public class Person{
    public void eat(){
      System.out.println("人吃了");
    }
  }
  public class Test{
    public static void main(String[] args){
          method(new Person());
     		Person p = method2();
    }
    pubic static void method(Person p){
         p.eat();
    }
    public static Person method2(){
      	return new Person();
    }
  }

  ```
  

#### **5.2 抽象类作为形参和返回值**

- 抽象类作为形参和返回值

  - 方法的形参是抽象类名，其实需要的是该抽象类的子类对象
  - 方法的返回值是抽象类名，其实返回的是该抽象类的子类对象

- 示例代码：

  ```java
  public abstract class Animal{
    	public abstract void eat();
  }
  public class Cat extends Animal{
    public void eat(){
      System.out.println("猫吃鱼");
    }
  }
  public class Test{
    public static void main(String[] args){
        method(new Cat());
      
       Animal a = method2();
      
    }
    public static void method(Animal a){
         a.eat();
    }
    public static Animal method2(){
      	return new cat();
    }
  }

  ```

#### **5.3 接口名作为形参和返回值**

- 接口作为形参和返回值

  - 方法的形参是接口名，其实需要的是该接口的实现类对象
  - 方法的返回值是接口名，其实返回的是该接口的实现类对象

- 示例代码：

  ```java
   public interface Fly{
      public abstract void fly();
   }
  public class Bird implements Fly{
    public void fly(){
      System.out.println("我飞了");
    }
  }
  public class Test{
    public static void main(String[] args){
        method(new Bird());
      
       Fly f = method2();
      
    }
    public static void method(Fly f){
       	f.fly();
    }
    public static Fly method2(){
      	return new Bird();
    }
  }
  ```
  ![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20200920140221.JPG)

