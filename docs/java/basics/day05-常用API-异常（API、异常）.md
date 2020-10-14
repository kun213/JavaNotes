## day05【API、异常】

## 今日学习内容-2020.9.22

- Math类
- BigInteger类
- BigDecimal类
- 基本类型包装类
- 异常

## 一、Math类

### 1.1 常用方法

| 方法名    方法名                     | 说明                                           |
| ------------------------------------ | ---------------------------------------------- |
| public static int   abs(int a)       | 返回参数的绝对值                               |
| public static double ceil(double a)  | 返回大于或等于参数的最小double值，等于一个整数 |
| public static double floor(double a) | 返回小于或等于参数的最大double值，等于一个整数 |
| public   static int round(float a)   | 按照四舍五入返回最接近参数的int                |

**代码举例：**

```java
  /**
     *  static int abs(int a)返回参数的绝对值
     *  正数的绝对值是他自己
     *  负数的绝对值是去掉符号
     */
    public static void method_1(){
        int i = Math.abs(-5);
        System.out.println(i);
    }
```

## 二、BigInteger类

### 2.1 会使用常用方法

| 方法名                     | 含义                                                         |
| -------------------------- | ------------------------------------------------------------ |
| add(BigInteger value)      | 返回其值为 `(this + val)` 的 BigInteger，超大整数加法运算    |
| subtract(BigInteger value) | 返回其值为 `(this - val)` 的 BigInteger，超大整数减法运算    |
| multiply(BigInteger value) | 返回其值为 `(this * val)` 的 BigInteger，超大整数乘法运算    |
| divide(BigInteger value)   | 返回其值为 `(this / val)` 的 BigInteger，超大整数除法运算，除不尽取整数部分 |

```java
public static void main(String[] args){
    BigInteger big1 = new BigInteger("987654321123456789000");
    BigInteger big2 = new BigInteger("123456789987654321");
    //加法运算
    BigInteger add = big1.add(big2);
    System.out.println("求和:"+add);
    //减法运算
    BigInteger sub = big1.subtract(big2);
    System.out.println("求差:"+sub);
    //乘法运算
    BigInteger mul = big1.multiply(big2);
    System.out.println("乘积:"+mul);
    //除法运算
    BigInteger div = big1.divide(big2);
    System.out.println("除法:"+div);
}
```

## 三、BigDecimal类

### 3.1 常用方法

BigDecimal类的加法减法乘法与BigInteger类相同，不在重复。

### 3.2 会使用除法计算

BigDecimal类实现精确的浮点数除法运算，如果两个浮点除法计算后是无限循环，那么就会抛出异常。

除法运算方法：

- BigDecimal divide(BigDecimal divisor,int scale,int roundingMode)
  - divesor：此 BigDecimal 要除以的值。
  - scale：保留的位数
  - roundingMode：舍入方式
- 舍入方式：BigDecimal类提供静态的成员变量来表示舍入的方式
  - BigDecimal.ROUND_UP  向上加1。
  - BigDecimal.ROUND_DOWN 直接舍去。
  - BigDecimal.ROUND_HALF_UP 四舍五入。

```java
public static void main(String[] args){
      BigDecimal big1 = new BigDecimal("5.25");
      BigDecimal big2 = new BigDecimal("3.25");
      //加法计算
      BigDecimal add = big1.add(big2);
      System.out.println("求和:"+add);
      //减法计算
      BigDecimal sub = big1.subtract(big2);
      System.out.println("求差:"+sub);
      //乘法计算
      BigDecimal mul = big1.multiply(big2);
      System.out.println("乘法:"+mul);
      //除法计算
      BigDecimal div = big1.divide(big2,2,BigDecimal.ROUND_HALF_UP);
      System.out.println(div);
}
```

## 四、基本类型包装类

基本类型与对应的包装类对象之间，来回转换的过程称为”装箱“与”拆箱“：

- **装箱**：从基本类型转换为对应的包装类对象。
- **拆箱**：从包装类对象转换为对应的基本类型。

用Integer与 int为例：（看懂代码即可）

基本数值---->包装对象

```java
Integer i = new Integer(4);//使用构造函数函数
Integer iii = Integer.valueOf(4);//使用包装类中的valueOf方法
```

包装对象---->基本数值

```java
int num = i.intValue();
```

### 4.1 能说出自动装箱与自动拆箱的概念

由于我们经常要做基本类型与包装类之间的转换，从Java 5（JDK 1.5）开始，基本类型与包装类的装箱、拆箱动作可以自动完成。例如：

```java
*  自动装箱 : 基本数据类型自动转成对象
*  自动拆箱 : 对象自动转成基本数据类型
```

```java
Integer i = 4;//自动装箱。相当于Integer i = Integer.valueOf(4);
i = i + 5;//等号右边：将i对象转成基本数值(自动拆箱) i.intValue() + 5;
//加法运算完成后，再次装箱，把基本数值转成对象。
```

### 4.2 可以将基本类型转换为对应的字符串类型（String）

- 转换方式
  - 方式一：直接在数字后加一个空字符串
  - 方式二：通过String类静态方法valueOf()
- 示例代码

```java
public static void main(String[] args) {
    //int --- String
    int number = 100;
    //方式1
    String s1 = number + "";
    System.out.println(s1);
    //方式2
    //public static String valueOf(int i)
    String s2 = String.valueOf(number);
    System.out.println(s2);
    System.out.println("--------");
}
```

