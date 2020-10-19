## day14 【网络编程】

## 今日学习内容-2020.10.16

- 软件架构CS／BS
- 网络通信三要素
- TCP通信
- Socket套接字
- ServerSocket
- 文件上传
- 自定义服务器
- NIO非阻塞IO

## 一、网络编程入门

### 1.1 可以说出软件的结构

**C/S结构** ：全称为Client/Server结构，是指客户端和服务器结构。常见程序有ＱＱ、迅雷等软件。

**B/S结构** ：全称为Browser/Server结构，是指浏览器和服务器结构。常见浏览器有谷歌、火狐等。

### 1.2 可以说出UDP和TCP协议特点

- **TCP**：传输控制协议 (Transmission Control Protocol)。TCP协议是**面向连接**的通信协议，即传输数据之前，在发送端和接收端建立逻辑连接，然后再传输数据，它提供了两台计算机之间可靠无差错的数据传输。
  - 三次握手：TCP协议中，在发送数据的准备阶段，客户端与服务器之间的三次交互，以保证连接的可靠。
    - 第一次握手，客户端向服务器端发出连接请求，等待服务器确认。
    - 第二次握手，服务器端向客户端回送一个响应，通知客户端收到了连接请求。
    - 第三次握手，客户端再次向服务器端发送确认信息，确认连接。整个交互过程如下图所示

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201019152238.jpg)

  完成三次握手，连接建立后，客户端和服务器就可以开始进行数据传输了。由于这种面向连接的特性，TCP协议可以保证传输数据的安全，所以应用十分广泛，例如下载文件、浏览网页等。

- **UDP**：用户数据报协议(User Datagram Protocol)。UDP协议是一个**面向无连接**的协议。传输数据时，不需要建立连接，不管对方端服务是否启动，直接将数据、数据源和目的地都封装在数据包中，直接发送。每个数据包的大小限制在64k以内。它是不可靠协议，因为无连接，所以传输速度快，但是容易丢失数据。日常应用中,例如视频会议、QQ聊天等。

  每次发送的数据最大为64kb。

## 二、TCP通信协议

### 2.1 可以说出TCP协议下两个常用类名称

**在Java中，提供了两个类用于实现TCP通信程序：**

- 客户端：`java.net.Socket` 类表示。创建`Socket`对象，向服务端发出连接请求，服务端响应请求，两者建立连接开始通信。
- 服务端：`java.net.ServerSocket` 类表示。创建`ServerSocket`对象，相当于开启一个服务，并等待客户端的连接。

### 2.2 如何编写TCP协议下字符串数据传输程序

实现TCP通信的客户端程序：

```java
/**
 * 实现TCP通信的客户端程序
 *
 * java.net.Socket 实现客户端的套接字对象,连接对象
 *
 * 需求: 客户端程序和服务器程序实现数据交换
 *
 * 实现步骤:
 *   1: 创建Socket对象 (主动连接服务器)
 *        Socket(String host, int port)
 *
 *   2: OutputStream getOutputStream()
 *      返回套接字中的字节输出流
 *      方法write写入数据, 写到服务器
 *
 *   3: InputStream getInputStream()
 *      返回套接字中的字节输入流
 *       方法read读取数据,读取的是服务器发回来的数据
 *
 *   4: 释放资源
 */
public class TCPClient {
    public static void main(String[] args)throws IOException {
        //创建Socket对象 (主动连接服务器)
        Socket socket = new Socket("127.0.0.1",9000);

        //OutputStream getOutputStream()
        OutputStream out = socket.getOutputStream();
        //字节流的方法写数据
        out.write("你好服务器".getBytes());//没有写到文件中,写到了服务器中

        InputStream  in = socket.getInputStream();//获取字节输入流
        byte[] bytes = new byte[1024];
        int len = in.read(bytes);//客户端输入流,读取到服务器发回来的数据
        System.out.println(new String(bytes,0,len));

        //释放资源
        //out.close();
        socket.close();
    }
}
```

实现TCP通信的服务器程序：

