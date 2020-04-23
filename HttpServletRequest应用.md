# HttpServletRequest

HttpServletRequest对象代表**客户端的请求**，当客户端通过HTTP协议访问服务器时，**HTTP请求头中的所有信息都封装在这个对象中**，开发人员通过这个对象的方法，可以获得客户这些信息。

##常用方法

### 获得客户机【浏览器】信息
**getRequestURL方法返回客户端发出请求时的完整URL。**

getRequestURI方法返回请求行中的资源名部分。

**getQueryString 方法返回请求行中的参数部分。**

getPathInfo方法返回请求URL中的额外路径信息。额外路径信息是请求URL中的位于Servlet的路径之后和查询参数之前的内容，它以“/”开头。

**getRemoteAddr方法返回发出请求的客户机的IP地址**

getRemoteHost方法返回发出请求的客户机的完整主机名

getRemotePort方法返回客户机所使用的网络端口号

**getLocalAddr方法返回WEB服务器的IP地址。**

getLocalName方法返回WEB服务器的主机名

### 获得客户机请求头
**getHeader方法**

getHeaders方法

getHeaderNames方法

### 获得客户机请求参数(客户端提交的数据)
**getParameter方法**

**getParameterValues（String name）方法**

getParameterNames方法

getParameterMap方法

## 应用
### 防盗链
```
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/refererServlet")
public class refererServlet extends HttpServlet {

    @Override
    public void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException{
        String referer = req.getHeader("referer");
        System.out.println(referer);
        if(referer != null && referer.contains("localhost")){
            resp.setContentType("text/html;charset=UTF-8");
            resp.getWriter().write("正常显示");

        } else {
            resp.setContentType("text/html;charset=UTF-8");
            resp.getWriter().write("盗链");
//            resp.sendRedirect("/myServlet/index.jsp");
        }

    }

    @Override
    public void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
        doGet(req, resp);
    }
}
```

### 表单提交数据【通过post方式提交数据】
```
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Arrays;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/HelloForm")
public class HelloForm extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public HelloForm() {
        super();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");

        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String gender = req.getParameter("gender");

        String[] hobbies = req.getParameterValues("hobbies");
        String[] address = req.getParameterValues("address");

        String description = req.getParameter("textarea");
        String hiddenValue = req.getParameter("aaa");

        System.out.println(username);
        System.out.println(password);
        System.out.println(gender);
        System.out.println(Arrays.toString(hobbies));
        System.out.println(address);
        System.out.println(hiddenValue);

    }
}

/*
chufang
色法
女
[跑步, 飞翔]
[Ljava.lang.String;@3a4fbdbb
my name is cf
*/

```

### 超链接方式提交数据
	sendRedirect("servlet的地址?参数名=" + 参数值 & "参数名=" + 参数值);
	
	
	
```
//index.jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
    <a href="/myServlet/refererServlet">refererServlet</a>
    <a href ="/myServlet/MyServlet?username=ccf">使用超链接将数据带给浏览器</a>
</body>
</html>
```

```
//在MyServlet doGet()
String username=request.getParameter("username");
System.out.println(username);
```


### 实现转发
之前讲过使用**response的sendRedirect()**可以实现重定向，做到的功能是页面跳转，使用**request的getRequestDispatcher.forward(request,response)实现转发**，做到的功能也是页面跳转，他们有什么区别呢？

**给服务器用的直接从资源名开始写，给浏览器用的要把应用名写上**

request.getRequestDispatcher("/资源名 URI").forward(request,response)

转发时"/"代表的是本应用程序的根目录 myServlet

response.send("/web应用/资源名 URI");

重定向时"/"代表的是webapps目录 myServlet上级目录

