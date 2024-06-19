### HTTP的请求和响应

---

#### 什么是HTTP协议

> HTTP协议(超文本传输协议HyperText Transfer Protocol)，它是基于TCP协议的应用层传输协议，简单来说就是客户端和服务端进行数据传输的一种规则。

在使用HTTP通信时，在一条通信线路上必定有一端是客户端，一端是服务器端，而请求必定由客户端发出、服务端回复响应

> HTTP是无连接的，每次请求一次，释放一次连接。所以无连接表示每次连接只能处理一个请求。优点就是节省传输时间，实现简单。我们有时称这种无连接为短连接。对应的就有了长链接，长连接专门解决效率问题。当建立好了一个连接之后，可以多次请求。但是缺点就是容易造成占用资源不释放的问题。当HTTP协议头部中字段Connection：keep-alive表示支持长链接
>
> HTTP是无状态的，即HTTP协议自身不对请求和响应之间的通信状态进行保存。也就是说在HTTP这个级别，协议对于发送过的请求或响应都不做持久话处理。

那么客户端是如何发送请求的呢？服务器端又是如何响应的呢？我们第一次之后访问某一网站，它是怎么记录我们的信息的呢（如登录信息）？

我们以访问京东首页为例

#### 首部信息：

![](.\img\image-20240308102301845.png)

#### 报文格式

![](.\img\image-20240308165219907.png)



#### URL URI

- URI（Uniform Resource Identifier) 统一资源标识符