```java
/**
 * 实现TCP通信的服务器程序
 * 192.168.27.95
 * java.net.ServerSocket 实现TCP协议中的服务器套接字对象
 *
 * 实现TCP服务器程序步骤:
 *   1: 创建ServerSocket对象
 *     ServerSocket(int port) 端口号
 *
 *   2: 等待客户端的连接,如果没有客户端连接,永远等待
 *     ServerSocket类方法 accept()同意
 *     方法 accept()返回值是 Socket对象 (客户端的连接对象,包含客户端的IP)
 *
 *   3: Socket对象中获取到字节输出流
 *     OutputStream getOutputStream()
 *     方法write写数据,写到客户端
 *
 *   4: Socket对象中获取到字节输入流
 *     InputStream getInputStream()
 *     方法read读取数据,读取的是客户端发来数据
 *
 *   5: 释放资源
 */
public class TCPServer {
    public static void main(String[] args)throws IOException {
        //创建ServerSocket对象
        ServerSocket server = new ServerSocket(9000);
        //ServerSocket类方法 accept()同意
        Socket socket = server.accept();
        //System.out.println(socket);

        //使用客户端连接获取对象,输入流
        InputStream in = socket.getInputStream();
        //in输入流的方法read()读取客户端发来数据
        byte[] bytes = new byte[1024];
        //len返回的是读取到的字节个数
        int len = in.read(bytes);
        System.out.println(new String(bytes,0,len));

        //Socket客户端对象,获取字节输出流
        OutputStream out = socket.getOutputStream();
        out.write("收到!".getBytes());//服务器输出流,写回客户端

        //释放资源
        socket.close();
        server.close();
    }
}
```

### 2.3 理解TCP协议下文件上传案例

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201019152240.jpg)

实现图片上传客户端：

```java
/**
 * 实现TCP图片上传客户端
 * 实现步骤:
 *  1: 创建Socket对象,连接服务器
 *  2: FileInputStream读取 mm.jpg   字节数组
 *  3: Socket对象获取字节输出流
 *     字节数组,交给输出流,写到服务器
 *  4: Socket对象获取字节输入流
 *     全部的图片发送完毕
 *     输入流,读取服务器回来的提示信息 "上传成功"
 *
 *     客户端方法Socket: shutdownOutput() 终止连接对象的输出流 ,
 *     同时跟上TCP终止序列
 *  5: 释放资源
 */
public class TCPClient {
    public static void main(String[] args) throws IOException{
        //1: 创建Socket对象,连接服务器
        Socket socket = new Socket("127.0.0.1",9000);
        //2: 自己的字节输入流,读取图片mm.jpg
        FileInputStream fis = new FileInputStream("e:/msh.jpg");
        //3: Socket对象获取字节输出流
        OutputStream out = socket.getOutputStream();//任何数据,写向服务器
        byte[] bytes = new byte[1024];
        int len = 0;
        //fis读取文件末尾,JVM返回read() -1
        while ((len = fis.read(bytes))!=-1){
            out.write(bytes,0,len);
        }
        //告诉服务器,没数据了,别读,服务器发送TCP的终止符
        socket.shutdownOutput();
        //4:Socket对象获取字节输入流
        InputStream in = socket.getInputStream();//输入流,读取服务器回来的数据
        len = in.read(bytes);
        System.out.println("服务器提示::"+new String(bytes,0,len));

        socket.close();
    }
}
```

实现图片上传服务器端：

```java
/**
 * 实现图片上传服务器端
 * 实现步骤:
 *  1: 创建对象ServerSocket 绑定端口
 *  2: Socket accept()方法等待客户端连接
 *  3: Socket对象,获取字节输入流,读取客户端发来图片的字节
 *  4: 创建字节输出流,读取到图片,写到 e:/upload
 *  5: Socket对象,获取字节输出流,"上传成功"写回客户端
 *
 *  bug:
 *    1: 演示了2次,为什么文件夹中只有一个文件,同名覆盖
 *      a: 1.jpg
 *      b: 1.jpg
 *
 *   2: 客户端收不到服务器的上传成功
 //告诉服务器,没数据了,别读,服务器发送TCP的终止符
 */
public class TCPServer {
    public static void main(String[] args) throws IOException{
        //1: 创建对象ServerSocket 绑定端口
        ServerSocket server = new ServerSocket(9000);
        // 2: Socket accept()方法等待客户端连接
        Socket socket = server.accept();
        //3: Socket对象,获取字节输入流,读取客户端发来图片的字节
        InputStream in = socket.getInputStream();
        //4: 创建字节输出流,读取到图片,写到 e:/upload
        //上传的文件,重命名,获取当前日期 转换成字符串
        String filename = new SimpleDateFormat("yyyyMMddHHmmssssss").format(Calendar.getInstance().getTime())+".jpg";
        FileOutputStream fos = new FileOutputStream("e:/upload/"+filename);
        byte[] bytes = new byte[1024];
        int len = 0;
        //read()读取是客户端写来的图片!!
        while ( (len = in.read(bytes))!=-1){
            fos.write(bytes,0,len);
        }

        //5: Socket对象,获取字节输出流,"上传成功"写回客户端
        OutputStream out= socket.getOutputStream();
        out.write("上传成功!".getBytes());

        socket.close();
        server.close();
    }
}
```

