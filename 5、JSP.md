### JSP

Java Server Pages

---

#### JSP介绍

JSP全称Java Server Pages，即Java服务端页面技术，是JavaEE平台下的技术规范。它允许使用特定的标签在HTML网页中插入Java代码，实现动态页面处理，所以JSP就是HTML与Java代码的复合体。JSP技术可以快速的实现一个页面的开发，相比在Servlet中实现页面开发将变得更加容易

#### 常见的视图层技术

HTML、JSP、Thymeleaf等

#### 前后端分离开发方式

在前后端分离的项目中真正可以做到“术业有专攻”（开发人员分离）。前后端分离开发方式中前端页面由专业团队完成页面的开发，并通过请求调用后端的api接口进行数据交互。

在开发前端页面的团队中更多关注的技术如：html、CSS、jQuery、Vue、Nodejs等前端技术。前端追求的是：页面表现。速度流畅，兼容性，用户体验等等。

而后端团队则更多的是业务的具体实现。在后端开发的团队中更多关注的技术如：设计模式、分布式框架、微服务架构、数据库的操作、Java的性能优化以及数据库优化等技术。

前后端分离已经成为互联网项目开发的业界标准使用方式，特别是为大型分布式架构、弹性计算框架、微服务架构、多端化服务（多种客户端，例如：浏览器，车载终端，Android，IOS等等）打下坚实的基础

#### JSP运行原理

![image-20240519162412334](.\img\image-20240519162412334.png)

#### JSP技术特定

JSP和Servlet是本质相同的技术，当一个JSP文件第一次被请求时，JSP引擎会将该JSP编译成一个Servlet，并执行这个Servlet。如果JSP文件被修改了，那么JSP引擎会重新编译这个JSP。

JSP引擎对JSP编译时会生成两个文件分别是.java的源文件以及编译后的.class文件，并放到Tomcat的work目录的Catalina对应的虚拟主机目录中的org\apache\jsp目录中。两个文件的名称会使用JSP的名称加“_jsp”表示。如：index_jsp.java、index_jsp.class

#### JSP与Servlet的区别

- JSP以源文件形式部署到容器中。而Servlet需要编译成class文件后部署到容器中
- JSP部署到web项目的根目录下或根目录下的其他子目录和静态同资源位于相同位置，而Servlet需要部署到WEB-INF/classes目录中
- JSP中的HTML代码会被JSP引擎放入到Servlet的out.write()方法中。而在Servlet中我们需要自己通过对字符流输出流的操作生成响应的页面
- JSP更擅长表现于页面显示，Servlet更擅长于逻辑控制

#### JSP标签的使用

![image-20240519164146723](.\img\image-20240519164146723.png)

#### JSP的三种原始标签

JSP的原始标签在JSP的任何版本中都可以使用

#### <%！ %>声明标签

声明标签用于在JSP中成员变量与方法的定义。标签中的内容会出现在JSP被编译后的Servlet的class的{}中

```jsp
<%!
  int a = 10;
  int add(int a, int b){
    return a + b;
  }
%>
```

![image-20240519165501762](.\img\image-20240519165501762.png)

#### <% %>脚本标签

脚本标签用于在JSP中编写业务逻辑。标签中的内容会出现在JSP被编译后的Servlet的_jspService方法体中。

```jsp
<%
  int c = 20;
%>
```

```java
  public void _jspService(final jakarta.servlet.http.HttpServletRequest request, final jakarta.servlet.http.HttpServletResponse response)
      throws java.io.IOException, jakarta.servlet.ServletException {
      ...
     int c = 20;
      ...
  }
```

#### <%= %>赋值标签

赋值标签用于在JSP中做内容输出。标签中的内容会出现在_jspServlet方法的out.print()方法的参数中。注意我们在使用赋值标签时不需要再代码中添加";"

```jsp
<%=a%>
<%=c%>
```

![image-20240519171303660](.\img\image-20240519171303660.png)

#### JSP原始标签的使用

需求：以20%的概率显示你中奖了

```jsp
<%
        int flag = new Random().nextInt(100);
        String str = "";
        if(flag <= 20){
            str = "中奖了";
        }else{
            str = "没中奖";
        }
    %>
    <%=str%>
```

因为原始标签中不能嵌套原始标签，所以也可以将输出文本放到标签外面

