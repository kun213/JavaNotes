## day02面向对象【接口，多态】

### 今日学习内容-2020.9.17

- 接口
- 多态

### 一、接口

#### 1.1 接口的定义格式

```java
public interface 接口名称 {
    // 抽象方法
    // 默认方法
    // 静态方法
}
```

#### 2.2 接口的实现

类与接口的关系为实现关系，即**类实现接口**，该类可以称为接口的实现类，也可以称为接口的子类。实现的动作类似继承，格式相仿，只是关键字不同，实现使用 ` implements`关键字。

非抽象子类实现接口：

1. 必须重写接口中所有抽象方法。
2. 继承了接口的默认方法，即可以直接调用，也可以重写。

实现格式：

```java
class 类名 implements 接口名 {
    // 重写接口中抽象方法【必须】
  	// 重写接口中默认方法【可选】
} 
```

#### 1.3 接口中的成员特点

接口中，无法定义成员变量，但是可以定义常量，其值不可以改变，默认使用public static final修饰。
```java
public interface LiveAble {
   int NUM0 ; // 错误,必须赋值  
   int NUM1 =10; // 正确 , 省去了默认修饰符 public static final
   public static final int NUM2= 100; // 正确 , 完整写法
}
```

#### 1.4 接口的多实现

在继承体系中，一个类只能继承一个父类。而对于接口而言，一个类是可以实现多个接口的，这叫做接口的**多实现**。并且，一个类能继承一个父类，同时实现多个接口。

实现格式：

```java
class 类名 [extends 父类名] implements 接口名1,接口名2,接口名3... {
    // 重写接口中抽象方法【必须】
  	// 重写接口中默认方法【不重名时可选】
} 
```

> [ ]： 表示可选操作。

#### 1.5 接口的多继承

一个接口能继承另一个或者多个接口，这和类之间的继承比较相似。接口的继承使用 `extends` 关键字，子接口继承父接口的方法。**如果父接口中的默认方法有重名的，那么子接口需要重写一次。**代码如下：

#### 1.6 抽象类和接口的区别

通过实例进行分析和代码演示抽象类和接口的用法。

1、举例：

​	犬：

​		行为：

​			吼叫；

​			吃饭；

​	缉毒犬：

​		行为：

​			吼叫；

​			吃饭；

​			缉毒；

2、思考：

​	由于犬分为很多种类，他们吼叫和吃饭的方式不一样，在描述的时候不能具体化，也就是吼叫和吃饭的行为不能明确。当描述行为时，行为的具体动作不能明确，这时，可以将这个行为写为抽象行为，那么这个类也就是抽象类。

​	可是当缉毒犬有其他额外功能时，而这个功能并不在这个事物的体系中。这时可以让缉毒犬具备犬科自身特点的同时也有其他额外功能，可以将这个额外功能定义接口中。 

```java
interface 缉毒{
	public abstract void 缉毒();
}
//定义犬科的这个提醒的共性功能
abstract class 犬科{
public abstract void 吃饭();
public abstract void 吼叫();
}
// 缉毒犬属于犬科一种，让其继承犬科，获取的犬科的特性，
//由于缉毒犬具有缉毒功能，那么它只要实现缉毒接口即可，这样即保证缉毒犬具备犬科的特性，也拥有了缉毒的功能
class 缉毒犬 extends 犬科 implements 缉毒{

	public void 缉毒() {
	}
	void 吃饭() {
	}
	void 吼叫() {
	}
}
class 缉毒猪 implements 缉毒{
	public void 缉毒() {
	}
}
```
3、通过上面的例子总结接口和抽象类的区别：

**相同点:**

​	 都位于继承的顶端,用于被其他类实现或继承;

​	 都不能直接实例化对象;

​	 都包含抽象方法,其子类都必须覆写这些抽象方法;

**区别:**

 	抽象类为部分方法提供实现,避免子类重复实现这些方法,提高代码重用性;接口只能包含抽象方法;

 	一个类只能继承一个直接父类(可能是抽象类),却可以实现多个接口;(接口弥补了Java的单继承)

 	抽象类为继承体系中的共性内容,接口为继承体系中的扩展功能

- 成员区别

  - 抽象类
    - 变量,常量；有构造方法；有抽象方法,也有非抽象方法
  - 接口
    - 常量；抽象方法

- 关系区别

  - 类与类
    - 继承，单继承
  - 类与接口
    - 实现，可以单实现，也可以多实现
  - 接口与接口
    - 继承，单继承，多继承