- URL（Uniform Resource Locator）统一资源定位符

  - 传送协议
  - 服务器（通常为域名或者IP地址）
  - 端口号，若为HTTP的默认值":80"可省略
  - 请求资源路径
  - 传递数据（在URL中传递数据是以key=value的结构进行数据绑定，以“?”字符为起点，每个参数以“&”隔开，再以“=”分开参数名称与数据，通常以UTF8的URL编码，避开字符冲突的问题）

  例如

  ```````
  http://www.baidu.cn:80/course/id/18.html?a=3&b=4
  ```````

  其中：

  1. http, 是协议；
  2. [www.baidu.cn](http://www.baidu.cn/)，是服务器域名；
  3. 80，是服务器上的默认网络端口号，默认不显示；
  4. /course/id/18.html，是路径(URI：直接定位到对应的资源)；
  5. ?a=3&b=4，请求时传递的数据；

#### 请求方法

| 序号 | 方法    | 描述                                                         |
| :--- | :------ | :----------------------------------------------------------- |
| 1    | GET     | 请求指定的页面信息，并返回实体主体。                         |
| 2    | HEAD    | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| 3    | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4    | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| 5    | DELETE  | 请求服务器删除指定的页面。                                   |
| 6    | CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。    |
| 7    | OPTIONS | 允许客户端查看服务器的性能。                                 |
| 8    | TRACE   | 回显服务器收到的请求，主要用于测试或诊断。                   |
| 9    | PATCH   | 是对 PUT 方法的补充，用来对已知资源进行局部更新 。           |

==虽然这些方法是HTTP协议里面规定的，但WEB SERVER未必允许或支持这些方法==

因此，***OPTIONS***方法可以返回服务器可用的方法

- ***GET***方法用于获取资源，返回实体主体
  - ***HEAD*** 和 GET基本一致，只不过不返回报文主体内容

  例如：我只关心服务器中的某一资源是否存在，无需返回主体内容

- ***POST***：传输实体主体

  > GET 和 POST的区别：**我们一般会听说GET请求有上限而POST请求无上限**，但实际上HTTP协议本身并未对两方法做过多限制，而是浏览器和服务端进行了一些限制，==GET的参数一般约定俗成放到URL的query中，POST请求将参数放入body中==，服务端解析字符串时需要分配内存，而URL需要作为一个整体看待，需要用一块足够大的内存。那么URL如果太长或并发量高，可能会挤爆服务端的内存，因此各端对URL进行了限制处理
  >
  > **POST请求比GET请求更安全？**
  >
  > 一般认为，将请求参数放入URL上会不安全，但这仅仅是直观上的认为
  >
  > 但不管是query还是body，其参数都是明文的，且HTTP协议本身就是不安全的协议

- ***PUT***:传输文件

  > 在请求报文的主体中包括文件内容，然后保存到请求URI指定的位置，但因为HTTP/1.1中的PUT方法自身不带验证机制，任何人都可以上传文件，存在安全性问题，因此一般的web网站不使用这个方法

- ***DELETE***：删除文件，与PUT相反

- ***TRACE***：追踪路径，客户端在发送请求时，在Max-Forwards首部字段填入数值，每经过一个服务器就会将该数字减1，当数值刚好到0时，停止继续传输，最后接受到请求的服务武器端则返回状态码 200 ok的响应

  > 但因为容易引起跨站追踪攻击，通常不会用到

  ![](.\img\image-20240308195918599.png)

- ***CONNECT***：要求用隧道协议连接代理

#### 请求首部行

用于说明是谁或什么在发送请求、请求源于何处，或者客户端的喜好及能力。服务器可以根据请求头部给出的客户端信息，试着为客户端提供更好的响应。请求头中信息的格式为key：value。

- Host

  客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号。

- Connection

  连接方式。如果值是close则表示基于短连接方式，如果该值是keep-alive，网络连接就是持久的，在一定时间范围内是不会关闭，使得对同一个服务器的请求可以继续在该连接上完成。

- Upgrade-Insecure-Requests

  服务端是否支持https加密协议。

- Cache-Control

  指定请求和响应遵循的缓存机制。

- User-Agent

  浏览器表明自己的身份（是哪种浏览器）。例如Chrome浏览器：Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36。

- Accept

  告诉WEB服务器自己接受什么介质类型，*/* 表示任何类型，type/* 表示该类型下的所有子类型。

- Accept-Encoding

  浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）。

- Accept-Language

  浏览器申明自己接收的语言。语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等。

- Accept-Charset

  浏览器告诉服务器自己能接收的字符集。

- Referer

  表示浏览器应该在多少时间之后刷新文档，以秒计时。

- Cookie

  可向服务端传递数据一种模型。

#### 状态码

##### 状态码的类别

|      | 类别                           | 原因短语                   |
| ---- | ------------------------------ | -------------------------- |
| 1XX  | Information(信息性状态码)      | 接收的请求正在处理         |
| 2XX  | Success(成功状态码)            | 请求正常处理完毕           |
| 3XX  | Redirection(重定向状态码)      | 需要进行附加操作以完成请求 |
| 4XX  | Client Error(客户端错误状态码) | 服务器无法处理请求         |
| 5XX  | Server Error(服务器错误状态码) | 服务器处理请求出错         |

![](.\img\image-20240308163545487.png)

（如上图，响应报文 状态码为502 )

因为算上附加的HTTP状态码，数量多达60多个，下文只介绍常见的13种

- ***200 OK***

  从客户端发来的请求在服务器端被正常处理了

- ***204 No Content***

  服务器成功处理了请求，但没有返回任何响应内容

  > 应用场景：①：当客户端发送的请求是一个更新或删除操作，且不需要返回结果时
  >
  > ​					②：当客户端请求的资源不存在，但客户端仍然希望服务器成功处理请求时

- ***206 Partial Content***

  客户端进行了范围请求，而服务器成功执行了这部分的GET请求。

---

- ***301 Moved Permanently***

  永久性重定向。表示请求的资源已经被分配了新的URI，以后应使用资源现在所指的URI
  
- ***302 Found8***

  临时性重定向。表示请求的资源已被分配了新的URI，希望用户（本次）能使用新的URI访问

![](.\img\image-20240309094237333.png)

> 301 重定向是永久的重定向，搜索引擎在抓取新内容的同时也将旧的网址替换为重定向之后的网址。302 重定向是临时的重定向，搜索引擎会抓取新的内容而保留旧的网址

- ***303 See Other***

  303状态码和302 Found状态码有着相同的功能，但303状态码明确表示客户端应采用GET方法获取资源

- ***304 Not Modified***

  自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。(可直接使用客户端未过期的缓存)

---

- ***400 Bad Request***

  该状态码表示请求报文中存在语法错误。当错误发生时，需修改请求的内容后再次发送请求

- ***401 Unauthorized***

  未授权，请求要求身份验证。若之前已进行过1次请求，则表示用户认证失败

- ***403 Forbidden***

  表示对请求资源的访问被服务器拒绝了。服务器端没有必要给出拒绝的理由。

- ***404 Not Found***

  该状态码表明服务器上无法找到请求的资源。除此之外，也可以在服务器拒绝请求且不想说明理由时使用

---

- ***500 Internal Server Error***

  服务器内部错误

- ***503 Service Unavailable***

  服务器暂时处于超负载或正在进行停机维护，现在无法处理请求

---

#### 响应首部行

用于告知浏览器当前响应中的详细信息，浏览器通过获取响应头中的信息可以知道应该如何处理响应结果。响应头中信息的格式为key：value。

- Date

  响应的Date使用的是GMT时间格式，表示响应消息送达时间。

- Server

  服务器通过这个Server告诉浏览器服务器的类型。

- Vary

  客户端缓存机制或者是缓存服务器在做缓存操作的时候，会使用到Vary头，会读取响应头中的Vary的内容，进行一些缓存的判断。

- Content-Encoding

  文档的编码(Encode)方式。用gzip压缩文档能够显著地减少HTML文档的响应时间。

- Content-Length

  表示内容长度。只有当浏览器使用持久HTTP连接时才需要这个数据。

- Content-Type

  表示响应的文档属于什么MIME类型。

我们现在理解了客户端通过HTTP发送请求和服务器端响应的过程，但因为HTTP是无状态的，那么是如何保存客户端信息的呢？

#### 响应实体主体

> 响应体就是响应的消息体，如果是纯数据就是返回纯数据，如果请求的是HTML页面，那么返回的就是HTML代码，如果是JS就是JS代码，如此之类。

#### Session&Cookie

无状态协议由于不必保存状态，可减少服务器的CPU及内存资源的消耗。

而若想保留无状态协议这个特征的同时又要解决类似的矛盾问题，于是引入了Cookie技术

- Session和Cookie是由服务器生成的，都是用来存储特定的值(键值对应)

- Session是存储在服务器的，而Cookie是会返回给客户端的
  - 一般来说，SessionID会以类似于Cookie的方式返回给客户端
  - SessionID是服务器用来识别、操作存储session值的对象的
  - 在服务端，session的存储方式有文件存储、数据库方式

- 客户端在发送请求的时候，会自动将存活、可用的cookie封装在请求头中和请求一起发送

- cookie和session都是有生命周期的

  - cookie的生命周期一般受到两个因素的影响
    - cookie自身的存活周期：是服务器生成cookie时去设定的
    - 客户端是否保留了cookie，客户端是否保留cookie，只对客户端自身有影响，对其他封装包工具是没有影响的

  > 例如，cookie就是一个优惠券，服务器生成cookie时会设定一个生命周期，就相当于优惠券的有效期；而客户端没有保留cookie就相当于把优惠券丢掉了，但只要还在有效期内，优惠券还可以给别人用；

- session的生命周期，一般受到两个因素影响
  - 服务器对于session对象的保存最大时间的设置
  - 客户端进程是否关闭

- cookie和session都是有其作用域的（比如说你用健身会员卡去餐厅是没有用的）



简单来说，cookie是存在浏览器端的一小段文本数据，大小不超过4Kb，发送网络请求时，cookie会在请求头里一起发送到服务器端，session对象是存储在服务器端的，一般用来存储用户会话的数据，sessionID一般存储在cookie里。session一般会配置有效时间 ，而首次登录相当于用你的username和password交换包含sessionID的cookie，下次在session存活时，再次访问就不用再输入username和password



#### 用socket构建HTTP请求响应

```java
package com.triticale.demo;
import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
public class service {
    public static void main(String args[]){
        try{
            ServerSocket serverSocket = new ServerSocket(4134);
            Socket socket = new Socket();
            while (true){
                System.out.println("正在监听------");
                socket = serverSocket.accept();
                System.out.println("监听到一个客户端请求");
                System.out.println("为该客户端创建一个线程实列并开始运行该线程");
                serviceThread thread1 = new serviceThread(socket);
                thread1.start();
            }
        }
        catch (Exception e){

        }
    }
}
```

```java
package com.triticale.demo;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
public class serviceThread extends Thread{
    private Socket socket;


    public  serviceThread(Socket t_socket){
        socket = t_socket;
    }
    @Override
    public void run(){
        System.out.println("线程开始，打印该客户端的ip和端口");
        System.out.println(socket.getLocalAddress() + ": " + socket.getLocalPort());
        InputStream clientInput = null;
        InputStreamReader clientInputStream = null;
        BufferedReader clientBufferReader = null;
        OutputStream clientOutStream = null;
        Writer clientWriter = null;
        System.out.println("打印该客户端的请求报文头");
        try{
            clientBufferReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String st = null;
            while ((st = clientBufferReader.readLine()) != null && st.length() != 0){
                System.out.println(st);
            }
            socket.shutdownInput();
            System.out.println("构造回应报文头并发送给还客户端");
            clientWriter = new OutputStreamWriter(socket.getOutputStream(),"UTF-8");
            clientWriter.write("HTTP/1.1 200 \r\n");
            clientWriter.write("Transfer-Encoding: chunked");
            clientWriter.write("Content-Type: text/html;charset=UTF-8\r\n");
            clientWriter.write("\r\n");
            clientWriter.write("helloWord\r\n");
            clientWriter.flush();
            socket.close();
        }catch (Exception e){

        }
    }
}
```

