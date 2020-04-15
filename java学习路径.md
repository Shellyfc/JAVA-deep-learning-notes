#JAVA 学习
##1. JAVA基础
###流程控制
###面向对象的概念
###Java语法
###this指针、重写和重载、final、static等等这些基础的东西
###集合(包括泛型)
###常用的集合类
###IO流
###IO流代码的编写  
##2. JavaWeb的路线
### Tomcat(简单过一下)
启动：

	sudo sh ./startup.sh
结束：

	sh ./shutdown.sh
	
文件放在WEBAPPS文件下
### XML/注解(简单过一下)
### Servlet(重点理解)
Servlet其实就是一个**遵循Servlet开发的java类**。Serlvet是由**服务器调用的，运行在服务器端**。
Servlet带给我们最大的作用就是能够**处理浏览器带来HTTP请求，并返回一个响应给浏览器，从而实现浏览器和服务器的交互**。


ex. MyServlet
![project](./src/project.png)

```
// MyServlet.java

import java.io.*;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/MyServlet") //完成servlet配置
public class MyServlet extends HttpServlet {
    private String message;

    @Override
    public void init() throws ServletException
    {
        // Do required initialization
        message = "Hello World from MyServlet";
    }

    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException
    {
        // Set response content type
        response.setContentType("text/html");
        // Actual logic goes here.
        PrintWriter out = response.getWriter();
        out.println("<h1>" + message + "</h1>");
    }


    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{

    }


    @Override
    public void destroy()
    {
        // do nothing.
    }
}

```
#### **两种配置servlet方法**

1. 在class前

```
@WebServlet("MyServlet")
```
2. 在web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>MyServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/MyServlet</url-pattern>
    </servlet-mapping>
