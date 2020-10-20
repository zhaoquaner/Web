# HttpServletRequest接口



## 介绍

- HttpServletRequest接口是由Http服务器负责提供
- HttpServletRequest接口负责在doGet/doPost方法运行时读取Http协议包中的信息
- 将HttpServletRequest接口修饰的对象叫做请求对象



## 作用

- 可以读取Http请求协议包中 请求行的信息
- 可以读取Http请求包中 请求头或者 请求体
- 可以代替浏览器向服务器申请资源文件调用



可以使用该请求对象的一系列getXXX()方法来获取请求协议包的信息，例如使用getRequestURL()、getMethod()可以获得请求的URL地址和请求方式。

使用`request.getParameterNames()`来获取所有参数的名称，使用`request.getParameter(parameterName)`来获取对应变量的值。

Get和Post方式对应的doGet和doPost方法使用方法基本一样，**但有一些差别：浏览器以Get方式发送请求，参数保存在请求头中，以Post方法发送请求，参数保存在请求体中**。

请求头中二进制内容由Tomcat解码，Tomcat9.0默认使用UTF-8进行解码，请求体中二进制内容由当前request对象负责解码，**request默认使用 ISO-8859-1解码，无法解码中文。**

所以在Post请求方式下，在读取请求体中内容之前，要让请求对象使用utf-8字符集进行解码，使用`request.setCharacterEncoding("UTF-8");`，即可改变字符集为UTF-8。