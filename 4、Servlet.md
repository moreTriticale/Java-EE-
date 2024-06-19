###  Servlet简介

Servlet是Server Applet的简称，称为服务端小程序，是JavaEE平台下的技术标准，基于Java语言边写的服务端程序，Web容器或应用服务器实现了Servlet标准所以Servlet需要运行在Web容器或应用服务器中。Servlet主要功能在于能够在服务器中执行并生成数据。

- Servlet技术特点：Servlet使用单进程多线程方式运行



### 创建第一个Servlet案例

#### 创建Servlet

```java
package com.triticale.servlet;
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
public class HelloWorld extends HttpServlet{
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException{
        PrintWriter pw = response.getWriter();
        pw.println("<html><body>");
        pw.println("<h1> " + "Hello World" + " </h1>");
        pw.println("</body></html>");
        pw.flush();
        pw.close();
    }
}
```

#### 编译Servlet

```
javac -classpath "D:\apache-tomcat-10.1.19\lib\servlet-api.jar" HelloWorld.java
```

#### 创建web.xml

Web项目的部署描述文件，是JavaWeb工程的配置文件，通过web.xml文件可以配置servlet、fiter等技术。Tomcat启动时会先解析该配置文件获取项目的配置信息



web.xml文件中的头信息

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
</web-app>
```

在web.xml文件中配置Servlet

```xml
	<servlet>
        <servlet-name>HelloWorld</servlet-name>
        <servlet-class>com.triticale.servlet.HelloWorld</servlet-class>
    </servlet>
	<servlet-mapping>
    <servlet-name>HelloWorld</servlet-name>
    <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```

### Web工程目录结构

![](.\img\image-20240402193456582.png)

- webapps:tomcat部署web项目的根目录
- project：web项目的根目录，目录名称自定义
- WEB-INF：存放所有web资源的根目录
- classes：存放已编译完的class文件目录，在该目录中存放class文件需要满足包的结构
- lib：存放项目中所依赖的第三方jar包目录
- web.xml：项目的部署描述文件



### Tomcat运行过程

1、用户访问localhost:8080/test/helloword，请求被发送到Tomcat，被监听8080端口并处理HTTP/1.1协议的Connector获得

2、Connector把该请求交给它所在的Service的Engine来处理，并等待Engine的回应

3、Engine获得请求localhost/test/helloword，匹配所有的虚拟主机Host

4、Engine匹配到名为localhost的Host虚拟主机来处理localhost/test/helloword请求（即使匹配不到会请求交给默认Host处理）

5、匹配到的Context获得请求/helloword

6、构造HttpServletRequest对象和HttpServletRespondse对象，作为参数调用HelloWorld的doGet()或doPost()方法，执行业务逻辑、数据存储等程序

7、Context把执行完之后的结果通过HttpServletResponse对象返回给Host

8、Host把HttpServletResponse返回给Engine

9、Engine把HttpServletResponse对象返回Connector



### Servlet继承结构

![](.\img\image-20240402201935863.png)

Servlet接口

- init(),创建Servlet对象后立即调用该方法完成一些初始化工作
- service(),处理客户端请求，执行业务操作，利用响应对象响应客户端请求
- destroy(),在销毁Servlet对象之前调用该方法，释放资源
- getServletConfig(),ServletConfig是容器向Servlet传递参数的载体
- getServletInfo(),获取servlet相关信息

ServletConfig接口

- String getServletName()，返回Servlet名字，即web.xml中\<servlet-name>元素的值
- ServletContext getServletContext(),返回一个代表当前Web应用的ServletContext对象
- String getInitParameter(String name),根据初始化参数名返回对应的初始化参数值
- Enumeration getInitParameterNames(),返回一个Enumeration对象，其中包含了所有的初始化参数名



GenericServle抽象类

GenericServle是实现了Servlet接口的抽象类。在GenericServle中进一步的定义了Servlet接口的具体实现，其设计目的是为了和应用层协议解耦，在GenericServle中包含一个Service抽象方法



HttpServlet抽象类

继承自GenericServle，针对于处理HTTP协议的请求所定制。在HttpServlet的service()方法中已经把ServletRequest和ServletResponse转为HttpServletRequest和HttpServletResponse。直接使用HttpServletRequest和HttpServletResponse，不再需要强转，实际开发中，直接继承HttpServlet，并根据请求方式复写doXxx()方法即可



### Servlet的生命周期

Servlet的生命周期是由容器管理的，分别经历三阶段

init():初始化

service():服务

destory():销毁

当客户端浏览器第一次请求Servlet时，先读web.xml文件加载servlet类，容器会实例化这个Servlet，然后调用一次init方法，并在==新的线程==中执行service方法处理请求。service()方法执行完毕后容器不会销毁这个Servlet而是做缓存处理，当客户端浏览器再次请求这个Servlet时，容器会从缓存中直接找到这个Servlet对象，并再一次在新的线程中执行Servlet方法。当容器在销毁Servlet之前调用一次destroy方法



### Servlet的作用

- 获取用户提交的数据
- 获取浏览器附加的信息
- 处理数据（访问数据库或调用接口）
- 给浏览器产生一个响应
- 在响应中添加附加信息



### Idea中的web项目部署详解

在Idea中默认的并不会把web项目真正的部署到Tomcat的webapps目录中，而是通过为每个web项目创建一个独立的Tomcat副本并在Tomcat副本中通过Tomcat的Context组件完成项目的目录指定，在Context组件的docBase属性中会指定Idea对web项目编译后的目录target/classes/......



### HttpServletRequest对象

HttpServletRequest对象代表客户端浏览器的请求，当客户端浏览器通过HTTP协议访问服务器时，HTTP请求中的所有信息都会被Tomcat所解析并封装在这个对象中，通过这个对象提供的方法，可以获得客户端请求的所有信息

#### 设置请求和响应编码

请求的数据包基于字节在网络上传输，Tomcat接受到请求的数据包后会将数据包中的字节转换为字符。在Tomcat中使用的是ISO-8859-1的单字节编码完成字节与字符的转换，所以数据中含有中文就会出现乱码，可以通过request.setCharacterEncoding("utf-8")方法来对提交的数据根据指定的编码方式重新做编码处理

```java
response.setContentType("text/html;charset=utf-8");
request.setCharacterEncoding("utf-8");
```



#### 获取请求信息

```java
request.getRequestURL();
//返回客户端浏览器发出请求时的完整URL
```

```java
request.getRequestURI();
//获取请求行中指定资源部分
```

```java
request.getRemoteAddr();
//获取发出请求的客户机的IP地址
```

```java
request.getLocalAddr();
//返回WEB服务器的IP地址
```

```java
request.getLocalPort();
//返回WEB服务器处理Http协议的连接器所监听的端口
```



#### 获取请求数据

##### 获取表单信息

根据key获取指定value

request.getParameter("key")

根据key获取对应的value，返回一个字符串

```java
String str = request.getParameter("key");
```

```html
<form action="read" method="post">
  蛋糕数量:<input type="text" name="cake_number"> <br/>
  蛋糕名称:<input type="text" name="cake_name"> <br/>
  蛋糕详细信息<input type="text" name="description">  <br/>
  <input type="submit" value="提交">
