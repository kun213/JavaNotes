## day04 【Object类、常用API】
## 今日学习内容-2020.9.20

- Object类
- 日期格式化
- 日历类
- 冒泡排序
- 数组的二分查找法
- Arrays
- 正则表达式

## 一、Object类

### 1.1 可以说出Object类的特点

`java.lang.Object`类是Java语言中的根类，每个类都使用 `Object` 作为超类。所有对象（包括数组）都实现这个类的方法。

如果一个类没有特别指定父类，那么默认则继承自Object类。例如：

```java
public class MyClass /*extends Object*/ {
  	// ...
}
```

### 1.2 能够重写Object类的toString方法

由于toString方法返回的结果是内存地址，而在开发中，内存地址并没有实际的应用价值，经常需要按照对象的属性得到相应的字符串表现形式，因此也需要重写它。

```java
public class Person {  
    private String name;
    private int age;
    
    public String toString() {
        return "Person"+name+":"+age;
    }
    // 省略构造器与Getter Setter
}
```

### 1.3 能够重写Object类的equals方法

实际应用中，比较内存地址是否相同并没有意义，我们可以定义对象自己的比较方式，比较对象中成员变量的值是否相同。需要对方法进行重写。

需求：重写equals()方法，比较两个对象中姓名和年龄是否相同，如果姓名和年龄都相同返回true，否则返回false。

```java
public class Person {
    private String name;
    private int age;
    
    public boolean equals(Object obj){
        //判断两个对象地址弱相同，即为同一个对象
        if(this == obj)
            return true;
        //obj对象为空，无需比较，返回false
        if(obj == null)
            return  false;
        //obj如果是Person类型对象，则强制转换
        if(obj instanceof Person){
            Person person = (Person)obj;
            //比较两个对象的name属性和age属性，如果相等，返回true
            return this.name.equals(person.name) && this.age == person.age;
        }
        return false;}}
```

## 二、DateFormat类

 使用将`日期格式化为字符串`的方法和使用将`字符串转换成日期`的方法

`java.text.DateFormat` 是日期/时间格式化子类的抽象类，我们通过这个类可以帮我们完成日期和文本之间的转换,也就是可以在Date对象与String对象之间进行来回转换。

- **格式化**：按照指定的格式，把Date对象转换为String对象。
- **解析**：按照指定的格式，把String对象转换为Date对象。

- String format(Date date)传递日期对象，返回格式化后的字符串。
- Date parse(String str)传递字符串，返回日期对象。

```java
 public static void main(String[] args) throws ParseException {
     //格式化：从 Date 到 String
     Date d = new Date();
     SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
     String s = sdf.format(d);
     System.out.println(s);
     System.out.println("--------");

     //从 String 到 Date
     String ss = "2048-08-09 11:11:11";
     //ParseException
     SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
     Date dd = sdf2.parse(ss);
     System.out.println(dd);
    }
```

## 三、Calendar日历类

### 3.1 日历对象获取方式

Calendar是抽象类，不能创建对象，需要使用子类对象。`java.util.GregorianCalendar`类是Calendar的子类，但是创建日历对象需要根据本地的时区，语言环境来创建，比较困难，Calendar类提供了静态方法 getInstance()直接获取子类的对象。

`public static Calendar getInstance()`：使用默认时区和语言环境获得一个日历。

```java
Calendar cal = Calendar.getInstance();
```

### 3.2 常用方法

- `public int get(int field)`：返回给定日历字段的值。
- `public void set(int field, int value)`：将给定的日历字段设置为给定值。
- `public abstract void add(int field, int amount)`：根据日历的规则，为给定的日历字段添加或减去指定的时间量。
- `public Date getTime()`：返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象。

### 3.2 日历字段

Calendar类中提供很多静态成员，直接类名调用，代表给定的日历字段：

| 字段值       | 含义                                  |
| ------------ | ------------------------------------- |
| YEAR         | 年                                    |
| MONTH        | 月（从0开始，可以+1使用）             |
| DAY_OF_MONTH | 月中的天（几号）                      |
| HOUR         | 时（12小时制）                        |
| HOUR_OF_DAY  | 时（24小时制）                        |
| MINUTE       | 分                                    |
| SECOND       | 秒                                    |
| DAY_OF_WEEK  | 周中的天（周几，周日为1，可以-1使用） |

代码使用简单演示：

```java
public static void main(String[] args) {
    // 创建Calendar对象
    Calendar cal = Calendar.getInstance();
    // 获取年
    int year = cal.get(Calendar.YEAR);
    //设置年份为2020年
    cal.set(Calendar.YEAR, 2020);
    //将年份修改为2000年
    cal.add(Calendar.YEAR,-20)     
    //将日历对象转换为日期对象
    Date d = cal.getTime();
    System.out.println(d);
}    
```
## 四、冒泡排序

 冒泡排序法是采用数组中相邻元素进行比较换位。