```jsp
    <%
        int flag = new Random().nextInt(100);
        if(flag <= 20){
    %>
    中奖了
        <%}else{%>
    没中奖<%
        }
    %>
```

#### JSP的指令标签

JSP指令标签的作用是声明JSP页面的一些属性和动作

<%@指令名称 属性="值" 属性="值1，值2..."%>

JSP指令标签分类

| page指令标签    | 位于JSP页面的顶端，可以有多个，主要声明JSP页面的一些属性 |
| --------------- | -------------------------------------------------------- |
| include指令标签 | 嵌入另一个JSP页面，同时解析这个JSP页面                   |
| taglib指令标签  | 导入其他标签库                                           |

#### Page指令标签

- contentType 

  设置响应类型和编码

- pageEncoding

  设置页面的编码

- import

  导入所需要的包

- language

  当前JSP页面里面可以嵌套的语言

- session

  设置JSP页面是否获取session内置对象

- buffer

  设置JSP页面的流的缓冲区的大小

- autoFlush

  是否自动刷新

- extends

  声明当前JSP的页面继承于哪个类，必须继承的是HttpServlet及其子类

- isELIgnored

  是否忽略el表达式

- errorPage

  当前JSP页面出现异常的时候要跳转的JSP页面

- isErrorPage

  当前JSP页面是否是一个错误页面。若值为true，可以使用JSP页面的一个内置对象exception

#### Include指令标签

静态包含，可以将其他页面内容包含进来，一起进行编译运行，生成一个java文件

<%@include file="被包含JSP的相对路径"%>

#### Taglib指令标签

导入标签库

<%@taglib prefix="前缀名" url="名称空间" %>

  #### JSP的内置对象

JSP中一共预先定义了9个这样的对象，分别是request、response、session、application、out、pagecontext、config、page、exception

**request对象**

request对象是HttpServletRequest类型的对象

**response对象**

response对象是HttpServletResponse类型的对象

**session对象**

session对象是HttpSession类型的对象。只有在包含session="true"的页面中才可以被使用

**application对象**

application对象是ServletContext类型的对象

**out对象**

out对象是JspWriter类型的对象

**config对象**

config对象是ServletConfig类型的对象

**pageContext对象**

pageContext对象是PageContext类型的对象。作用是取得任何范围的参数，通过它可以获取JSP页面的out、request、reponse、application等对象。pageContext对象的创建和初始化都是由容器来完成的，在JSP页面中可以直接使用pageContext对象

**page对象**

page对象代表JSP本身

**exception对象**

exception对象的作用是显示异常信息，只有在包含isErrorPage=”true"的页面中才可以被使用

#### 请求转发

请求转发是服务端的一种请求方式，相当于在服务端中直接请求某个资源

RequestDispatcher dispatcher = request.getRequestDispatcher("/test.jsp");

dispatcher.forward(request,response);

简写方式

request.getRequestDispatcher("/test.jsp").forword(request,response);

#### 请求转发和重定向的区别

- 请求转发对于客户端浏览器而言是在一次请求与响应中完成，而重定向是在两次请求两次响应中完成
- 请求转发并不会更改客户端浏览器的地址栏中的内容。而重定向会改变客户端浏览器地址栏中的内容
- 请求转发可以使用request对象传递数据，而重定向不能使用request对象传递数据
- 如果是处理的DML操作，建议使用重定向方式为客户端浏览器产生响应，可以解决表单重复提交现象

#### 请求转发案例

需求：在Servlet中获取客户端浏览器所支持的语言，并通过JSP页面将客户端浏览器所支持的语言响应给客户端浏览器

```java
package com.triticale.jspdemo;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
@WebServlet("/language.do")
public class LanguageServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取请求头中所支持的语言
        String header = request.getHeader("Accept-Language");
        request.setAttribute("header",header);
        request.getRequestDispatcher("showMsg.jsp").forward(request,response);
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    String header = (String) request.getAttribute("header");
%>
    <font color="blue"><%=header%></font>
</body>
</html>
```

#### JSP中四大作用域对象

作用域：“数据共享的范围”，也就是说数据能够在多大的范围内有效

| 对象名称    | 作用范围         |
| ----------- | ---------------- |
| application | 整个应用都有效   |
| session     | 在当前会话中有效 |
| request     | 在当前请求中有效 |
| page        | 在当前页面有效   |

