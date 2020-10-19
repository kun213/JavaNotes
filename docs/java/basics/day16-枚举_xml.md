

## day16 【枚举、XML】

## 今日学习内容-2020.10.19

- 枚举
- xml文档
- DTD约束
- Schema约束
- DOM解析
- Xpath表达式

## 一、枚举

### 1.1 如何自定义枚举常量

- 格式：`public enmu 枚举名{}`

- 枚举常量定义：

  - 枚举中的常量名字大写，多个常量之间逗号分开，最后一个常量可以写分号，也可以不写。每一个常量，都表示这个类的对象。修饰符为`public static final`。
  - 枚举中有默认的无参数的private修饰的构造方法，如果手写构造方法，也必须是私有修饰的。而且构造方法必须写在常量的后面，这时最后一个常量就必须要写分号。

  枚举的使用：`由于枚举的常量为静态修饰可以直接枚举名.调用`

```java
public enum  Color {
    //枚举的静态常量
    RED,GREEN,YELLOW
}
```

```java
public enum Color{
	//枚举静态常量，直接为变量color赋值
	RED("红色"),GREEN("绿色"),YELLOW("黄色");
	private String color;
	private Color(String color){
		this.color = color ;
	}
    //省略get/set
}
```

## 二、XML

### 2.1 可以说出XML的作用

编程中一般用来存放配置文件，如需修改配置文件，只需修改xml即可，不需要去编译了java源文件了，使代码有了良好的拓展性，把所有的配置信息放在一起就可以对整个项目进行调度，同时可读性也变强了。

### 2.2 知道XML的组成元素

```properties
<?xml version="1.0" encoding="utf-8" ?>
<!--标签文档注释-->
<!--
   XML文档声明格式：<?xml version="1.0" encoding="UTF-8"?>
   XML文档的组成部分: 标签,元素 Element
   语法: <标签名>  </标签名>  开始,和结束(闭合)
   语法: <标签名 /> 自闭合标签
   命名: 不要是 xml XML Xml
   注意: 一个格式良好的XML文档,必须有一个根元素,根标签

   标签中,写入内容 (标签体)
     包含普通的文本字符串
     包含子标签
-->
<!--
    XML文档的组成部分: 标签的属性: Attribute
    语法:
      属性只能写在标签里面,属于标签的
      只能写在开始标签中,不能写在结束的标签中
      属性之间,空格分开
      属性必须有属性值,属性值使用""包裹
      一个标签中的属性不能同名
-->
<!--
  字符   转义字符
   <      &lt;
   >      &gt;
   &      &amp;
-->

<student name="张三" id="001">
      1&lt;4 ? 5 : 6
</student>
```

## 三、XML约束

### 3.1 可是说出有哪些XML约束技术

#### 3.1.1 DTD约束

**什么是DTD**

DTD（Document Type Definition），文档类型定义，用来约束XML文档。规定XML文档中元素的名称，子元素的名称及顺序，元素的属性等。

**DTD重点要求**

开发中，我们很少自己编写DTD约束文档，通常情况我们都是通过框架提供的DTD约束文档，编写对应的XML文档。常见框架使用DTD约束有：SpringMVC、MyBatis等。

#### 3.1.2 Schema约束

**什么是Schema**

Schema是新的XML文档约束；
Schema要比DTD强大很多，是DTD 替代者；
Schema本身也是XML文档，但Schema文档的扩展名为xsd，而不是xml。
Schema 功能更强大，数据类型更完善
Schema 支持名称空间

**Schema重点要求**

与DTD一样，要求可以通过schema约束文档编写xml文档。常见框架使用schema的有：Spring等

**声明命名空间**

```properties
 <!--
    xmlns="约束文档的命名空间的名字"  W3C万维网联盟, 名字必须做到全球唯一性(互联网地址)
    xmlns:xsi="地址"是W3C的官方约束文档
    xsi:schemaLocation="属性值标明了约束文档的位置所在"
    http://www.itcast.cn/bean  命令空间的名字
    bean-schema.xsd 是约束文档的路径和文件名
-->
```

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201019151536.png)

## 四、XML解析

### 4.1可以说出解析XML文档DOM方式原理

DOM解析器一次性把整个xml文档加载进内存，然后在内存中构建一颗Document的对象树，通过Document对象，得到树上的节点对象，通过节点对象访问（增删改查操作）到xml文档的内容。

缺点：`当文件结构较大或者数据较复杂的时候，可能出现内存的溢出`

