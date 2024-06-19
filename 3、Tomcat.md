### Tomcat简介

Tomcat服务器是Apache的一个开源免费的Web容器。它实现了JavaEE平台下部分技术规范，属于轻量级应用服务器



### Tomcat版本说明

| Tomcat版本 | JDK版本     | Servlet版本 | JSP版本 |
| ---------- | ----------- | ----------- | ------- |
| 10.0.X     | 8 and later | 5.0         | 3.0     |
| 9.0.X      | 8 and later | 4.0         | 2.3     |
| 8.0.X      | 7 and later | 3.1         | 2.3     |
| 7.0.X      | 6 and later | 3.0         | 2.2     |
| 6.0.X      | 5 and later | 2.5         | 2.1     |



### Tomcat作用

可以在Tomcat中运行我们所编写的Servlet、JSP



### Tomcat目标结构与介绍

- bin目录：主要用来存放Tomcat的命令文件
- conf目录：主要是用来存放Tomcat的一些配置文件
- lib目录：主要用来存放Tomcat运行需要加载的jar包
- logs目录：用来存放Tomcat在运行过程中产生的日志文件
- temp目录：用来存放Tomcat在运行过程中产生的临时文件
- work目录：用来存放Tomcat在运行时的编译后文件，例如JSP编译后的文件
- webapps目录：用来存放应用程序，当tomcat启动时会去加载webapps目录下的应用程序，可以以文件夹，war包的形式发布应用



Tomcat启动与关闭需要执行bin目录中的命令脚本

### Tomcat启动

- 方式一

  运行startup.bat文件

- 方式二

  catlina.bat start

  其中catlina.bat是命令文件，start是启动Tomcat参数

### Tomcat关闭

- 方式一

  运行shutdown.bat文件

- 方式二

  catlina.bat stop

  其中catlina.bat是命令文件，stop是关闭Tomcat参数

- 方式三

  直接关闭掉控制台窗口



### 访问Tomcat

```
http://ip:port
```

如访问本机Tomcat的URL格式

```
http://127.0.0.1:8080
```



### Tomcat配置文件介绍

Tomcat的配置文件由4个xml组成，分别是context.xml 、web.xml、server.xml 、tomcat-user.xml。每个文件都有自己的功能和配置方法



#### context.xml

context.xml是Tomcat公用的环境配置。Tomcat服务器会定时去扫描这个文件。一旦发现文件被修改（时间戳改变了），就会自动重新加载这个文件，而不需要重启服务器。



#### web.xml

Web应用程序描述文件，都是关于是Web应用程序的配置文件。所有Web应用的web.xml文件的父文件



#### server.xml

是Tomcat服务器的核心配置文件，server.xml的每一个元素都对应了tomcat中的一个组件，通过对xml中元素的配置，实现对tomcat中的各个组件和端口的配置



#### tomcat-user.xml

配置访问Tomcat的用户以及角色的配置文件



### 解决控制台乱码

控制台产生乱码的原因是在Tomcat在输出日志中使用的是UTF-8编码，而我们中文的Windows操作系统使用的是GBK编码。由于编码格式不统一，所以出现了乱码。

解决方式

修改conf目录中的logging.properties文件重新指定的编码方式。

```
java.util.logging.ConsoleHandler.encoding = GBK
```



修改Tomcat监听端口

Tomcat默认监听端口为8080.可以通过修改server.xml文件来改变Tomcat的监听端口

```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />
```



### 配置Tomcat Manager

Tomcat Manager是Tomcat自带的、用于对Tomcat自身以及部署在Tomcat上的应用进行管理的web应用。默认情况下，Tomcat Manager是处于禁用状态的。准确的说，Tomcat Manager需要以用户角色进行登录并授权才能使用相应的功能，不过Tomcat并没有配置任何默认的用户，因此我们需要先进行用户配置后才能使用Tomcat Manager。



#### 配置Tomcat Manager的访问用户

Tomcat Manager中没有默认用户，我们需要在tomcat-users.xml文件配置。Tomcat Manager的用户配置需要配置两个部分：角色配置、用户名及密码配置。



#### Tomcat Manager中的角色分类

![image-20240331200752583](C:\Users\lt\AppData\Roaming\Typora\typora-user-images\image-20240331200752583.png)



#### 配置用户及角色

修改tomcat-users.xml

```xml
<role rolename = "manager-gui"/>
<user username = "tomcat" password ="tomcat" roles="manager-gui"/>
```



#### 解除访问限制

进入Tomcat的webapps目录下，打开webapps/manager/META-INF/context.xml文件,修改下面这段配置

```xml
<!--把下面这段注释掉-->
<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
```



### Tomcat架构

#### Tomcat工作原理

Tomcat是一个能够处理请求并产生响应的应用程序。Tomcat实现了JavaEE平台下的一些技术规范，所以我们可以在Tomcat中运行我们所编写的Servlet、JSP。