#### JSTL标签库

JSTL（Java server pages standard tag library，即JSP标准标签库）JSTL标签是基于JSP页面的。这些标签可以插入在JSP代码中，本质上JSTL也是提前定义好的一组标签，这些标签封装了不同的功能，在页面上调用标签时，就等于调用了封装起来的功能。JSTL的目标是使JSP页面的可读性更强、简化JSP页面的设计、实现代码复用、提高效率



在JSP2.0版本后开始支持JSTL标签库。在使用JSTL标签库时需要在JSP中添加对应的taglib指令标签

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

#### JSTL标签分类

根据JSTL标签所提供的功能，可以将其分为5个类别

**核心标签**

最常用、最重要，也是最基本的标签

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

![img](.\img\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ4MDMzMDAz,size_16,color_FFFFFF,t_70)

**格式化标签**

JSTL格式化标签用来格式化并输出文本、日期、时间、数字

```jsp
<% taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

**SQL标签**

JSTL SQL标签提供了与关系型数据库进行交互的标签

```jsp
<% taglib prefix="sql" uri="http://java.sun.com.jsp/jstl/sql" %>
```

**XML标签**

```jsp
<% taglib prefix="x" uri="http://java.sun.com.jsp/jstl/xml" %>
```

**JSTL函数**

JSTL包含一系列标准函数，大部分是通用的字符串处理函数

```jsp
<% taglib prefix="fn" uri="http://java.sun.com.jsp/jstl/functions" %>
```

#### EL表达式

EL（Expression Language）是一种表达式语言。是为了使JSP写起来更加简单，减少java代码，可以使得获取存储在Java对象中的数据变得简单。在JSP2.0版本后开始支持EL表达式。

语法结构

${表达式}

${对象.属性名}

| 隐含对象         | 描述                          |
| ---------------- | ----------------------------- |
| pageScope        | page作用域                    |
| requestScope     | request作用域                 |
| sessionScore     | session作用域                 |
| applicationScope | application作用域             |
| param            | Request对象的参数，字符集     |
| paramValues      | Request对象的参数，字符串集合 |
| header           | HTTP信息头，字符串            |
| headerValues     | HTTP信息头，字符串集合        |
| initParam        | 上下文初始化参数              |
| cookie           | Cookie值                      |
| pageContext      | 当前页面的pageContext         |

**使用EL表达式取出作用域中的值**

${pageScope.name}

${requestScope.name}

${sessionScope.name}

${applicationScope.name}

获取作用域属性中的数据时，也可以只写属性名，EL表达式会按照pageScope、requestScope、sessionScope、applicationScope==(作用域由小到大)==的顺序查找该属性的值

#{name}

### JSTL标签库与EL表达式的使用

#### JSTL标签库的使用步骤

添加jstl.jar

在JSP页面中添加taglib指令标签

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

上述方式配置失败，Maven玩家可以在pom.xml里面添加依赖

```xml
<dependency>
            <groupId>org.glassfish.web</groupId>
            <artifactId>jakarta.servlet.jsp.jstl</artifactId>
            <version>3.0.1</version>
        </dependency>
        <dependency>
            <groupId>jakarta.servlet.jsp.jstl</groupId>
            <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
            <version>3.0.0</version>
        </dependency>
```

jsp文件taglib需要修改

```jsp
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
```

**\<c:if>**

标签判断表达式的值，如果表达式的值为true则执行其主体内容

```jsp
<c:if test="true">
        Hello JSTL
    </c:if>
```

或者使用EL表达式

```jsp
<c:if test="${1 == 1}">
        Hello JSTL
    </c:if>
```

**\<c:choose>,\<c:when>,\<c:otherwise>**

\<c:choose>标签与Java switch语句的功能一样，用于在众多选项中做出选择

switch语句中有case，而\<c:choose>标签中有\<c:when>,switch语句中有default，而\<c:choose>标签中有\<c:otherwise>

```jsp
<c:choose>
    <c:when test="${1==1}">
        Hello When!
    </c:when>
    <c:when test="${1==1}">
        Hello When2!
    </c:when>
    <c:otherwise>
        Hello Otherwise!
    </c:otherwise>