</form>
```

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String cakeNumber = request.getParameter("cake_number");
        String cakeName = request.getParameter("cake_name");
        String description = request.getParameter("description");
        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("utf-8");
        PrintWriter out =response.getWriter();
        out.println("蛋糕名称："+ cakeName);
        out.println("蛋糕数量："+ cakeNumber);
        out.println("蛋糕描述："+ description);
        out.flush();
        out.close();
    }
```

##### 获取复选框(checkbox组件)中的值

request.getParameterValues("checkboxkey")

```html
<form action="read" method="post">
  上下层蛋糕形状：<input type="checkbox" name="cake_shape" value="Roundness">圆形<br>
               <input type="checkbox" name="cake_shape" value="Quadrate">方形<br>
               <input type="checkbox" name="cake_shape" value="Triangle">三角形<br>
               <input type="checkbox" name="cake_shape" value="Shape">不规则形<br>
  <input type="submit" value="提交">
</form>
```

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("utf-8");
        String[] cakeShape = request.getParameterValues("cake_shape");
        PrintWriter out =response.getWriter();
        for (String x : cakeShape){
            out.println(x);
        }
        out.flush();
        out.close();
    }
```

##### 获取所有提交数据的key

request.getParameterNames()

获取请求中所有数据的key，该方法返回一个枚举类型

```java
Enumeration<String> parameterNames = request.getParameterNames();
```

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("utf-8");
        Enumeration<String> parameterNames = request.getParameterNames();
        List<String> list = new ArrayList<>();
        while(parameterNames.hasMoreElements()){
            list.add(parameterNames.nextElement());
        }
        PrintWriter out = response.getWriter();
        out.println("Names" + list);
    }
```

##### 使用map结构获取提交数据

request.getParameterMap()

获取请求中所有的数据并存放在一个Map结构中，该方法返回一个Map，其中key为String类型，value为String[]类型

```java
Map<String,String[]> parameterMap = request.getParagrameterMap();
```

```java
Map<String,String[]> map = request.getParameterMap();
Iterator<Map.Entry<String,String[]>> iterator = map.entrySet().iterator();
PrintWriter out = response.getWriter();
while(iterator.hasNext()){
	Map.Entry<String,String[]> entry = iterator.next();
	String key = entry.getKey();
	String[] value = entry.getValue();
	out.println(key + "=" + Arrays.asList(value));
}
```

#### 资源访问路径

- 绝对路径

  绝对路径访问资源表示直接以“/”作为项目的Context Path。该方式适用于以“/”作为项目的Context Path

  ```html
  <form action="/getInfo.do" method="post">
  ```

- 相对路径

  相对路径访问资源表示会相对于项目的Context Path作为相对路径。该方式适用于为项目指定的具体的Context Path

  ```html
  <form action="getInfo.do" method="post">
  ```

  

#### 获取请求头信息

request.getHeader("headerKey")

根据请求头中的key获取对应的value

```java
String headerValue = request.getHeader("headerKey");
```

request.getHeaderNames()

获取请求头中所有的key，该方法返回枚举类型

```java
Enumeration<String> headerNames = request.getHeaderNames();
```

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html;charset=utf-8");
	request.setCharacterEncoding("utf-8");
	Enumeration<String> headerNames = request.getHeaderNames();
	List<String> list = new ArrayList<>();
	PrintWriter out = response.getWriter();
	while(headerNames.hasMoreElements()){
		list.add(headerNames.nextElement());
	}
	for(String x : list){
		out.println(x + ":" + request.getHeader(x)+"<br>");
	}
}
```



#### 获取请求头案例

需求：编写一个Servlet，如果浏览器的语言是zh-CN，显示”您好，聪明的中国人！“，如果浏览器的语言设置为en-US，那么则显示”Hello，American“

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html;charset=utf-8");
	request.setCharacterEncoding("utf-8");

	String language = request.getHeader("Accept-Language");
	PrintWriter out = response.getWriter();
	if(language.startsWith("zh-CN")){
		out.println("您好，聪明的中国人!");
	}else if(language.startsWith("en-US")){
		out.println("Hello,American!");
	}else{
	out.println("抱歉，系统识别错误");
	}
	out.flush();
	out.close();
}
```

#### HttpServletRequest对象的生命周期

当有请求到达Tomcat时，Tomcat会创建HttpServletRequest对象，并将该对象通过参数的方式传递到我们的Servlet的方法中，当处理请求处理完毕并产生响应后该对象生命周期结束。

#### HttpServletResponse对象

HttpServletResponse对象代表服务器的响应。这个对象中封装了响应客户端浏览器的流对象，以及向客户端浏览器响应的响应头、响应数据、响应状态码等信息。



#### 设置响应类型

response.setContentType("MIME")

该方法可通过MIME-Type设置响应类型