![](.\img\image-20240331202701932.png)

#### 架构图

![img](E:\软件工程\Java EE就业学习\Java Web\img\Center)

```xml
<Server port="8005" shutdown="SHUTDOWN">
    <Service name="Catalina">
         <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />
        <Engine name="Catalina" defaultHost="localhost">
            <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
			</Host>
    </Engine>
```



### Tomcat组件

#### Server组件

启动一个server实例（即一个JVM进程），它监听在8005端口以接收shutdown命令。

Server的定义不能使用同一个端口，这意味着如果在同一个物理机上启动了多个Server实例必须配置它们使用不同的端口

```xml
<Server port="8005" shutdown="SHUTDOWN">
```

port:接收shutdown指令的端口，默认为8005；

shutdown：发往此Server用于实现关闭Tomcat实例的命令字符串，默认为SHUTDOWN；



#### Service组件

Service主要用于关联一个引擎和与此引擎相关的连接器，每个连接器通过一个特定的端口和协议接收请求并将其转发至关联的引擎进行处理。因此，Service要包括一个引擎，一个或多个连接器

```xml
<Service name="Catelina">
```

name:此服务器名称，默认为Catalina



#### Connector组件

支持处理不同请求的组件，一个引擎可以有一个或多个连接器，以适应多种请求方式，默认只开启了处理Http协议的连接器。如果需要使用其他协议，需要在Tomcat中配置改协议的连接器

在Tomcat中连接器类型通常有4种

- HTTP连接器
- SSL连接器 (HTTPS)
- AJP 1.3连接器
- proxy连接器

```xml
<Connector port="8888" protocol="HTTP/1.1"
           	   connectionTimeout="20000"
               redirectPort="8443"/>
```

port:监听的端口

protocol:连接器使用的协议，默认为HTTP/1.1；

connectionTimeout：等待客户端发送请求的超过时间，单位为毫秒；

redirectPort：如果某连接器支持的协议是HTTP，当接收客户端发来的HTTPS请求时，则转发至此熟悉定义的端口；

maxThreads:支持的最大并发连接数，默认是200个；

#### Engine组件

Engine是Servlet处理器的一个实例，即servlet引擎，定义在server.xml中的Service标签中，Engine需要defaultHost属性为其定义一个接收所有发往非明确定义虚拟主机的请求的Host组件

![](.\img\image-20240401203501238.png)

```xml
<Engine name="Catalina" defaultHost="localhost">
```

name:Engine组件的名称

defaultHost:Tomcat支持基于FQDN（全限定域名）的虚拟主机，这些虚拟主机可以通过在Engine容器中定义多个不同的Host组件来实现；但如果此引擎的连接器收到一个发往非明确定义虚拟主机的请求时则需要将此请求发往一个默认的虚拟主机进行处理，因此，在Engine中定义的多个虚拟主机的主机名称至少要有一个跟defaultHost定义的主机名称同名;

#### Host组件

虚拟主机

是一种在单一主机或主机群上，实现多网域服务的方法，可以运行多个网站或服务的技术。

Host组件位于Engine容器中用于接收请求并进行相应处理的虚拟主机。通过该容器可以运行Servlet或者JSP来处理请求

```xml
<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
```

name:虚拟主机的名称，Tomcat通过在请求URL中的域名与name中的值匹配，用于查找能够处理该请求的虚拟主机。如果未找到则交给在Engine中defaultHost指定的主机处理；

appBase：此Host的webapps目录，即指定存放web应用程序的目录的路径；

autoDeploy：在Tomcat处于运行状态时放置于appBase目录中的应用程序文件是否自动进行deploy；默认为true；

unpackWARs：在启用此webapps时是否对WAR格式的归档文件先进行展开；默认为true；

#### Context组件

Context是Host的子组件，代表指定一个Web一个应用，它运行在某个指定的虚拟主机上；每个Web应用都是一个WAR文件，或者文件的目录

```xml
<Context path="/test" docBase="D:\bjsxt\itbz.war"/>
```

path: context path既浏览器访问项目的访问路径

docBase：相应的Web应用程序存放位置；也可以使用相对路径，起始路径为此Context所属Host中appBase定义的路径



### 配置虚拟主机

在conf目录下server.xml文件中添加虚拟主机

同时创建一个webapps1目录

```xml
 <Host name="triticale" appBase="webapps1"
          unpackWARs="true" autoDeploy="true">
     <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

在C:\Windows\System32\drivers\etc下的host文件中配置triticale.com 127.0.0.1

![](.\img\image-20240401211814830.png)

### 配置Context

创建index.html页面

将index.html资源部署到d盘的demo目录中。

通过triticale.com:8080/triticale/index.html访问虚拟主机，并访问index.html

```xml
<Context path="/triticale" docBase="d:/demo"/>
```

