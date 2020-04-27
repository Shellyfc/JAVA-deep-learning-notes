# 过滤器
常⻅的过滤器用途主要包括:对用户请求进行统一认证、对用户的访问请求进行记录和审核、对用户发送的数据进行过滤或替换、转换图象格式、对响应内容进行压缩以减少传输量、对请求或响应进行加解 密处理、触发资源访问事件、对XML的输出应用XSLT等。
#监听器
监听器涉及三个组件:**事件源，事件对象，事件监听器**

- ServletContextListener: 对Servlet上下文的创建和销毁进行监听

- ServletContextAttributeListener: 监听Servlet上下文属性的添加、删除和替换

- HttpSessionListener： 对Session的创建的销毁进行监听
- HttpSessionAttributeListener： 对Session对象中属性的添加、删除和替换进行监听
- ServletRequestListener： 对请求对象的初始化和销毁进行监听
- ServletRequestAttrubuteLister：对请求对象属性的添加、删除和替换进行监听