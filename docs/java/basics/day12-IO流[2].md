## day12【IO流】

## 今日学习内容-2020.10.13

- 字符集
- 字符流
- 转换流
- 字符缓冲流
- 打印流
- 对象序列化
- commons-io

## 一、常用的编码表

### 1.1 可以说出常用的编码表

- **ASCII字符集** ：
  - ASCII（American Standard Code for Information Interchange，美国信息交换标准代码）是基于拉丁字母的一套电脑编码系统，用于显示现代英语，主要包括控制字符（回车键、退格、换行键等）和可显示字符（英文大小写字符、阿拉伯数字和西文符号）。
  - 基本的ASCII字符集，使用7位（bits）表示一个字符，共128字符。ASCII的扩展字符集使用8位（bits）表示一个字符，共256字符，方便支持欧洲常用字符。
- **ISO-8859-1字符集**：
  - 拉丁码表，别名Latin-1，用于显示欧洲使用的语言，包括荷兰、丹麦、德语、意大利语、西班牙语等。
  - ISO-8859-1使用单字节编码，兼容ASCII编码。
- **GBxxx字符集**：
  - GB就是国标的意思，是为了显示中文而设计的一套字符集。
  - **GB2312**：简体中文码表。一个小于127的字符的意义与原来相同。但两个大于127的字符连在一起时，就表示一个汉字，这样大约可以组合了包含7000多个简体汉字，此外数学符号、罗马希腊的字母、日文的假名们都编进去了，连在ASCII里本来就有的数字、标点、字母都统统重新编了两个字节长的编码，这就是常说的"全角"字符，而原来在127号以下的那些就叫"半角"字符了。
  - **GBK**：最常用的中文码表。是在GB2312标准基础上的扩展规范，使用了双字节编码方案，共收录了21003个汉字，完全兼容GB2312标准，同时支持繁体汉字以及日韩汉字等。
    - 中文版操作系统使用的编码表就是GBK。
    - 中文汉字在2312和GBK编码表中均为两个字节表示，第一个字节为负数，第二个字节可能是负数也可能是正数。
  - **GB18030**：最新的中文码表。收录汉字70244个，采用多字节编码，每个字可以由1个、2个或4个字节组成。支持中国国内少数民族的文字，同时支持繁体汉字以及日韩汉字等。尚未正式启用。
- **Unicode字符集** ：
  - Unicode编码系统为表达任意语言的任意字符而设计，是业界的一种标准，也称为统一码、标准万国码。
  - 它最多使用4个字节的数字来表达每个字母、符号，或者文字。有三种编码实现方案，UTF-8、UTF-16和UTF-32。最为常用的UTF-8编码。
  - UTF-8编码，可以用来表示Unicode标准中任何字符，它是电子邮件、网页及其他存储或传送文字的应用中，优先采用的编码。互联网工程工作小组（IETF）要求所有互联网协议都必须支持UTF-8编码。所以，我们开发Web应用，也要使用UTF-8编码。它使用一至四个字节为每个字符编码，编码规则：
    1. 128个US-ASCII字符，只需一个字节编码。
    2. 拉丁文等字符，需要二个字节编码。 
    3. 大部分常用字（含中文），使用三个字节编码。
    4. 其他极少使用的Unicode辅助字符，使用四字节编码。
    5. UTF-8是变长编码表，汉字在UTF-8中均为负数。

## 二、转换流

### 2.1 如何使用OutputStreamWriter写数据到文件

#### 2.1.1 构造方法