| 媒体类型                                                     | 文件扩展名             | 说明                                                         |
| :----------------------------------------------------------- | :--------------------- | :----------------------------------------------------------- |
| **application/msword**                                       | doc                    | 微软 Office Word 格式（Microsoft Word 97 - 2004 document）   |
| **application/vnd.openxmlformats-officedocument.wordprocessingml.document** | docx                   | 微软 Office Word 文档格式                                    |
| **application/vnd.ms-excel**                                 | xls                    | 微软 Office Excel 格式（Microsoft Excel 97 - 2004 Workbook   |
| **application/vnd.openxmlformats-officedocument.spreadsheetml.sheet** | xlsx                   | 微软 Office Excel 文档格式                                   |
| **application/vnd.ms-powerpoint**                            | ppt                    | 微软 Office PowerPoint 格式（Microsoft PowerPoint 97 - 2003 演示文稿） |
| **application/vnd.openxmlformats-officedocument.presentationml.presentation** | pptx                   | 微软 Office PowerPoint 文稿格式                              |
| **application/x-gzip**                                       | gz, gzip               | GZ 压缩文件格式                                              |
| **application/zip**                                          | zip, 7zip              | ZIP 压缩文件格式                                             |
| **application/rar**                                          | rar                    | RAR 压缩文件格式                                             |
| **application/x-tar**                                        | tar, tgz               | TAR 压缩文件格式                                             |
| **application/pdf**                                          | pdf                    | PDF 是 Portable Document Format 的简称，即便携式文档格式     |
| **application/rtf**                                          | rtf                    | RTF 是指 Rich Text Format，即通常所说的富文本格式            |
| **image/gif**                                                | gif                    | GIF 图像格式                                                 |
| **image/jpeg**                                               | jpg, jpeg              | JPG(JPEG) 图像格式                                           |
| **image/jp2**                                                | jpg2                   | JPG2 图像格式                                                |
| **image/png**                                                | png                    | PNG 图像格式                                                 |
| **image/tiff**                                               | tif, tiff              | TIF(TIFF) 图像格式                                           |
| **image/bmp**                                                | bmp                    | BMP 图像格式（位图格式）                                     |
| **image/svg+xml**                                            | svg, svgz              | SVG 图像格式                                                 |
| **image/webp**                                               | webp                   | WebP 图像格式                                                |
| **image/x-icon**                                             | ico                    | ico 图像格式，通常用于浏览器 Favicon 图标                    |
| **application/kswps**                                        | wps                    | 金山 Office 文字排版文件格式                                 |
| **application/kset**                                         | et                     | 金山 Office 表格文件格式                                     |
| **application/ksdps**                                        | dps                    | 金山 Office 演示文稿格式                                     |
| **application/x-photoshop**                                  | psd                    | Photoshop 源文件格式                                         |
| **application/x-coreldraw**                                  | cdr                    | Coreldraw 源文件格式                                         |
| **application/x-shockwave-flash**                            | swf                    | Adobe Flash 源文件格式                                       |
| **text/plain**                                               | txt                    | 普通文本格式                                                 |
| **application/x-javascript**                                 | js                     | Javascript 文件类型                                          |
| **text/javascript**                                          | js                     | 表示 Javascript 脚本文件                                     |
| **text/css**                                                 | css                    | 表示 CSS 样式表                                              |
| **text/html**                                                | htm, html, shtml       | HTML 文件格式                                                |
| **application/xhtml+xml**                                    | xht, xhtml             | XHTML 文件格式                                               |
| **text/xml**                                                 | xml                    | XML 文件格式                                                 |
| **text/x-vcard**                                             | vcf                    | VCF 文件格式                                                 |
| **application/x-httpd-php**                                  | php, php3, php4, phtml | PHP 文件格式                                                 |
| **application/java-archive**                                 | jar                    | Java 归档文件格式                                            |
| **application/vnd.android.package-archive**                  | apk                    | Android 平台包文件格式                                       |
| **application/octet-stream**                                 | exe                    | Windows 系统可执行文件格式                                   |
| **application/x-x509-user-cert**                             | crt, pem               | PEM 文件格式                                                 |
| **audio/mpeg**                                               | mp3                    | mpeg 音频格式                                                |
| **audio/midi**                                               | mid, midi              | mid 音频格式                                                 |
| **audio/x-wav**                                              | wav                    | wav 音频格式                                                 |
| **audio/x-mpegurl**                                          | m3u                    | m3u 音频格式                                                 |
| **audio/x-m4a**                                              | m4a                    | m4a 音频格式                                                 |
| **audio/ogg**                                                | ogg                    | ogg 音频格式                                                 |
| **audio/x-realaudio**                                        | ra                     | Real Audio 音频格式                                          |
| **video/mp4**                                                | mp4                    | mp4 视频格式                                                 |
| **video/mpeg**                                               | mpg, mpe, mpeg         | mpeg 视频格式                                                |
| **video/quicktime**                                          | qt, mov                | QuickTime 视频格式                                           |
| **video/x-m4v**                                              | m4v                    | m4v 视频格式                                                 |
| **video/x-ms-wmv**                                           | wmv                    | wmv 视频格式（Windows 操作系统上的一种视频格式）             |
| **video/x-msvideo**                                          | avi                    | avi 视频格式                                                 |
| **video/webm**                                               | webm                   | webm 视频格式                                                |
| **video/x-flv**                                              | flv                    | 一种基于 flash 技术的视频格式                                |

##### 设置字符型响应

常见的字符型响应类型

response.setContentType("text/html")

设置响应类型为文本型，内容含有html字符串，是默认的响应类型

response.setContentType("text/plain")

设置响应类型为文本型，内容是普通文本

response.setContentType("text/json")

设置响应类型为JSON格式的字符串



##### 设置字节型响应

常见的字节型响应

response.setContentType("image/jpeg")

设置响应类型为图片类型，图片类型为jpeg或jpg格式



response.setContentType("image/gif")

设置响应类型为图片类型，图片格式为gif格式

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("image/jpeg");
	request.setCharacterEncoding("utf-8");
    File file = new File("d:/苍雪飞光_4K_cb58d.jpg");
    InputStream is = new FileInputStream(file);
    byte[] buff = new byte[is.available()];
    is.read(buff);

    OutputStream out = response.getOutputStream();
    out.write(buff);
    out.flush();
    out.close();
}
```



### 在响应中添加附加信息

#### 重定向响应

![](.\img\image-20240406105340386.png)

response.sendRedirect(URL地址)

重定向响应会在响应头中添加一个Location的key，对应的value是给定的URL。客户端浏览器在解析响应头后自动向Location中的URL发送请求

```java
response.sendRedirect("http://www.baidu.com");
```

重定向响应特定：

- 重定向会产生两次请求响应
- 重定向的URL是由客户端浏览器发送的
- 浏览器地址栏会变化



#### 重定向响应案例

需求：创建一个搜索页面，通过百度搜索引擎完成内容的搜索

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	response.setContentType("text/html");
    request.setCharacterEncoding("utf-8");
    String search = request.getParameter("search");
    response.sendRedirect("https://www.baidu.com/s?wd=" + URLEncoder.encode(search,"utf-8"));
}
```

