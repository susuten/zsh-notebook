### JSP

#### jsp技术原理
1. Java Server Pages
2. 动态页面与静态页面分离，跨平台性
3. JSP = HTML文件 + Java程序片断 + JSP标记
4. 以Servlet技术为基础，提供强大的组件支持功能
5. 是一种开发Web应用的理想构架

#### Jsp机制
```text
所有JSP页面，在执行的时候都会被服务器端的JSP引擎转换为Servelet（.java），然后又由JSP引擎调用Java编译器，将Servelet（.java）编译为Class文件（.class），并由Java虚拟机（JVM）解释执行。同时在%CATALINA_HOME%\work\Catalina\localhost下多出两个文件：_Test_jsp.java和_Test_jsp.class，他们分别就是Servelet和Class文件。然后由Servlet产生HTML页面传回客户端以供显示
```
#### Jsp基本语法
1. Jsp页面结构
```text
声明变量、方法、类<%! %>
JspAVA程序片<% %>
JAVA表达式<%= %>
```
2. Jsp标签
```text
1.指令标签：主要用于为转换阶段提供整个Jsp页面的相关信息，指令不会产生任何的输出到当前的输出流中
（1）包含指令<%@ include %>  用于在Jsp页面中静态包含一个文件，该文件可以是Jsp页面，HTML网页或者一段Java代码
	<%@ include file=“filePath”%>
（2）页指令<%@ page  %> 用于定义JSP页面中的全局属性 包括language、pageEncoding、isErrorPage、（推荐把他放在JSP文件的顶部 ）
2.动作标签：为请求处理阶段提供信息！当Jsp容器遇到这个标签，就用预先定义的对应于该标签的java代码来代替
（1）include：动态包含一个文件：静态文件（txt、html）或动态文件（jsp)
<jsp:include page = "文件名"% flush = "true"%>
take care: flush指示在读入包含内容之前是否清空任何现有的缓冲区，JSP1.1中需要flush属性，在JSP1.2中flush属性缺省为false
（2）forward:这个动作运行在运行时将当前的请求转发给一个静态的资源、Jsp页面或者Servlet。这个请求会终止当前页面的执行，即<jsp:forward>”标签以后的代码，将不能执行
格式：<jsp:forward page={"relativeURL" | "<%= expression %>"} /> 
（3）param：以“名-值”的形式为其他的标签提供附加信息
```
3. Jsp注释 <%--  --%>
4. <%@include file = "文件"%>与<jsp:include page = "文件名"%>
```text
1.指令标签的“包含指令include”，是将静态嵌入文件作为主体文件的一部分，所以主文件和子文件其实是一体。(生成一个java文件)
2.动作标签的“包含指令include”，是动态嵌入文件，子文件不必考虑主文件的属性。（生成多个java文件）
3.指令标签在编译时就将子文件载入
4.动作标签在运行时才将子文件载入
```

#### Jsp内置对象
```text
JSP程序中8个默认的内置对象，对于它们而言，不用声明就可以直接使用。这8个对象分别是request、response、pageContext、session、application、out、config和page。 
```
1. Request
```text
1.范围：请求作用域，就是客户端的一次请求
2.方法：
getCookies()：返回客户端cookie对象，结果是一个cookie数组
getSession(boolean create)：返回和当前客户请求相关联的HttpSession对象
getHeader(String name)：获得http协议定义的传送文件头信息
getAttribute(String name)：返回name指定的属性值，若不存在指定的属性，就返回null
getParameterValues(String name)：以字符串数组的形式返回指定参数所有值
getHeaderName()：返回所有request header的名字，结果集是一个Enumeration类的实例
getMethod()：返回客户端向服务器传送数据的方法，一般有GET、POST、PUT
getParameter(String name)：以字符串的形式返回客户端传来的某一个请求参数的值，该参数名由name指定。当传递给此方法的参数名没有实际参数与之对应时，返回null
```
2. Response：实现HttpServletResponse接口，可对客户的请求做出动态的响应，向客户端发送数据，如Cookie、HTTP文件头信息等
```text
addCookie(Cookie cookie)
addHeader(String name, String value)
containsHeader(String name)
```
3. Session：实际上就是服务器与客户端之间的“会话”
```text
1.范围：全会话作用域，当用户首次访问时，产生一个新的会话，以后服务器就可以记住这个会话状态。生命周期：会话超时，或者服务器端强制使会话失效
2.可以用来存储访问者的一些特定信息。主机服务器就是根据session来跟踪这些信息的
3.session可以创建访问者信息容器
4.session可以用来跟踪访问者的访问路径
5.方法：
getAttribute(String name)
setAttribute(String name,java.lang.Object.value)
removeAttribute(String name)
```
4. Application
```text
1.范围：全局作用范围，整个应用程序共享，就是在部署文件中的同一个webApp共享，生命周期为：应用程序启动到停止
2.方法：
getAttribute(String name)
setAttribute(String name,Object.value)
```
5. Out：out对象用于输出数据，它可以是javax.servlet.jsp.JspWriter的一个实例。out对象的范围是page。
```text
方法：
out.print()
out.newLine()：输出一个换行符号
out.clearBuffer()：清空缓冲区里的数据，并且把数据写到客户端去
out.clear()：清空缓冲区里的数据，但不会把数据写到客户端去
out.getRemaining()：获取缓冲区中没有被占用的空间的大小
```

#### 页面之间的跳转带参数问题
1. 发送参数主要有几种模式
```text
1.URL带参数
<a href = "url">
2.Form带参数
<form name=userform  method="post" action="formclick2.jsp"></form>
3.Forward带参数
<jsp:forward page = “forward2.jsp”></jsp:forward>
4.JavaScript
window.location.href = "JavaScriptmode2.jsp"
5.responsedirect
<%response.sendRedirect("responsedirect2.jsp"); %>
```
2. 接受参数
Request.getParameter() 