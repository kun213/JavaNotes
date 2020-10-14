## day11【IO流】

## 今日学习内容-2020.10.10

- File类
- 递归
- 文件过滤器
- 字节流
- 文件复制
- 字节缓冲流

## 一、File类

### 1.1 可以说出File对象的创建方式

**File类的构造方法**

- public File(String pathname) ` ：通过将给定的**路径名字符串**转换为抽象路径名来创建新的 File实例。  
- `public File(String parent,String child) ` ：从**父路径名字符串和子路径名字符串**创建新的 File实例。
- `public File(File parent, String child)` ：从**父抽象路径名和子路径名字符串**创建新的 File实例。  

代码举例：

```java
/**
 * File类构造方法
 */

public class FileDemo {
    public static void main(String[] args) {
        method_3();
    }
    /**
     * File类构造方法
     *   File(File parent,String child)传递File类型的父路径,和字符串的子路径
     *   第一个参数是File对象
     */
    public static void method_3(){
        File parent = new File("E:\\JavaEE\\IdeaProjects");
        File file = new File(parent,"basic-code");
        System.out.println(file);
    }

    /**
     * File类构造方法
     *  File(String parent,String child)传递字符串的父路径,和字符串的子路径
     *  程序中,分的越开越好
     *  单独操作父路径,单独操作子路径
     */
    public static void method_2(){
        File file = new File("E:\\JavaEE\\IdeaProjects","basic-code");
        System.out.println(file);
    }

    /**
     * File类构造方法
     *   File(String path)传递字符串的路径
     */
    public static void method_1(){
        File file = new File("E:\\JavaEE\\IdeaProjects\\basic-code");
        System.out.println(file);
    }
}
```

**注意**：

1. 一个File对象代表硬盘中实际存在的一个文件或者目录。
2. 无论该路径下是否存在文件或者目录，都不影响File对象的创建。

### 1.2 如何使用File类常用方法

**File类的获取方法**

- `public String getAbsolutePath() ` ：返回此File的绝对路径名字符串。
- ` public String getPath() ` ：将此File转换为路径名字符串。 
- `public String getName()`  ：返回由此File表示的文件或目录的名称。  
- `public long length()`  ：返回由此File表示的文件的长度。 
- `public File getParentFile()`返回由此File表示的文件或目录的父目录，如果没有父目录，返回null。

代码举例：

```java
/**
 * File类的获取方法
 *   基本上都是get开头
 */
public class FileDemo {
    public static void main(String[] args) {
            method_5();
    }
    /**
     *  String getPath() 将构造方法中的路径,转成字符串
     */
    public static void method_5(){
        File file = new File("E:\\JavaEE\\IdeaProjects\\basic-code");
        String path = file.getPath();
        System.out.println(path.toString());
    }

    /**
     *  File getAbsoluteFile() 返回File构造方法中路径的绝对路径形式
     *  返回值是File对象
     *
     *  注意: 直接传递文件名,或者文件夹名
     *  获取到的绝对路径,将从IDEA的工程下计算
     */
    public static void method_4(){
        File file = new File("1.txt");
        File abso = file.getAbsoluteFile();
        System.out.println(abso);
    }

    /**
     * long length() 获取File构造方法中路径表示的文件的字节数
     */
    public static void method_3(){
      File file = new File("E:\\开始学习的Java笔记和代码\\测试io\\1.txt");
        long l = file.length();
        System.out.println(l);
    }
    /**
     * File getParentFile()  获取,构造方法中封装的路径的父路径
     * 上一级文件夹
     * 方法的返回值是File对象,可以方法调用链
     * 如果此路径名没有父目录，则返回 null。
     */
    public static void method_2(){
        File file = new File("E:\\JavaEE\\IdeaProjects\\basic-code");
        File parent = file.getParentFile().getParentFile();//方法调用链
        System.out.println(parent);
    }
    /**
     * String getName() 返回名字
     * 获取了,构造方法中封装路径,最后的名称
     * 名称可能是文件夹名,可能是文件名
     */
    public static void method_1(){
        File file = new File("E:\\JavaEE\\IdeaProjects\\basic-code");
        String name = file.getName();
        System.out.println(name);
    }
}
```

### 1.3 如何辨别相对路径和绝对路径

- **绝对路径**：从盘符开始的路径，这是一个完整的路径，绝对路径具有唯一性。
- **相对路径**：相对于项目目录的路径，这是一个便捷的路径，开发中经常使用。

```java
public static void main(String[] args) {
    // D盘下的bbb.java文件
    File f = new File("D:\\bbb.java");
    System.out.println(f.getAbsolutePath());

    // 项目下的bbb.java文件
    File f2 = new File("bbb.java");
    System.out.println(f2.getAbsolutePath());
}
```

### 1.4 如何遍历文件夹

- `public File[] listFiles()`返回一个File数组，表示该File目录中的所有的子文件或目录、
- `public File[] listFiles(FileFilter filter)`返回一个File数组，表示该File目录中的所有的子文件或目录，filter是文件过滤器，可以过滤不需要的文件。

```java
public static void main(String[] args) {
    File dir = new File("d:\\java_code");
    //获取当前目录下的文件以及文件夹对象，只要拿到了文件对象，那么就可以获取更多信息
    File[] files = dir.listFiles();
    for (File file : files) {
    	System.out.println(file);
    }
}
```

**FileFilter接口**

文件过滤器接口，此接口的实现类可以传递给方法listFiles()，实现文件的过滤功能

FileFilter接口方法：

`public boolean accept(File path)`：方法参数就是listFiles()方法获取到的文件或者目录的路径。如果方法返回true，表示需要此路径，否则此路径将被忽略。

**遍历目录，获取所有的Java文件**

```java
public static void main(String[] args){
    File dir = new File("d:\\demo");
    File[] files = dir.listFiles(new FileFilter() {
    @Override
    public boolean accept(File pathname) {
        //判断如果获取到的是目录，直接放行
        if(pathname.isDirectory())
        return true;
        //获取路径中的文件名，判断是否java结尾，是就返回true
        //.toLowerCase是String 中的所有字符都转换为小写
        return pathname.getName().toLowerCase().endsWith("java");
      }
    });
    for(File file : files){
    	System.out.println(file);
    }
}
```

## 二、方法递归

### 2.1 可以解释递归的含义

```properties
/**
 * 方法的递归调用: 编写程序上一个手段
 * 方法自己调用自己,具有方法语言,都可以使用递归
 * 解决问题:
 *   目录遍历案例,功能是确定的,就是制定目录进行遍历
 *   遍历的目录,每次可能不同
 *
 *   功能的计算主体明确,计算中的参数每次是变化
 *
 *   方法自身调用注意事项:
 *     1: 不能是死递归,方法不停的进栈,不会出去,内存满了,溢出
 *     2: 递归一定要有出口,能结束.但是如果进栈的方法过多也不可以
 *
 */
