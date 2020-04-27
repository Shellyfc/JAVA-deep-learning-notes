# Session
Session 是另一种记录浏览器状态的机制。不同的是Cookie保存在浏览器中，Session保存在服务器中。用户使用浏览器访问服务器的时候，服务器把用户的信息以某种的形式记录在服务器，这就是Session

## Session API
long getCreationTime();【获取Session被创建时间】

**String getId();【获取Session的id】**

long getLastAccessedTime();【返回Session最后活跃的时间】

ServletContext getServletContext();【获取ServletContext对象】

**void setMaxInactiveInterval(int var1);【设置Session超时时间】**

**int getMaxInactiveInterval();【获取Session超时时间】**

**Object getAttribute(String var1);【获取Session属性】**

Enumeration getAttributeNames();【获取Session所有的属性名】

**void setAttribute(String var1, Object var2);【设置Session属性】**

**void removeAttribute(String var1);【移除Session属性】**

**void invalidate();【销毁该Session】**

boolean isNew();【该Session是否为新的】



- 一般来讲，当我们要存进的是用户级别的数据就用Session，那什么是用户级别呢？只要浏览器不关闭，希望数据还在，就使用Session来保存。

- 服务器为Session自动维护的Cookie的maxAge属性默认是-1的，当浏览器关闭掉了，该Cookie就自动消亡了。当用户再次访问的时候，已经不是原来的Cookie了。


## 应用
懒得做了