#### 文件下载

在实现文件下载时，我们需要在响应头中添加附加信息。

```java
response.addHeader("Content-Disposition","attachment;filename="+文件名)
```

Content-Disposition:attachment

该附加信息表示作为对下载文件的一个标识字段，不会在浏览器中显示而是直接下载处理

filename=文件名

表示指定下载文件的文件名

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html");
	request.setCharacterEncoding("utf-8");
	File file = new File("d:/test.txt");
    InputStream is = new FileInputStream(file);
	byte[] buff = new byte[is.available()];
	is.read(buff);
 	//在响应中添加附加信息
	response.addHeader("Content-Disposition","attachment;filename="+file.getName);
	OutputStream os = response.getOutputStream();
	os.write(buff);
	os.flush();
	os.close();
}
```

解决文件名中文乱码问题

```java
response.addHeader("Content-Disposition","attachment;filename="+new String(file.getName().getBytes("gbk"),"iso-8859-1"));
```



### ServletContext对象

ServletContext官方叫Servlet上下文。服务器会为每一个Web应用创建一个ServletContext对象。这个对象全局唯一，而且Web应用中的所有Servlet都共享这个对象。所以叫全局应用程序共享对象。

![](.\img\image-20240406130901866.png)

ServletContext对象的作用

- 相对路径转绝对路径
- 获取容器的附加信息
- 读取配置信息
- 全局容器



### ServletContext对象的使用

#### 相对路径转绝对路径

context.getRealPath("path")

该方法可以将一个相对路径转换为绝对路径，在文件的上传与下载时候需要用到该方法做路径的转换

```java
ServletContext servletContext = this.getServletContext();
String realPath = servletContext.getRealPath("image/无标题.jpg");
File file = new File(realPath);
```



#### 获取容器的附加信息

servletContext.getServerInfo()

返回Servlet容器的名称和版本号



servletContext.getMajorVersion()

返回Servlet容器所支持的Servlet主版本号



servletContext.getMinorVersion()

返回Servlet容器所支持的Servlet副版本号

```JAVA
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html");
    request.setCharacterEncoding("utf-8");
    ServletContext servletContext = this.getServletContext();
    String serverInfo = servletContext.getServerInfo();
    int majorVersion = servletContext.getMajorVersion();
    int minorVersion = servletContext.getMinorVersion();
    PrintWriter out = response.getWriter();
    out.println("容器基本信息" + serverInfo + "<br>");
    out.println("容器主版本号" + majorVersion + "<br>");
    out.println("容器副版本号" + minorVersion + "<br>");
    out.fulsh();
    out.close();
}
```



#### 获取web.xml文件中的信息

```xml
<context-param>
	<param-name>key</param-name>
    <param-value>value</param-value>
</context-param>
```



servletContext.getInitParameter("key")

该方法可以读取web.xml文件中\<param-value>标签中的配置信息

servletContext.getInitParameterNames()

该方法可以读取web.xml文件中所有param-name标签中的值



#### 全局容器

servletContext.setAttribute("Key",ObjectValue)

向全局容器中存放数据



servletContext.getAttribute("key")

从全局容器中获取数据



servletContext.removeAttribute("key")

根据key删除全局容器中的value



servlet1代码

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html");
    request.setCharacterEncoding("utf-8");
    ServletContext servletContext = this.getServletContext();
    String value1 = (String)servletContext.getAttribute("key1");
    String value2 = (String)servletContext.getAttribute("key2");
    PrintWriter pw = response.getWriter();
    pw.println(value1 + "<br>");
    pw.println(value2 + "<br>");
    pw.flush();
    pw.close();
}
```

servlet2代码

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	response.setContentType("text/html");
    request.setCharacterEncoding("utf-8");
    ServletContext servletContext = this.getServletContext();
    String value1 = (String)servletContext.getAttribute("key1");
    String value2 = (String)servletContext.getAttribute("key2");
    PrintWriter pw = response.getWriter();
    pw.println(value1 + "2" + "<br>");
    pw.println(value2 + "2" + "<br>");
    pw.flush();
    pw.close();
}
```



#### ServletContext对象生命周期

当容器启动时会创建ServletContext对象并一直缓存该对象，直到容器关闭后该对象生命周期结束。ServletContext对象的生命周期非常长，所以在使用全局容器时不建议存放业务数据。



#### ServletConfig对象

ServletConfig对象对应web.xml文件中的\<servlet>节点。当Tomcat初始化一个Servlet时，会将该Servlet的配置信息，封装在一个ServletConfig对象中，我们可以通过该对象读取\<servlet>节点中的配置信息

```xml
<servlet>
	<servlet-name>ReadServlet</servlet-name>
	<servlet-class>com.example.login.ReadServlet</servlet-class>
    <init-param>
		<param-name>key</param-name>
		<param-value>value</param-value>
	</init-param>
</servlet>
```

servletConfig.getInitParameter("key")

该方法可以读取web.xml文件中\<servlet>标签中\<init-param>标签中的配置信息



servletConfig.getInitParameterNames()

该方法可以读取web.xml文件中当前\<servlet>标签中所有\<init-param>标签中的值

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

	response.setContentType("text/html");
    request.setCharacterEncoding("utf-8");
    Enumeration<String> initParameterNames = getServletConfig().getInitParameterNames();
    PrintWriter pw = response.getWriter();
    while(initParameterNames.hasMoreElements()){
    	String name = initParameterNames.nextElement();
        String initParameter = getServletConfig().getInitParameter(name);
        pw.println(initParameter + "<br>");
    }
    pw.flush();
    pw.close();
}
```



### Cookie对象与HttpSession对象