```

### 2.2 如何使用递归的方式计算5的阶乘

**分析**：分析：n的阶乘：n! = n * (n-1) *...* 3 * 2 * 1 

实现代码：

```java
public class JieChengDemo {
    public static void main(String[] args) {
        int jieCheng = getJieCheng(5);
        System.out.println(jieCheng);
    }
    /**
    通过递归算法实现.
    参数列表:int
    返回值类型: int
     */
    public static int getJieCheng(int num){
         /*
        num为1时,方法返回1,
        相当于是方法的出口,num总有是1的情况
           */
        if (num == 1){
            return 1;
        }else {
             /*
            num不为1时,方法返回 num +(num-1)的累和
            递归调用getJieCheng方法
              */
            return num * getJieCheng(num-1);
        }
    }
```



1~N递归求和的代码执行图解：

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201008211848.jpg)

**注意**：递归一定要有条件限定，保证递归能够停止下来，否则就是死递归。递归次数不要太多，否则会发生栈内存溢出，会抛出StackOverflowError错误。

### 2.3 可以说出使用递归会内存溢出隐患的原因

**函数调用**的参数是通过栈空间来传递的，在调用过程中会占用线程的栈资源。

**递归调用**只有走到最后的结束点后函数才能依次退出，而未到达最后的结束点之前，占用的栈空间一直没有释放，如果递归调用次数过多，就可能导致占用的栈资源超过线程的最大值，从而导致栈溢出，导致程序的异常退出。

## 三、IO流

### 3.1 可以说出IO流的分类和功能

根据数据的流向分为：**输入流**和**输出流**。

- **输入流** ：把数据从`其他设备`上读取到`内存`中的流。 
- **输出流** ：把数据从`内存` 中写出到`其他设备`上的流。

格局数据的类型分为：**字节流**和**字符流**。

- **字节流** ：以字节为单位，读写数据的流。
- **字符流** ：以字符为单位，读写数据的流。

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201008211857.jpg)

## 四、字节流

使用字节流可以进行任何文件的复制，因为字节流操作的是组成文件的最小单元-字节。

复制原理图解：

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201008211907.jpg)

**代码实现**

```java
/**
 * 字节流复制文件,任意文件
 * 不能是文件夹
 */ 
public static void main(String[] args) throws IOException {
     // 1.创建流对象
     // 1.1 指定数据源
     FileInputStream fis = new FileInputStream("D:\\test.jpg");
     // 1.2 指定目的地
     FileOutputStream fos = new FileOutputStream("test_copy.jpg");

     // 2.读写数据
     // 2.1 定义数组
     byte[] b = new byte[1024];
     // 2.2 定义长度
     int len;
     // 2.3 循环读取
     while ((len = fis.read(b))!=-1) {
         // 2.4 写出数据
         fos.write(b, 0 , len);
     }

     // 3.关闭资源
     fos.close();
     fis.close();
}
```

## 五、字节缓冲流

### 5.1 如何使用字节流缓冲流复制文件

缓冲流：针对基础流对象进行高效处理的流对象。或者为基础流增加功能。

**字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream` 

缓冲流的基本原理，是在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。

**构造方法**

- `public BufferedInputStream(InputStream in)` ：创建一个 新的缓冲输入流。 
- `public BufferedOutputStream(OutputStream out)`： 创建一个新的缓冲输出流。

```java
// 创建字节缓冲输入流
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("bis.txt"));
// 创建字节缓冲输出流
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("bos.txt"));
```

**注意**：在使用缓冲流时，必须传递基础流。

代码实现：缓冲流+数组方式：

```java
public static void main(String[] args) throws FileNotFoundException {
    // 记录开始时间
    long start = System.currentTimeMillis();
    // 创建流对象
    BufferedInputStream bis = new BufferedInputStream(new FileInputStream("jdk8.exe"));
    BufferedOutputStream bos = new BufferedOutputStream(new           FileOutputStream("copy.exe"));
    
    // 读写数据
    int len = 0;
    byte[] bytes = new byte[8*1024];
    while ((len = bis.read(bytes)) != -1) {
    	bos.write(bytes, 0 , len);
    }
    
    // 记录结束时间
    long end = System.currentTimeMillis();
    System.out.println("缓冲流使用数组复制时间:"+(end - start)+" 毫秒");
}
缓冲流使用数组复制时间:666 毫秒
```