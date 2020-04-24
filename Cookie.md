# Cookie

- 网页之间的交互是通过HTTP协议传输数据的，而Http协议是无状态的协议。无状态的协议是什么意思呢？一旦数据提交完后，浏览器和服务器的连接就会关闭，再次交互的时候需要重新建立新的连接。

- 服务器无法确认用户的信息，于是乎，W3C就提出了：给每一个用户都发一个通行证，无论谁访问的时候都需要携带通行证，这样服务器就可以从通行证上确认用户的信息。通行证就是Cookie

- Cookie的流程：浏览器访问服务器，如果服务器需要记录该用户的状态，就使用response向浏览器发送一个Cookie，浏览器会把Cookie保存起来。当浏览器再次访问服务器的时候，浏览器会把请求的网址连同Cookie一同交给服务器。

## Cookie API
Cookie类用于创建一个Cookie对象

response接口中定义了一个addCookie方法，它用于在其响应头中增加一个相应的Set-Cookie头字段

request接口中定义了一个getCookies方法，它用于获取客户端提交的Cookie

## 常用的Cookie方法：

public Cookie(String name,String value)

setValue与getValue方法

setMaxAge与getMaxAge方法

setPath与getPath方法

setDomain与getDomain方法

getName方法

cookie.setMaxAge(10*60);

## Cookie的有效期
Cookie的有效期是通过setMaxAge()来设置的。

- 如果MaxAge为正数，浏览器会把Cookie写到硬盘中，只要还在MaxAge秒之前，登陆网站时该Cookie就有效【不论关闭了浏览器还是电脑】

- 如果MaxAge为负数，Cookie是临时性的，仅在本浏览器内有效，关闭浏览器Cookie就失效了，Cookie不会写到硬盘中。Cookie默认值就是-1。这也就为什么在我第一个例子中，如果我没设置Cookie的有效期，在硬盘中就找不到对应的文件。

- 如果MaxAge为0，则表示删除该Cookie。Cookie机制没有提供删除Cookie对应的方法，把MaxAge设置为0等同于删除Cookie

```
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/cookieEx")
public class CookieEx extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");

        Cookie cookie = new Cookie("username","shellyfc");

        cookie.setMaxAge(1000);

        resp.addCookie(cookie);



        resp.getWriter().write("发送了一个cookie");
    }
}
```

![](./src/cookie查看.png)
chrome-inspect-application-cookies


### 中文的存储


	String name = "中文的value";
	
	Cookie cookie = new Cookie("info", URLEncoder.encode(name, "UTF-8"));
	
	Cookie[] cookies = req.getCookies(); //会获得所有的cookies
        for( int i = 0; cookies != null && i < cookies.length; i++){
            String name = cookies[i].getName();

            String value2 = URLDecoder.decode(cookies[i].getValue(),"UTF-8");

            resp.getWriter().write(name + " " + value2);
        }
        
### Cookie的路径

	cookie.setPath("/myServlet/MyServlet");
	
只有设置的路径才能获取cookie，当加上一个path之后，这个cookie已经变了，所以会有两个cookies



## Cookie应用
### 显示用户上次访问的时间
先检查（遍历）所有Cookie有没有我要的，如果得不到我想要的Cookie，Cookie的值是null，那么就是第一次登陆。
```
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;

@WebServlet("/cookieLastLogin")
public class CookieLastLogin extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter printWriter = resp.getWriter();


        Cookie[] cookies = req.getCookies();

        String cookieValue = null;
        for ( int i = 0; cookies != null && i < cookies.length; i++){
            if ( cookies[i].getName().equals("time")){
                printWriter.write("您上次登录的时间是： ");
                cookieValue = cookies[i].getValue();
                printWriter.write(cookieValue);

                cookies[i].setValue(simpleDateFormat.format(new Date()));
                resp.addCookie(cookies[i]);

                break;
            }
        }

        if(cookieValue == null ){
            Cookie cookie = new Cookie("time", simpleDateFormat.format(new Date()));
            cookie.setMaxAge(20000);
            resp.addCookie(cookie);
            printWriter.write("您是第一次登录");
        }
    }

```



### 显示上次浏览过商品