![](https://cdn.jsdelivr.net/gh/kun213/picbed/img/20201019151643.png)

### 4.2 如何通过Dom4j解析XML文档

如果想要使用DOM4J，需要引入支持xpath的jar包 `dom4j-1.6.1.jar`

DOM4J必须使用核心类SaxReader加载xml文档获得Document，通过Document对象获得文档的根元素，然后就可以操作了。

xml文件：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--
    根标签 beans
    两个子标签 bean
-->
<beans>
    <!--
       bean标签含有2个属性
       id属性, className属性
       含有2个子标签property
    -->
    <bean id="001" className="User">
        <!--
           bean的子标签 property
           property的属性 name 和 value
        -->
        <property name="user" value="jack">杰克</property>
        <property name="user" value="rose">露丝</property>
    </bean>

    <bean id="002" className="admin">
        <property name="admin" value="admin">管理员</property>
        <property name="admin" value="manger">经理</property>
    </bean>
</beans>
```

解析xml代码：

```java
/**
 *  使用DOM思想,读取xml文件
 *  介绍dom4j 核心类
 *  1: SAXReader  DOM解析思想的核心类
 *      方法: read(绑定了这个xml文件的输入流) 读取xml文件
 *      返回文档对象,返回值是Document对象
 *
 *  2: Document对象
 *    方法: getRootElement()获取文档的根标签
 *    返回值,返回的是标签对象 Element
 *
 *  3:Element标签对象
 *    方法获取子标签  List<Element>  elements()
 *    返回所有的子标签集合List
 *
 *    方法 String attributeValue(String 属性名) 获取属性值
 *    方法 String getText() 获取到的是,标签体的文本部分
 */
public class DOMReaderXml {
    public static void main(String[] args) throws Exception{
        //使用类加载器,获取输入流,绑定xml文件
        InputStream inputStream =
                DOMReaderXml.class.getClassLoader().getResourceAsStream("beans.xml");
        //核心类,对象
        SAXReader saxReader = new SAXReader();
        //对象sax的方法read()传递字节输入流,返回Document对象
        Document document = saxReader.read(inputStream);
        //document对象的方法,获取根标签
        Element rootElement = document.getRootElement();
        //获取根标签的2个子标签bean
        List<Element> beanElements = rootElement.elements();
        //先对集合进行判断,然后遍历
        //集合不能是null,集合的长度>0
        if(beanElements!=null && beanElements.size()>0){
            for(Element beanElement :beanElements){
                //获取Bean标签的属性id和className
                String id = beanElement.attributeValue("id");
                String className = beanElement.attributeValue("className");
                System.out.println("--------------");
                System.out.println(id+"::"+className);
                //bean标签下,还有2个子标签property
                //beanElement bean标签对象,获取他的子标签
                List<Element> propertyElements = beanElement.elements();
                //集合不能是null,集合的长度>0
                if(propertyElements!=null && propertyElements.size()>0){
                    for (Element propertyElement : propertyElements){
                        // propertyElement就是获取的bean的子标签property
                        //property标签的属性name和value
                        String name = propertyElement.attributeValue("name");
                        String value = propertyElement.attributeValue("value");
                        //property标签的文本
                        String text = propertyElement.getText();
                        System.out.println(name+"::"+value+"::"+text);
                    }
                }
            }
        }
    }
}
```

### 4.3 如何使用xpath解析HTML文档

DOM4J在解析XML时只能一层一层解析，所以当XML文件层数过多时使用会很不方便，结合XPATH就可以直接获取到某个元素

默认的情况下，dom4j不支持xpath，如果想要在dom4j里面使用xpath，需要引入支持xpath的jar包 `jaxen-1.1.6.jar`

在dom4j里面提供了两个方法，用来支持xpath

```java
List<Node> selectNodes("xpath表达式")，用来获取多个节点
Node selectSingleNode("xpath表达式")，用来获取一个节点
```

xml 文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<students>
    <student number="codebull_0001">
        <name id="lk">
            张三
            <xing>张</xing>
            <ming>三</ming>
        </name>
        <age>18</age>
        <sex>male</sex>
    </student>
    <student number="codebull_0002">
        <name id="lk1">jack</name>
        <age>18</age>
        <sex>female</sex>
    </student>
</students>
```

写xpath代码解析xml文件：

```java
/**
 *  xpath表达式,快速精准定位xml
 *  不能单独使用,依靠dom4j (Document)
 *
 *  表达式:
 *    /AAA/DDD/BBB： 表示一层一层的，AAA下面 DDD下面的BBB
 *    //BBB： 表示和这个名称相同，表示只要名称是BBB 都得到
 *    //BBB[@id]： 表示只要BBB元素上面有id属性 都得到
 *   //BBB[@id='b1'] 表示元素名称是BBB,在BBB上面有id属性，并且id的属性值是b1
 *
 *    xpath的方法支持
 *      Element对象的方法:
 *        List<Node> selectNodes(//BBB)找文档中的所有BBB标签元素
 *        Node selectSingleNode(//BBB)找文档中的第一个BBB标签元素
 *
 */
public class XPathReadXml {
    public static void main(String[] args) throws Exception {
        //类的加载器,获取字节输入流
        InputStream inputStream =
                XPathReadXml.class.getClassLoader().getResourceAsStream("student.xml");
        //DOM4J核心对象
        SAXReader saxReader = new SAXReader();
        //read()方法传递字节输入流,返回Document对象
        Document document = saxReader.read(inputStream);
        //获取根标签
        Element rootElement = document.getRootElement();

        //获取age标签,student/age
        List<Node> list = rootElement.selectNodes("/students/student/age");
        for (Node node : list){
            System.out.println(node.getText());
        }

        //获取标签sex,xpath最常用的一个形式 //sex
        //返回的是Node节点对象
        Element nodes = (Element) rootElement.selectSingleNode("//sex");
        System.out.println(nodes.getText());

        //获取标签是name,属性是id,有这个属性就行
        List<Node> list1 = rootElement.selectNodes("//name[@id]");
        for (Node node : list1){
            System.out.println(node.getText());
        }

        //获取标签是name,属性是id,属性值必须是lk
        List<Node> list2 = rootElement.selectNodes("//name[@id='lk1']");
        for (Node node : list2){
            System.out.println(node.getText());
        }
    }
}
```