Cookie对象与HttpSession对象的作用是维护客户端浏览器与服务端的会话状态的两个对象。由于HTTP协议是一个无状态的协议，所以服务端并不会记录当前客户端浏览器的访问状态，但是在有些时候我们需要服务器端能够记录客户端浏览器的访问状态，如获取当前客户端浏览器的访问服务端的次数时就需要会话状态的维持。在Servlet中提供了Cookie对象与HttpSession对象用于维护客户端与服务端的会话状态的维持。二者不同的是Cookie是通过客户端浏览器实现会话的维持，而HttpSession是通过服务器端来实现会话状态的维持。

#### Cookie对象的特点

- Cookie使用字符串存储数据
- Cookie使用Key与Value结构存储数据
- 单个Cookie存储数据大小限制在4097个字节
- Cookie存储的数据中不支持中文，Servlet4.0中支持
- Cookie是与域名绑定所以不支持跨一级域名访问（A饭店的会员卡不能在B饭店使用）
- Cookie对象保存在客户端浏览器内存或系统磁盘中
- Cookie分为持久化Cookie和状态Cookie
- 浏览器在保存同一域名所返回Cookie的数量是有限的。不同浏览器支持的数量不同，Chrome浏览器是50个
- 浏览器每次请求时都会把与当前访问的域名相关的Cookie在请求中提交到服务端

#### Cookie对象的创建

Cookie cookie = new Cookie("key","value)

通过new关键字创建Cookie对象



response.addCookie(cookie)

通过HttpServletResponse对象将Cookie写回给客户端浏览器 

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	Cookie cookie = new Cookie("key","triticale");
	response.addCookie(cookie);
    PrintWriter pw = response.getWriter();
    pw.println("Create Cookie OK");
    pw.flush();
    pw.close();
}
```

#### 获取Cookie中的数据

浏览器每次请求时都会把当前访问的域名相关的Cookie在请求中提交到服务端。通过HttpServletRequest对象获取Cookie，返回Cookie数组

```java
Cookie[] cookies = request.getCookies();
        PrintWriter out = response.getWriter();
        for (int i = 0; i < cookies.length; i++){
            Cookie cookie = cookies[i];
            String name = cookie.getName();
            String value = cookie.getValue();
            out.println("Name:" + name + "Value:" + value);
        }
```

#### 解决Cookie不支持中文

在Cookie中name的值不能使用中文，Value是可以的。但是在Servlet4.0版本之前的Cookie中的Value也是不支持中文存储的，如果存储的数据中含有中文，代码会直接出现异常。我们可以通过对含有中文的数据重新进行编码来解决该问题。在Servlet4.0中的Cookie的Value开始支持中文存储

URLEncoder.encode("content","code");

将内容按照指定的编码方式做URL编码处理

URLDecoder.decode("content","code");

将内容按照指定的编码方式做URL解码处理

#### Cookie跨域问题

域名分类：域名分为顶级域、顶级域名(一级域名)、二级域名

![image-20240526100745597](.\img\image-20240526100745597.png)

域名等级的区别：一级域名比二级域名更高级，二级域名是依附于一级域名之下的附属分区域名，即二级域名是一级域名的细化分级。例如：baidu.com为一级域名，news.baidu.com为二级域名

Cookie不支持一级域名的跨域，支持二级域名的跨域

#### 状态Cookie与持久化Cookie

状态Cookie：Cookie对象仅会被缓存在浏览器所在的内存中，当浏览器关闭后Cookie对象也会销毁

持久化Cookie：浏览器会对Cookie做持久化处理，基于文件形式保存在系统的指定目录中。在Windows10系统中为了安全问题不会显示Cookie中的内容

当Cookie对象创建后默认为状态Cookie。可以使用Cookie对象下的cookie.setMaxAge(60)方法设置失效时间，单位为秒。一旦设置了失效时间，那么该Cookie为持久化Cookie，浏览器会将Cookie对象持久化到磁盘中，当失效时间到达后文件删除。



需求：当客户端浏览器第一次访问Servlet时响应“您好，欢迎您第一次访问！”，第二次访问时响应“欢迎您回来！

```java
@WebServlet("/welcome")
public class WelcomeServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/plain;charset=utf-8");
        Cookie[] cookies = request.getCookies();
        Boolean flag = false;
        if(cookies != null){
            for (Cookie c : cookies) {
                if("welcome".equals(c.getName())){
                    flag = true;
                    break;
                }
            }
        }
        PrintWriter out = response.getWriter();
        if(flag){
            out.println("欢迎您回来");
        }else {
            out.println("您好，欢迎您第一次访问！");
            Cookie cookie = new Cookie("welcome","welcome");
            cookie.setMaxAge(60*60);
            response.addCookie(cookie);
        }
    }
}
```

#### Cookie总结

Cookie对于存储内容是基于明文的方式存储的，所以安全性很低。不要在Cookie中存放敏感数据。在数据存储时，虽然在Servlet4.0中Cookie支持中文，但是建议对Cookie中存放的内容做编码处理，可以提高安全性

#### HttpSession对象的特点

- HttpSession保存在服务端
- HttpSession使用Key与Value存储结构数据
- HttpSession的Key是字符串类型，Value则是Object类型
- HttpSession存储数据大小无限制

#### HttpSession对象的创建

![](.\img\image-20240526134355136.png)

HttpSession对象的创建是通过request.getSession()方法来创建的。客户端浏览器在请求服务端资源时，如果在请求中没有jsessionid，getSession()方法将会为这个客户端浏览器创建一个新的HttpSession对象，并为这个HttpSession对象生成一个jsessionid，在响应中通过状态Cookie写回给客户端浏览器，如果在请求中包含了jsessionid，getSession()方法则根据这个ID返回与这个客户端浏览器对应的HttpSession对象

getSession()方法还有一个重载方法getSession(true|false)。当参数为true时与getSession()方法作用相同。当参数为false时则只去根据jsessionid查找是否有与这个客户端浏览器对应的HttpSession，如果有则返回，如果没有jsessionid则不会创建新的HttpSession对象

#### HttpSession对象的使用

session.setAttribute("key",value)

将数据存储到HttpSession对象中



Object value = session.getAttribute("key")

根据key获取HttpSession中的数据，返回Object



Enumeration\<String> attributeNames = session.getAttributeNames()

获取HttpSession中所有的key，返回枚举类型



session.removeAttribute("key")

根据key删除HttpSession中的数据



String id = session。getId()

根据获取当前HttpSession的SessionID，返回字符串类型

#### HttpSession的销毁方式

- 通过web.xml文件指定超过时间
- 通过HttpSession对象中的invalidate()方法销毁当前HttpSession对象



我们可以在web.xml文件中指定HttpSession的超过时间，当到达指定的超过时间后，容器就会销毁改HttpSession对象，单位为==分钟==。该事件对整个web项目中的所有HttpSession对象有效。时间的计算方式是根据最后一次请求时间作为起始时间。只要用户继续访问，服务器就会更新HttpSession的最好访问时间，并维护改HttpSession。用户每访问服务器一次，无论是否读写HttpSession，服务器都认为改用户的HttpSession“活跃”(active)了一次，销毁时间则会重新计算。如果有哪个客户端浏览器对应的HttpSession的失效时间已到，那么与该客户端浏览器对应的HttpSession对象就会被销毁。其他客户端浏览器对应的HttpSession对象会继续保存不会被销毁

```xml
<session-config>
	<session-timeout>1</session-timeout>