</web-app>
```
然后访问 [http://localhost:8080/myServlet/MyServlet](http://localhost:8080/myServlet/MyServlet)

#### 缺省servlet

```
<servlet-mapping>   
<servlet-name>MyServlet</servlet-name>
<url-pattern>/</url-pattern>
</servlet-mapping>
```

### HTTP协议(重点理解)
HTTP协议就是客户端和服务器交互的一种通迅的格式。
当在浏览器中点击这个链接的时候，浏览器会向服务器发送一段文本，告诉服务器请求打开的是哪一个网页。服务器收到请求后，就返回一段文本给浏览器，浏览器会将该文本解析，然后显示出来。这段文本就是遵循HTTP协议规范的。
####HTTP请求
浏览器向服务器请求某个web资源时，称之为浏览器向服务器发送了一个http请求。

一个完整http请求应该包含三个部分：

1.  请求行【描述客户端的**请求方式、请求的资源名称，以及使用的HTTP协议版本号**】
	. ```GET /java.html HTTP/1.1```
	
	1.1. 其中GET为**请求方式**，有*POST, GET, HEAD, OPTIONS, DELETE, TRACE, PUT*
	
	1.2. **POST**: 用来提交数据，在URL地址后附带的参数是有限制的，其数据容量通常不能超过1K。
	
	1.3. **GET**: 用来查询数据，可以在请求的实体内容中向服务器发送数据，传送的数据量无限制。
	
	1.4. GET的速度比POST的速度快
	
2.  多个请求头【描述客户端请求哪台主机，以及客户端的一些环境信息等】

	2.1. Accept: text/html,image/* 【浏览器告诉服务器，它支持的数据类型】
	
	2.2. Accept-Charset: ISO-8859-1 【浏览器告诉服务器，它支持哪种字符集】
	
	2.3. Accept-Encoding: gzip,compress 【浏览器告诉服务器，它支持的压缩格式】
	
	2.4. Accept-Language: en-us,zh-cn 【浏览器告诉服务器，它的语言环境】
	
	2.5. Host: www.it315.org:80【浏览器告诉服务器，它的想访问哪台主机】
	
	2.6. If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，缓存数据的时间】
	
	2.7. Referer: http://www.it315.org/index.jsp【浏览器告诉服务器，客户机是从那个页面来的---反盗链】
	
	2.8. User-Agent: Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)【浏览器告诉服务器，浏览器的内核是什么】
	
	2.9. Cookie【浏览器告诉服务器，带来的Cookie是什么】
	
	2.10. Connection: close/Keep-Alive 【浏览器告诉服务器，请求完后是断开链接还是保持链接】
	
	2.11. Date: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，请求的时间】
	
3. 一个空行

#### HTTP响应
**一个HTTP响应代表着服务器向浏览器回送数据**

一个完整的HTTP响应应该包含四个部分:
1. 一个状态行【用于描述服务器对请求的**处理结果**。】
格式： HTTP版本号　状态码　原因叙述

***状态行：HTTP/1.1 200 OK***

状态码用于表示服务器对请求的**处理结果**，它是一个**三位的十进制数。响应状态码分为5类**

|状态码 | 含义|
|------|----|
|100~199  | 成功接收请求，要求客户端继续提交下一次请求才能完成整个处理过程 |
|200~299  | 成功接收请求并完成整个处理过程，常用200 |
|300~399  | 完成请求，需进一步细化请求 |
|400~499  | 客户端请求有误，常用404  |
|500~599  | 服务器端出现错误，常用500 |


2. 多个响应头【用于描述**服务器的基本信息**，以及数据的描述，服务器通过这些数据的描述信息，可以**通知客户端如何处理等一会儿它回送的数据**】

	Location: http://www.it315.org/index.jsp 【服务器告诉浏览器要跳转到哪个页面】
	
	Server:apache tomcat【服务器告诉浏览器，服务器的型号是什么】
	
	Content-Encoding: gzip 【服务器告诉浏览器数据压缩的格式】
	
	Content-Length: 80 【服务器告诉浏览器回送数据的长度】
	
	Content-Language: zh-cn 【服务器告诉浏览器，服务器的语言环境】
	
	Content-Type: text/html; charset=GB2312 【服务器告诉浏览器，回送数据的类型】
	
	Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器该资源上次更新时间】
	Refresh: 1;url=http://www.it315.org【服务器告诉浏览器要定时刷新】
	
	Content-Disposition: attachment; filename=aaa.zip【服务器告诉浏览器以下载方式打开数据】
	
	Transfer-Encoding: chunked 【服务器告诉浏览器数据以分块方式回送】
	
	Set-Cookie:SS=Q0=5Lb_nQ; path=/search【服务器告诉浏览器要保存Cookie】
	Expires: -1【服务器告诉浏览器不要设置缓存】
	
	Cache-Control: no-cache 【服务器告诉浏览器不要设置缓存】
	
	Pragma: no-cache 【服务器告诉浏览器不要设置缓存】
	
	Connection: close/Keep-Alive 【服务器告诉浏览器连接方式】
	
	Date: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器回送数据的时间】
	
3. 一个空行

4. 实体内容【服务器向客户端回送的数据】


### Filter过滤器(重点理解)
### Listener监听器(简单过一下)
### JSP(简单过一下)
### AJAX、JSON(简单过一下)
####起码已经可以使用request对象来接收前端发送过来的数据，使用response对象将Java后端的数据返回给前端，使用Filter拦截器来处理中文乱码问题(Tomcat默认的编码是ISO-88591)。总的来说已经可以实现前后端交互了！
##3. 学习数据库
### 创建表(create table)
### 增删改查(insert,delete,update,select)
### 对于存储过程、触发器这些了解一下即可
### 对于索引、锁后面再学(此部分很重要，但以快速入门来说，可以先不看)
##4. 学习Java连接数据库(JDBC)
### 导入MySQL或者Oracle驱动包
### 装载数据库驱动程序
### 获取到与数据库连接
### 获取可以执行SQL语句的对象
### 执行SQL语句
### 关闭连接
##5. 项目管理和框架的学习
##6. Linux学习
##总结
### Java基础-->流程控制-->面向对象(包括Java语法)-->Java集合-->Java IO流-->异常-->多线程-->网络编程-->反射
### JavaWeb基础-->HTML/CSS/JavaScript/jQuery-->Tomcat-->XML/注解->Servlet-->HTTP-->Filter过滤器和监听器-->JSP-->AJAX/JSON-->数据库(MySQL)-->JDBC和DbUtils
### 项目管理和框架-->Maven-->SpringBoot
### Linux基本命令
##学习目标：
### 以Maven来管理我们的项目
### 前端通过BootStrap来搭建页面框架
### SpringBoot来搭建Java后端环境，SpringMVC处理前端请求(SpringBoot整合了)
### DAO层使用DbUtils组件来完成，MySQL作为数据库