</c:choose>
```

如果有多个\<c:when>满足，会返回第一个

**\<c:forEach>**

迭代器，用于迭代集合

| 属性      | 描述                       |
| --------- | -------------------------- |
| items     | 被迭代的集合               |
| begin     | 迭代器的起始因子           |
| end       | 迭代器的结束因子           |
| step      | 迭代因子的增长数           |
| var       | 代表当前迭代元素的变量名称 |
| varStatus | 代表循环状态的变量名称     |

varStatus属性

current:当前这次迭代的（集合中的）项

index:当前这次迭代从0开始的迭代索引

count:当前这次迭代从1开始的迭代计数

first:用来表明当前这轮迭代是否为第一次迭代的标志

last:用来表明当前这轮迭代是否为最后一次迭代的标志

begin:属性值

end:属性值

step:属性值

```jsp
<c:forEach begin="0" end="9" step="2" varStatus="tri">
    ForEach  <br/>
</c:forEach>
```

**使用ForEach迭代List**

需求：

创建Users对象，含有userid，username属性

创建一个Servlet，在Servlet中创建多个Users对象并放到List集合中，在showUsers.jsp的页面中显示所有的Users对象的信息

```java
@WebServlet("/findUsers")
public class FindUsersServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Users users1 = new Users(1,"张三");
        Users users2 = new Users(2,"李四");
        List<Users> list = new ArrayList<>();
        list.add(users1);
        list.add(users2);
        request.setAttribute("list",list);
        request.getRequestDispatcher("showUsers.jsp").forward(request,response);
    }
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <table border="1" align="center">
        <tr>
           <th>用户ID</th>
            <th>用户姓名</th>
        </tr>
        <c:forEach items="${requestScope.list}" var="user">
            <tr>
                <td>${user.userid}</td>
                <td>${user.username}</td>
            </tr>
        </c:forEach>

    </table>
</body>
</html>
```

使用ForEach迭代Map

需求：

创建Users对象，含有userid，username属性

创建一个Servlet，在Servlet中创建多个Users对象并放到Map集合中，在showUsers2.jsp的页面中显示所有的Users对象的信息

```java
@WebServlet("/findusers2")
public class FindUsers2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Users users1 = new Users(1,"张三");
        Users users2 = new Users(2,"李四");
        Map<String,Users> map = new HashMap();
        map.put("users1",users1);
        map.put("users2",users2);
        request.setAttribute("map",map);
        request.getRequestDispatcher("showUsers2.jsp").forward(request,response);
    }
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<table border="1" align="center">
    <tr>
        <th>Map的key</th>
        <th>用户ID</th>
        <th>用户姓名</th>
    </tr>
    <c:forEach items="${requestScope.map}" var="user">
        <tr>
            <td>${user.key}</td>
            <td>${user.value.userid}</td>
            <td>${user.value.username}</td>
        </tr>
    </c:forEach>

</table>
</body>
</html>
```

### JSTL格式化标签的使用

```jsp
<%@ taglib prefix="fmt" uri="jakarta.tags.fmt" %>
```

对日期的格式化

```java
@WebServlet("/format")
public class FormatServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setAttribute("date",new Date());
        request.getRequestDispatcher("format.jsp").forward(request,response);
    }
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="jakarta.tags.fmt" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${requestScope.date}
<hr/>
<fmt:formatDate value="${date}" pattern="yyyy-MM-dd"/>
</body>
</html>
```

对数字的格式化

```java
@WebServlet("/format")
public class FormatServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setAttribute("balance",2323324556.226);
        request.getRequestDispatcher("format.jsp").forward(request,response);
    }
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="jakarta.tags.fmt" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${balance}
<hr/>
<fmt:formatNumber value="${balance}" type="currency"/>
</body>
</html>
```

### MVC模式

MVC模式：Model、View、Controller即模型、视图、控制器。是软件的一种架构模式。MVC要实现的目标是将软件的用户界面和业务逻辑分离，可提高代码可扩展性、可复用性、可维护性、以及灵活性

View(视图):用户的操作界面，如:html、jsp

Model(模型)：具体的业务模型与数据模型。如：service、dao、pojo

Controller(控制)：处理从视图层发送的请求，并选取模型层的业务模型完成响应的业务实现，并产生响应。如:Servlet



### MVC模式与应用程序分层的区别

MVC模式是一种软件的架构方式，而应用程序分层这是一种代码的组织方式。MVC模式与应用程序分层都是一致的：为了解耦合、提高代码复用性