</session-config>
```

我们也可以在Tomcat的web.xml文件中配置HttoSession的销毁时间。如果在Tomcat的web.xml文件中配置了HttpSession的超过时间，对应的是Tomcat中所有的web项目都有效。相当于配置了全局的HttpSession超过时间。如果我们在Web项目中配置了超过时间，那么会以Web项目中的超过时间为准
```xml
<session-config>
	<session-timeout>1</session-timeout>
</session-config>
```

invaildate()方法就HttpSession对象中所提供的用于销毁当前HttpSession的方法。我们通过调用该方法可以销毁当前HttpSession对象



需求：当客户端浏览器第一次访问Servlet时响应“您好，欢迎您第一次访问！”，第二次访问时响应“欢迎您回来！

```java
@WebServlet("/session")
public class WelcomeSessionServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/plain;charset=utf-8");
        HttpSession session = request.getSession();
        String test = (String)session.getAttribute("test");
        Boolean flag = true;
        if (test == null){
            flag = false;
            session.setAttribute("test","test");
        }
        PrintWriter out = response.getWriter();
        if(flag){
            out.println("欢迎您回来");
        }else {
            out.println("您好，欢迎您第一次访问！");

        }
    }
}
```

#### HttpSession生命周期

在HttpSession对象声明周期中没有固定的创建时间和销毁时间。何时创建取决于我们什么时候第一次调用了getSession()或getSession(true)方法。HttpSession对象的销毁时间取决于超过时间的到达以及调用了invalidate()方法。如果没有超过或者没有调用invalidate()方法，那么HttpSession会一直存储。默认超过时间为30分钟(Tomcat的web.xml文件配置的时间就是默认超过时间)

#### HttpSession对象总结

HttpSession与Cookie的区别

- cookie数据存放在客户的浏览器或系统的文件中，而HttpSession中的数据存放在服务器中
- cookie不安全，而HttpSession是安全的
- 单个cookie保存的数据不能超过4K，很多浏览器都限制一个域名保存cookie的数量。而HttpSession没有容量以及数量的限制

#### HttpSession使用建议

HttpSession对象是保存在服务端的，所以安全性较高。我们可以在HttpSession对象中存储数据，但是由于HttpSession对象的生命周期不固定，所以不建议存放业务数据。一般情况下我们知识存放用户登录信息。



#### 自启动Servlet

自动启动Servlet表示在Tomcat启动时就会实例化这个Servlet，他的实例化过程不依赖于请求，而是依赖容器的启动

可以通过web.xml中的\<servlet>标签中通过\<load-on-startup>1\</load-on-startup>配置自启动Servlet

```xml
<servlet>
	<servlet-name>autoStartServlet</servlet-name>
	<servlet-class>com.example.android.AutoStartServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	<servlet-name>autoStartServlet</servlet-name>
	<url-pattern>/autoStart</url-pattern>
</servlet-mapping>
```

#### 通过自启动Servlet实现配置信息的读取

需求：修改文件下载案例，通过自启动Servlet读取配置信息

在自启动Servlet中的init()方法中添加以下代码获取配置信息

```java
public void init() throws ServletException {
        ServletConfig servletConfig = this.getServletConfig();
        String value = servletConfig.getInitParameter("path");
        ServletContext servletContext = this.getServletContext();
        servletContext.setAttribute("path",value);
        System.out.println("init");
    }