### 4.1 冒泡排序图解

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20200921162427.png)

**代码实现**

```java
public static void main(String[] args) {
    public static void main(String[] args) {
        //定义一个数组
        int[] arr = {7, 6, 5, 4, 3};
        System.out.println("排序前：" + arrayToString(arr));
        // 这里减1，是控制每轮比较的次数
        for (int x = 0; x < arr.length - 1; x++) {
            // -1是为了避免索引越界，-x是为了调高比较效率
            for (int i = 0; i < arr.length - 1 - x; i++) {
                if (arr[i] > arr[i + 1]) {
                    int temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                }
            }
        }
        System.out.println("排序后：" + arrayToString(arr));
    }
```
## 五、数组的二分查找法

所谓数组的二分查找法，对于一个有序数组，查找一个元素是否存在于数组中，如果存在就返回出现的索引，如果不存在就返回负数。

### 5.1 二分查找法原理

取数组中间的元素和被查找的元素进行比较，如果被查找元素大于数组中间元素，就舍去数组元素的一半，对另一半继续进行查找。

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20200921191055.JPG)

**代码实现**

```java
 public static void main(String[] args) {
        int[] arr = {5,13,19,21,37,56,64,75,80,88,92};
        int key = 21;
        int index = binarySearch(arr, key);
        System.out.println(index);

    }
    public static int binarySearch(int[]arr,int key){
        //最小索引
        int min = 0;
        //最大索引
        int max = arr.length - 1;
        //初始化中间索引
        int mid = 0;
        //循环折半，最小索引小于等于最大索引时，才能折半
        while (min <= max){
            //折半，计算中间索引
            mid = (min + max) / 2;
            if(key > arr[mid]){
                //元素大于数组中间索引元素，移动最小索引
                min = mid + 1;
            }else if(key < arr[mid]){
                //元素小于数组中间索引元素，移动最大索引
                max = mid - 1;
            }else{
                //查询到元素，返回索引
                return mid;
            }
        }
        //循环查找结束后，找不到元素，返回-1
        return - 1;
    }
```

## 六、正则表达式

### 6.1 正则规则-字符类

| 规则写法    | 规则含义                                    |
| ----------- | ------------------------------------------- |
| [abc]       | a、b 或 c（简单类）                         |
| [^abc]      | 任何字符，除了 a、b 或 c（否定）            |
| [a-zA-Z]    | a 到 z 或 A到 Z，两头的字母包括在内（范围） |
| [0-9]       | 0到9，两头的数字包括在内（范围）            |
| [a-zA-Z0-9] | a 到 z 或 A到 Z或0-9                        |

### 6.2 正则规则-预定义字符类

| 规则写法 | 规则含义                  |
| -------- | ------------------------- |
| .        | 任何字符                  |
| \d       | 数字[0-9]                 |
| \D       | 非数字 `[^0-9]`           |
| \w       | 单词字符 [a-zA-Z0-9_]     |
| \W       | 非单词字符`[^a-zA-Z0-9_]` |

### 6.3 正则规则-数量词

| 规则写法 | 规则含义                       |
| -------- | ------------------------------ |
| X{?}     | 一次或一次也没有               |
| X{*}     | 零次或多次                     |
| X{+}     | 一次或多次                     |
| X{n}     | 恰好 *n* 次                    |
| X{n,}    | 至少 *n* 次                    |
| X{n,m}   | 至少 *n* 次，但是不超过 *m* 次 |

### 6.4 正则练习-String类matches方法

方法：boolean matches(String regex)传递正则表达式规则，检测字符串是否匹配正则表达式规则，匹配返回true。

需求：检查手机号，检查邮件地址。

分析：

- 手机号：只能1开头，第二位可以是345678任意一个，第三位开始全数字，总长11位。
- 邮件地址：@前面可以是数字，字母，下划线。@后面是字母和.。

```java
public static void main(String[] args){
    //验证手机号码
    String tel = "13800138000";
    String telRegex = "1[345678][0-9]{9}";
    boolean flag = tel.matches(telRegex);
    System.out.println(flag);
    //验证邮件地址
    String email = "s_123456@sina.com.cn.";
    String emailRegex = "[a-zA-Z0-9_]+@([a-z]+\\.[a-z]+)+";
    flag = email.matches(emailRegex);
    System.out.println(flag);
}
```

### 6.5 正则练习-String类split方法

方法：String[] split(String regex)传递正则表达式规则，以正则规则对字符串进行切割。

```java
public static void main(String[] args){
    String str1 = "ab  a   bbb  abc   aa      c";
    //对空格进行切割
    String[] strArr =str1.split(" +");
    System.out.println(Arrays.toString(strArr));

    String str2 = "192.168.22.123";
    strArr = str2.split("\\.");
    System.out.println(Arrays.toString(strArr));
}
```

**注意：输出数组元素时会看到存在一个多余空格，Arrays.toString()方法源码中追加的空格。**