- `OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。 
- `OutputStreamWriter(OutputStream in, String charsetName)`: 创建一个指定字符集的字符流。

```java
OutputStreamWriter isr = new OutputStreamWriter(new FileOutputStream("out.txt"));
OutputStreamWriter isr2 = new OutputStreamWriter(new FileOutputStream("out.txt") , "GBK");
```

#### 2.1.2 写出数据方法

- `void write(int c)`写出单个字符。
- `void write(char[] ch)`写出字符数组。
- `void write(char[] ch,int off,int len)`写出字符数组一部分，开始索引和写出的个数。
- `void write(String s)`写入字符串。
- `void flush()`刷新该流的缓冲，字符流写数据会先写在内存中，刷新后才会到达目的文件。

```java
 public static void main(String[] args) throws IOException {
     // 定义文件路径
     String FileName = "E:\\out.txt";
     // 创建流对象,默认UTF8编码
     OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(FileName));
     // 写出数据
     osw.write("你好"); // 保存为6个字节
     osw.flush();
     osw.close();

    // 定义文件路径
    String FileName2 = "E:\\out2.txt";
    // 创建流对象,指定GBK编码
    OutputStreamWriter osw2 = new OutputStreamWriter(new FileOutputStream(FileName2),"GBK");
    // 写出数据
    osw2.write("你好");// 保存为4个字节
    osw2.flush();     
    osw2.close();
}
```

### 2.2 如何使用InputStreamReader读取数据

#### 2.2.1 构造方法

- `InputStreamReader(InputStream in)`: 创建一个使用默认字符集的字符流。 
- `InputStreamReader(InputStream in, String charsetName)`: 创建一个指定字符集的字符流。

```java
InputStreamReader isr = new InputStreamReader(new FileInputStream("in.txt"));
InputStreamReader isr2 = new InputStreamReader(new FileInputStream("in.txt") , "GBK");
```

#### 2.2.2 读取数据方法

- `int read()`读取单个字符，读取到文件末尾时返回-1。
- `int read(char[] ch)`读取字符存储到数组中个，返回读取到的字符个数，读取到文件末尾时返回-1。

```java
public static void main(String[] args) throws IOException {
    // 定义文件路径,文件为gbk编码
    String FileName = "E:\\file_gbk.txt";
    // 创建流对象,默认UTF8编码
    InputStreamReader isr = new InputStreamReader(new FileInputStream(FileName));
    // 创建流对象,指定GBK编码
    InputStreamReader isr2 = new InputStreamReader(new FileInputStream(FileName) , "GBK");
    // 定义变量,保存字符
    int read;
    // 使用默认编码字符流读取,乱码
    while ((read = isr.read()) != -1) {
    	System.out.print((char)read); 
    }
    isr.close();

    // 使用指定编码字符流读取,正常解析
    while ((read = isr2.read()) != -1) {
    	System.out.print((char)read);// 大家好
    }
    isr2.close();
}
```

## 三、字符流便捷类

### 3.1 FileWriter类

#### 3.1.1 如何使用FileWirter写数据到文件

**构造方法**

- `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。   
- `FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。  

**写出数据**

```java
public static void main(String[] args) throws IOException {
    // 使用文件名称创建流对象
    FileWriter fw = new FileWriter("fw.txt");
    
    //写出单个字符
    fw.write(97),
    fw.flush();
    
    //写出字符数组
    char[] chs = "CodeBull".toCharArray();
    fw.write(chs);
    fw.flush();
    
    //写出字符数组的一部分，开始索引，写出个数
    fw.write(chs,1,3);
    fw.flush();
    
    //写出字符串
    fw.write("CodeBull");
    fw.flush();
    fw.close();
}
```

**注意**：字符串输出数据，必须要使用flush()方法，否则数据在内存中，不会到达指定文件，close()方法在关闭前也会进行刷新，推荐写一次刷新一次，避免内存占用过多。

#### 3.1.2可以说出FileWriter中关闭和刷新方法的区别

因为内置缓冲区的原因，如果不关闭输出流，无法写出字符到文件中。但是关闭的流对象，是无法继续写出数据的。如果我们既想写出数据，又想继续使用流，就需要flush 方法了。

**close()和flush()方法的区别**
        flush():刷新缓冲区。流对象还可以继续使用。

​        close():先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。

#### 3.1.3 如何使用FileWriter写数据实现换行和追加写

**构造方法**

- `FileWriter(File file, boolean append)`: 根据给定的 File 对象构造一个 FileWriter 对象。
- `FileWriter(String fileName, boolean append)`: 根据给定的文件名以及指示是否附加写入数据的 boolean 值来构造 FileWriter 对象。

**写数据实现追加写**

```java
/**
 * 字节流写入数据: 追加和换行
 * 流对象构造方法,第二个参数true
 * 换行问题:
 *   Windows:  \r\n
 *   Linux:    \n
 *   Mac:      \r
 */
    public static void main(String[] args) 
throws IOException {
        FileWriter fw = new FileWriter("day12-code/1.txt",true);
        fw.write("第一行\n");
        fw.write("第二行");
        fw.write("第三行");
        fw.close();
    }
}
```

### 3.2 FileReader类

#### 3.2.1 如何使用FileReader读数据

**构造方法**

- `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。   
- `FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。  

**读取数据**

```java
 public static void main(String[] args) throws IOException {
     // 使用文件名称创建流对象
     FileReader fr = new FileReader("read.txt");
     // 定义变量，保存有效字符个数
     int len ；
     // 定义字符数组，作为装字符数据的容器
     char[] cbuf = new char[2];
     // 循环读取
     while ((len = fr.read(cbuf))!=-1) {
         System.out.println(new String(cbuf,0,len));
     }
     // 关闭资源
     fr.close();
 }
```

## 四、字符缓冲流

### 4.1 如何使用BufferedWriter写换行

**构造方法**

- `BufferedWriter(Writer out)`： 创建一个新的缓冲输出流。

```java
// 创建字符缓冲输出流
BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
```

**特有方法**

- BufferedWriter：`public void newLine()`: 写一行行分隔符,由系统属性定义符号。

```java
public static void main(String[] args) throws IOException  {
    // 创建流对象
    BufferedWriter bw = new BufferedWriter(new FileWriter("out.txt"));
    // 写出数据
    bw.write("CodeBull");
    // 写出换行
    bw.newLine();
    bw.write("加油");
    bw.newLine();
    bw.write("努力");
    bw.newLine();
    // 释放资源
    bw.close();
}
```

### 4.2 如何使用BufferedReader读取文本行

**构造方法**

- `BufferedReader(Reader in)` ：创建一个 新的缓冲输入流。 

```java
// 创建字符缓冲输入流
BufferedReader br = new BufferedReader(new FileReader("br.txt"));
```

**特有方法**

- BufferedReader：`public String readLine()`: 读一行文字。 

```java
public static void main(String[] args) throws IOException {
    // 创建流对象
    BufferedReader br = new BufferedReader(new FileReader("in.txt"));
    // 定义字符串,保存读取的一行文字
    String line  = null;
    // 循环读取,读取到最后返回null
    while ((line = br.readLine())!=null) {
    System.out.print(line);
    System.out.println("------");
    }
    // 释放资源
    br.close();
}
```

## 五、打印流

**打印流的特点**

- 打印流指负责输出数据，不负责数据来源。
- 打印流永远不会抛出IOException。
- 使用PrintWriter打印流，可以开启自动刷新功能。
  - 调用println，printf，format三个方法中的一个才能自动刷新。