### 4.3 将字符串转换为对应的基本类型

```java
Integer类中静态方法 static int parseInt(String s)
*   参数字符串转成基本数据类型int, 字符串必须数字格式
*  String -> int
```

代码举例：

```java
public static void main(String[] args) {
        int i = Integer.parseInt("100");
        System.out.println(i+5);
       //字符串转变成基本类型
        Integer integer = new Integer("200");
        int j = integer.intValue();
        System.out.println(j+5);
    }
```

String转换成基本类型 

除了Character类之外，其他所有包装类都具有parseXxx静态方法可以将字符串参数转换为对应的基本类型：

- `public static byte parseByte(String s)`：将字符串参数转换为对应的byte基本类型。
- `public static short parseShort(String s)`：将字符串参数转换为对应的short基本类型。
- `public static int parseInt(String s)`：将字符串参数转换为对应的int基本类型。
- `public static long parseLong(String s)`：将字符串参数转换为对应的long基本类型。
- `public static float parseFloat(String s)`：将字符串参数转换为对应的float基本类型。
- `public static double parseDouble(String s)`：将字符串参数转换为对应的double基本类型。
- `public static boolean parseBoolean(String s)`：将字符串参数转换为对应的boolean基本类型。

## 五、异常

### 5.1 如何辨别程序中异常和错误

```java
  异常的继承体系:
     java.lang.Throwable 是所有异常和错误的父类
       子类 : Error  所有错误的父类
          所有的错误的类名都是以Error结尾
          错误是程序中出现了严重问题,不修改源代码,不能执行
          人: 严重的疾病,不换器官就完了
              
       子类 : Exception  所有异常的父类
          所有的异常的类名都是以Exception结尾
          程序中出现了一般性问题,处理掉,可以继续执行
```

### 5.2 说出异常的分类

```java
      Exception的分类
         RuntimeException : 运行异常。在运行时期,检查异常.在编译时期,运行异常不会编译器检测(不报错)。(如数学异常)
        非RuntimeException : 编译异常。在编译时期,就会检查,如果没有处理异常,则编译失败。(如日期格式化异常)
```

### 5.3 说出常用的几个运行期异常

越界异常：IndexOutOfBoundsException
空指针异常：NullPointerException
类型强制转换异常：ClassCastException
无效参数异常：IllegalArgumentException

### 5.4 能够使用try...catch关键字处理异常

try...catch的方式就是捕获异常

格式：

```java
try{
     被检测的代码
    可能出现异常的代码
}catch(异常类的类名 变量名){
     异常处理的方式
     //记录日志/打印异常信息/继续抛出异常
}
/**
 *     try: 关键字 是尝试的意思,一旦出现异常
 *     catch : 关键字 抓住异常, 捕获,进行处理
 *     异常的处理方式:  有catch抓住异常,就叫做处理了
 *     处理方式任意: 变量,new 对象,调用方法,判断,循环
 */
```

代码示例：


```java
public static void main(String[] args) {
        int[] arr  = {1,2,3};
        try {
            int i = getArray(arr);
            System.out.println(i);
        }catch (Exception ex){
            //调用Throwable类方法
            //public String getMessage():获取异常的描述信息,原因(提示给用户的时候,就提示错误原因。
            String message = ex.getMessage();
            System.out.println("message="+message);// 5

            //public String toString():获取异常的类型和异常描述信息(不用)。
            String str = ex.toString();
            System.out.println("str="+str);//java.lang.ArrayIndexOutOfBoundsException: 5

            //public void printStackTrace():打印异常的跟踪栈信息并输出到控制台。(常用)
            ex.printStackTrace();//异常信息: 异常类名,越界索引,程序出现的行数 (主力)
        }
        System.out.println("程序结束");
    }

    public static int getArray(int[] arr){
        return arr[5];
    }
```

### 5.5 能够使用throws关键字处理异常

**thow**: 在方法内部抛出异常对象

​            只能写在方法里面,throw new 异常对象()

**throws**: 在方法定义上使用,告知调用者有异常

​              throws只能写在方法定义后,throws 写异常类名

示例说明：

```java
/*需求:
 *   定义方法,计算正方形面积 (整数)
 */
public static void main(String[] args) {
        try {
            int area = getArea(5);
            System.out.println("面积是:" + area);
        }catch (Exception ex){
            System.out.println("边长不存在");
        }
    }
    /**
     *  边长未知数,定义在参数上
     *  用户调用方法时,传递
     *  用户传递0或者负数
     *  使用异常技术,手动抛出异常
     *  关键字 throw new的异常对象()
     *
     *  getArea()方法: 传递错误的参数,导致不能计算
     *  方法的内部会抛出异常的对象, 对于调用者main,看的到你方法中有异常吗,NO
     *  getArea()方法,告诉调用者,我有异常!!
     *
     *  关键字 throws  在方法的定义上标明出,我有异常,需要调用者进行处理
     *  throws关键字后面写异常类的名字
     */
    public static int getArea(int a)throws Exception{
        if(a <= 0)
            //无法计算,使用异常提示用户,手动抛出异常
            throw new Exception();
        return  a * a;
    }
}
```