```

#### Servlet线程安全问题

在Servlrt中使用的是多线程方式来执行serivice()方法处理请求，所以我们在使用Servlet时需要考虑到线程安全问题，在多线程中对于对象中的成员变量是最不安全的，所以不要在Servlet中通过成员变量的方式来存放数据，如果一定要使用成员变量来存储数据，在对数据进行操作时需要使用线程同步的方式来解决线程安全问题，避免出现数据张冠李戴的现象

```java
@WebServlet("/ts")
public class ThreadSafeServlet extends HttpServlet {
    private PrintWriter out;
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取用户提交的数据
        String value = request.getParameter("name");
        try {
            synchronized (this){
                out = response.getWriter();
                Thread.sleep(5000);
                out.println(value);
                out.flush();
                out.close();
            }
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

为解决这一问题可使用synchronized以线程同步的方式，因为servlet只实例化一次，因此在此处传入this即可



#### Servlrt的url-pattern配置

**精准匹配**

精确匹配是指\<url-pattern>中配置的值必须与url完全精确匹配

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>/demo.do</url-pattern>
</servlet-mapping>
```

http://localhost:8080/demo/demo.do匹配

http://localhost:8080/demo/trtiticale/demo.do不匹配

**扩展名匹配**

在\<url-pattern>允许使用通配符作为匹配规则，*表示匹配任意字符。在扩展名匹配中只要扩展名相同都会被匹配，和路径无关。

注意：在使用扩展名匹配时，在\<url-pattern>中不能使用 / ，否则容器启动就会抛出异常

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

http://localhost:8080/demo/demo.do匹配

http://localhost:8080/demo/trtiticale/demo.do匹配

http://localhost:8080/demo/abc不匹配

**路径匹配**

根据请求路径进行匹配，在请求中只要包含该路径都匹配。*表示任意路径以及子路径

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>/triticale/*</url-pattern>
</servlet-mapping>
```

http://localhost:8080/demo/trtiticale/demo.do匹配

http://localhost:8080/demo/trtiticale/demo匹配

http://localhost:8080/demo/aa/trtiticale/demo.do不匹配

**任意匹配**

匹配 /  匹配所有但不包含JSP页面

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

**匹配所有**

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>/*</url-pattern>
</servlet-mapping>
```

#### 优先顺序

当一个url与多个Servlrt的匹配规则可以匹配时，则按照”精确路径 > 最长路径 > 扩展名“这样的优先级匹配到对于的Servlet



#### Servlet的多URL映射方式

在web.xml文件中支持将多个URL映射到一个Servlrt中，但是相同的URL不能同时映射到两个Servlrt中

```xml
<servlet-mapping>
	<servlet-name>demoServlet</servlrt-name>
    <url-pattern>/demo</url-pattern>
    <url-pattern>/triticale</url-pattern>
</servlet-mapping>
```

#### 基于注解式开发啊Servlet

在Servlet3.0以及之后的版本中支持注解式开发Servlet。对于servlet的配置不再依赖于web.xml配置文件，而是使用@WebServlet注解完成Servlet的配置

| 属性名         | 类型           | 作用                            |
| -------------- | -------------- | ------------------------------- |
| initParams     | WebInitParam[] | Servlet的init参数               |
| name           | String         | Servlet的名称                   |
| urlPatterns    | String[]       | Servlet的访问URL，支持多个      |
| value          | String[]       | 等价于urlPatterns，两者不能共存 |
| loadOnStartup  | int            | 自启动Servlet                   |
| description    | String         | Servlet的描述                   |
| displayName    | String         | Servlrt的显示名称               |
| asyncSupported | boolean        | 声明Servlet是否支持异步操作模式 |

#### 文件上传

在Servlrt3.0之前的版本中如果实现文件上传需要依赖apache的Fileupload组件，在Servlet3.0以及之后的版本中提供了Part对象处理文件上传，所以不在需要额外的添加Fileupload组件。

在Servlet3.0以及之后的版本中实现文件上传时必须要在Servlet中开启多参数配置：

web.xml

```xml
<multipart-config>
	<file-size-threshold></file-size-threshold>
    <location></location>
    <max-file-size></max-file-size>
    <max-request-size></max-request-size>
</multipart-config>
```
@MultipartConfig

| 元素名                 | 类型   | 描述                                                         |
| ---------------------- | ------ | ------------------------------------------------------------ |
| \<file-size-threshold> | int    | 当数据量大于改值时，内容将被写入临时文件                     |
| \<location>            | String | 存放生成的临时文件地址                                       |
| \<max-file-size>       | long   | 允许上传的文件最大值(byte)。默认值为-1，表示没有限制         |
| \<max-request-size>    | long   | 一个multipart/fprm-data请求能携带的最大字节数（byte），默认值为-1，表示没有限制 |

Part对象中常用的方法

- long getSize()

  上传文件大小

- String getSubmittedFileName()

  上传文件的原始文件名

- String getName()

  获取\<input name="upload" ...>标签中name属性值

- InputStream getInputStream()

  获取上传文件输入流

- void write(String path)

  保存文件至服务器

```java
@WebServlet("/fileUpload.do")
@MultipartConfig
public class FileUploadServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        String desc = request.getParameter("desc");
        Part part = request.getPart("file");
        String newName = UUID.randomUUID().toString() + part.getSubmittedFileName().substring(part.getSubmittedFileName().lastIndexOf("."));
        ServletContext servletContext = this.getServletContext();
        String realPath = servletContext.getRealPath("images/" + newName);
        System.out.println(realPath);
        part.write(realPath);
        response.setContentType("text/html;charset=utf-8");
        PrintWriter pw = response.getWriter();
        pw.println(desc);
        pw.flush();
        pw.close();
    }
}
```

#### Filter过滤器

Filter过滤器是Servlet2.3中提供的一个过滤请求与响应的对象

Filter过滤器既可以对客户端向服务器端发送的请求进行过滤，也可以对服务器端向客户端产生的响应进行过滤处理

![](.\img\image-20240527205940511.png)

**Filter对象的创建**

创建一个Class实现Filter接口，并实现接口中三个抽象方法

init()方法：初始化方法，在创建Filter后立即调用。可用于完成初始化操作

doFilter()方法:拦截请求与响应方法，可用于对请求和响应实现预处理

destory()方法：销毁方法，在销毁Filter之前自动调用。可用于完成资源释放等动作

```java
public class FirstFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {
        System.out.println("init");
    }

    public void destroy() {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        //对请求进行处理
        System.out.println("请求被过滤");
        chain.doFilter(request, response);
        //对响应进行处理
        System.out.println("响应被过滤");
    }
}
```

#### 在Filter中设置请求编码

需求：在Filter中实现对请求的编码的设置

```java
@Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        chain.doFilter(request, response);
        
    }
```

不建议在filter中加入响应编码，响应编码应该在各自的Servlet中实现（因为不同的业务响应的内容不同）

#### FilterConfig对象的使用

FilterConfig对象是用来读取\<filter>中\<init-param>初始化参数的对象。该对象通过参数传递到init方法中，用于读取初始化参数



filterConfig.getInitParameter("name")

通过name获取对应的value



filterConfig.getInitParameterNames()

返回改Filter中所有的\<param-name>的值

```xml
<filter>
	<filter-name>encodingFilter</filter-name>
	<filter-class>com.titicale.filter.EncodingFilter</filter-class>
	<init-param>
		<param-name>code</param-name>
		<param-value>utf-8</param-value>
	</init-param>
</filter>
<filter-mapping>
<filter-name>encodingFilter</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
```

```java
public class EncodingFilter implements Filter {
    private String defaultCode = "utf-8";
    
    public void init(FilterConfig config) throws ServletException {
        String code = config.getInitParameter("code");
        if(code != null && code.length() > 0){
            this.defaultCode = code;
        }
    }

    public void destroy() {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        request.setCharacterEncoding(defaultCode);
        chain.doFilter(request, response);

    }
}
```

#### FilterChain(过滤器链)

Filter技术的特点是在对请求或响应做预处理时，可实现“插拔式”的程序设计。我没可以根据自己的需求添加多个Filter，也可以根据需求去掉某个Filter，通过修改web.xml文件即可实现。那么如果又多个过滤器对某个请求及响应进行过滤，那么这组过滤器就称为过滤器链

**Filter执行顺序**

则按照在web.xml文件中配置的上下顺序来决定先后。在上的先执行，在下的后执行

![image-20240528152321550](.\img\image-20240528152321550.png)

如第一个过滤器设置请求编码，第二个过滤器判断用户是否登录，第三个过滤器判断权限



#### 基于注解式开发Filter

Filter支持注解式开发，通过@WebFilter注解代替web.xml中Filter的配置

| 属性名      | 类型           | 作用                                              |
| ----------- | -------------- | ------------------------------------------------- |
| fitlerName  | String         | 指定过滤器的name属性                              |
| urlPatterns | String[]       | 拦截请求的URL，支持多个                           |
| value       | String[]       | 等价于urlPatterns，两者不能共存                   |
| description | String         | 过滤器的描述                                      |
| displayName | String         | 过滤器的显示名称                                  |
| initParams  | WebInitParam[] | 指定一组过滤器初始化参数，等价于\<init-param>标签 |

使用注解式开发Filter时，执行顺序会根据Filter的名称进行排序的结果决定调用的顺序

注意：web.xml的优先级要高于注解式的优先级

```java
@WebFilter(urlPatterns = "/*",initParams = {@WebInitParam(name="key",value="value")})
```

#### Filter的生命周期

Filter的生命周期是由容器管理的。当容器启动时会实例化Filter并调用init方法完成初始化动作。当客户端浏览器发送请求时，容器会启动一个新的线程来处理请求，如果请求的URL能够被过滤器所匹配，那么优先调用过滤器中的doFilter方法，再根据是否有chain.doFilter的指令，决定是否继续请求目标资源。当容器关闭时会销毁Filter对象，在销毁之前会调用destory方法

#### Listener监听器

监听器用于监听web应用中某些对象的创建、销毁、增加、修改、删除等动作的发生，然后做出相应的响应处理。当范围对象的状态发生变化的时候，服务器会自动调用监听器对象中的方法

**监听器分类**

- ServletContext对象生命周期监听器和属性监听器
- HttpSession对象生命周期监听器和属性监听器

- ServletRequest对象生命周期监听器和属性监听器

#### ServletContext对象的生命周期监听器

ServletContextListener接口定义了ServletContext对象声明周期的监听行为



void contextInitialized(ServletContextEvent sce)

ServlrtContext对象创建之后会触发该监听方法，并将ServletContext对象传递到该方法中



void contextDestroyed(ServletContextEvent sce)

ServlrtContext对象在销毁之前会触发该监听方法，并将ServletContext对象传递到该方法中

```xml
<listener>
	<listener-class>com.triticale.listener.ServletContextLifecycleListener</listener-class>
</listener>
```

```java
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContext Init....");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext Destroy....");
    }
```

#### ServletContext对象的属性操作监听器

ServletContextAttributeListener接口定义了对于ServletContext对象属性操作的监听行为



void attributeAdded(ServletContextAttributeEvent event)

向ServletContext对象中添加属性时会触发该监听方法，并将ServletContext对象传递到该方法中。触发事件的方法为servletContext.setAttribute("key","value")




void attributeRemoved(ServletContextAttributeEvent event)

向ServletContext对象中删除属性时会触发该监听方法，并将ServletContext对象传递到该方法中。触发事件的方法为servletContext.removeAttribute("key","value")



void attributeReplaced(ServletContextAttributeEvent event)

向ServletContext对象中替换属性时会触发该监听方法，并将ServletContext对象传递到该方法中。触发事件的方法为servletContext.setAttribute("key","value")

注意：在监听替换方法中，通过event.getValue拿到的是被替换到的值，替换的值可通过servletContext拿到

```java
 	@Override
    public void attributeAdded(ServletContextAttributeEvent event) {
        System.out.println("-----------Start Added-------------");
        System.out.println("name:" + event.getName() + " value:" + event.getValue());
        System.out.println(event.getServletContext());
        System.out.println("-----------End Added-------------");
    }

    @Override
    public void attributeRemoved(ServletContextAttributeEvent event) {
        System.out.println("-----------Start Removed-------------");
        System.out.println("name:" + event.getName() + " value:" + event.getValue());
        System.out.println(event.getServletContext());
        System.out.println("-----------End Removed-------------");
    }

    @Override
    public void attributeReplaced(ServletContextAttributeEvent event) {
        System.out.println("-----------Start Replaced-------------");
        System.out.println("name:" + event.getName() + " value:" + event.getValue());
        System.out.println(event.getServletContext());
        System.out.println("-----------End Replaced------------");
    }
```

HttpSession和HttpServletRequest对象的生命周期监听器和属性操作监听器同理，不再做详细演示

#### 基于注解式开发监听器

Listener支持注解式开发，通过@WebListener注解替代web.xml中Listener的配置

#### Filter与Listener设计模式

**Filter的设计模式**

在Servlet的Filter中使用的是责任链设计模式

责任链(Chain of Responsibility):责任链模式也叫职责链模式，是一种对象行为模式。在责任链模式里，很多对象由一个对象对下一个对象的引用而连接起来形成一条链。请求在这个链上传递，直到链上的某一个对象决定处理此请求。发出这个请求的客户端并不需要直到链上的哪一个对象最终处理这个请求，这使得系统可以在不影响客户端的情况下动态地组织链和分配责任

优点

- 降低了对象之间的耦合度
- 增强了系统的可扩展性
- 增强了给对象指派责任的灵活性
- 责任链简化了对象之间的链接
- 责任分担。每个类只需要处理自己该处理的工作

缺点

- 不能保证请求一定被接收
- 对于较长的责任链，请求的处理可能涉及多个处理对象，系统性能将受到一定影响
- 可能会由于责任链的错误设置而导致系统出错，如可能会造成循环调用

**Listener的设计模式**

在Servlet的Listener中使用的观察者设计模式

观察者模式(Observer Pattern)：观察者模式是一种对象行为模式。它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得通知并被自动更新

优点

- 观察者和被观察者是抽象耦合的
- 建立一套触发机制

缺点

- 如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间
- 如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃
- 观察者模式没有相应的机制让观察者知道所观察的目标是怎么发生变化的，而仅仅知识知道观察目标发生了变化