- 设计理念区别

  - 抽象类

    - 对类抽象，包括属性、行为

  - 接口

    - 对行为抽象，主要是行为

------

### 二、多态

#### 2.1 说出多态的前提

- 继承或者实现【二选一】
- 方法的重写【意义体现：不重写，无意义】
- 父类引用指向子类对象【格式体现】

#### 2.2 写出多态的格式

```java
父类类型 变量名 = new 子类对象；
变量名.方法名();
```
**当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误；如果有，执行的是子类重写后方法。**

代码如下：

定义父类：

```java
public abstract class Animal {  
    public abstract void eat();  
}  
```

定义子类：

```java
class Cat extends Animal {  
    public void eat() {  
        System.out.println("吃鱼");  
    }  
}  

class Dog extends Animal {  
    public void eat() {  
        System.out.println("吃骨头");  
    }  
}
```

定义测试类：

```java
public class Test {
    public static void main(String[] args) {
        // 多态形式，创建对象
        Animal a1 = new Cat();  
        // 调用的是 Cat 的 eat
        a1.eat();          

        // 多态形式，创建对象
        Animal a2 = new Dog(); 
        // 调用的是 Dog 的 eat
        a2.eat();               
    }  
}
```
#### 2.3 引用类型转换

多态的转型分为向上转型与向下转型两种：

##### 2.3.1 向上转型

- **向上转型**：多态本身是子类类型向父类类型向上转换的过程，这个过程是默认的。

当父类引用指向一个子类对象时，便是向上转型。

使用格式：

```java
父类类型  变量名 = new 子类类型();
如：Animal a = new Cat();
```

##### 2.3.2 向下转型

- **向下转型**：父类类型向子类类型向下转换的过程，这个过程是强制的。

一个已经向上转型的子类对象，将父类引用转为子类引用，可以使用强制类型转换的格式，便是向下转型。

使用格式：

```java
子类类型 变量名 = (子类类型) 父类变量名;
如:Cat c =(Cat) a;  
```

##### 2.3.3 为什么要转型

当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误。也就是说，**不能调用**子类拥有，而父类没有的方法。编译都错误，更别说运行了。这也是多态给我们带来的一点"小麻烦"。所以，想要调用子类特有的方法，必须做向下转型。

转型演示，代码如下：

定义类：

```java
abstract class Animal {  
    abstract void eat();  
}  

class Cat extends Animal {  
    public void eat() {  
        System.out.println("吃鱼");  
    }  
    public void catchMouse() {  
        System.out.println("抓老鼠");  
    }  
}  

class Dog extends Animal {  
    public void eat() {  
        System.out.println("吃骨头");  
    }  
    public void watchHouse() {  
        System.out.println("看家");  
    }  
}
```

定义测试类：

```java
public class Test {
    public static void main(String[] args) {
        // 向上转型  
        Animal a = new Cat();  
        a.eat(); 				// 调用的是 Cat 的 eat

        // 向下转型  
        Cat c = (Cat)a;       
        c.catchMouse(); 		// 调用的是 Cat 的 catchMouse
    }  
}
```

##### 2.3.4 转型的异常

转型的过程中，一不小心就会遇到这样的问题，请看如下代码：

```java
public class Test {
    public static void main(String[] args) {
        // 向上转型  
        Animal a = new Cat();  
        a.eat();               // 调用的是 Cat 的 eat

        // 向下转型  
        Dog d = (Dog)a;       
        d.watchHouse();        // 调用的是 Dog 的 watchHouse 【运行报错】
    }  
}
```

这段代码可以通过编译，但是运行时，却报出了 `ClassCastException` ，类型转换异常！这是因为，明明创建了Cat类型对象，运行时，当然不能转换成Dog对象的。这两个类型并没有任何继承关系，不符合类型转换的定义。

为了避免ClassCastException的发生，Java提供了 `instanceof` 关键字，给引用变量做类型的校验，格式如下：

```java
变量名 instanceof 数据类型 
如果变量属于该数据类型，返回true。
如果变量不属于该数据类型，返回false。
```

所以，转换前，我们最好先做一个判断，代码如下：

```java
public class Test {
    public static void main(String[] args) {
        // 向上转型  
        Animal a = new Cat();  
        a.eat();               // 调用的是 Cat 的 eat

        // 向下转型  
        if (a instanceof Cat){
            Cat c = (Cat)a;       
            c.catchMouse();        // 调用的是 Cat 的 catchMouse
        } else if (a instanceof Dog){
            Dog d = (Dog)a;       
            d.watchHouse();       // 调用的是 Dog 的 watchHouse
        }
    }  
}
```
