<a name="content">目录</a>

[R语言爬虫](#title)
- [基础知识1：HTML基础与R语言解析](#base-html)
	- [在R语言中解析HTML](#decode-html)
- [基础知识2：XML&XPath表达式](#base-xml)
	- [在R语言中解析XML](#decode-xml)
	- [XPath表达式](#xpath)
	- [SelectorGadget自动生成XPath表达式](#selectorgadget)
- [基础知识3：HTTP协议](#base-http)
	- [URL语法](#url)
	- [HTTP消息](#http-message)
- [基础知识4：AJAX与动态网页介绍](#ajax)




<h1 name="title">R语言爬虫</h1>
<p align="right">整理自公众号<strong>数据科学家养成记</strong>系列文章《R语言爬虫系列》</p>

<p align="center"><img src=/picture/Web-crawler.jpg width="800"></img></p>

<a name="base-html"><h3>基础知识1：[HTML基础与R语言解析](https://mp.weixin.qq.com/s/cHQqKulrkBRREPOYF3bGxg) [<sup>目录</sup>](#content)</h3></a>

点击链接查看具体内容

<h4 name="decode-html">在R语言中解析HTML</h4>

在R语言中，我们通常使用XML包中的htmlParse函数来解析一个HTML文件

```
library(XML)
url <- "http://www.r-datacollection.com/materials/html/JavaScript.html"
exmaple1 <- htmlParse(file = url)
```

<a name="base-xml"><h3>基础知识2：[XML&XPath表达式](https://mp.weixin.qq.com/s/yyEJM9eNVJ4B7fqMFCaJ-w) [<sup>目录</sup>](#content)</h3></a>

XML（eXtensible Markup Language）全称叫做可扩展标记语言，首先它和HTML一样，是一门标记语言，那它就该有标记语言的全部特征，这是XML的共性。XML当然必须也有自己的特性，XML是被设计用来传输和存储数据的，这和HTML用来显示数据大不一样，所以XML又有网络数据交换最流行格式的美誉。

参考以下例子：

<img src="/picture/xml.png"></img>

XML的语法规则：
- 一个XML文档永远以声明该文档的一行代码来开头：
```
<?xml version=”1.0” encoding=”ISO-8859-1”?>
```
- XML文档必须要有一个根元素，这个根元素包裹了整个文档，在上面的例子里，根元素是：

```
<nbaplayer>

   ...

<\nbaplayer>
```

-  一个XML元素由起始标签和具体内容来定义，一个元素可以用一个闭合标签来结束，也可以在起始标签里用一个斜杠（/）来闭合。元素里可以包含其他元素、属性、具体数据等其他内容。我们来看上述例子中<city>元素：

```
<city name=”houston”> rockets </city>
```

<h4 name="decode-xml">在R语言中解析XML</h4>

R中导入和解析XML文档的包就叫XML包，我们可以使用 xmlParse( ) 函数来解析XML。

```
library(XML)
nbadata <- xmlParse(file="D:/Rdata/nbadata.xml")
```

<h4 name="xpath">XPath表达式</h4>

简单来说，XPath表达式就是选取XML或者HTML文件中节点的方法，这里的节点，通常是指XML/HTML文档中的元素。

XPath通过路径表达式（Path Expression）来选择节点信息，跟文件系统路径一样使用“/”符号来分割路径。先来看看XPath表达式选择节点的基本规则：

> nodename：选择该节点的所有子节点
> “/”：选择根节点
> “//”：选择任意节点
> “@”：选择属性

还是以前面的XML文档作为例子：
> nbaplayer：选取nbaplayer元素所有的子节点
> /nbaplayer：选取跟节点nbaplayer
> //team：选择所有的team子元素
> //@name：选择所有的name属性值

除此之外，我们还可以通过给表达式附加一些条件来选择指定的数据，所有的筛选条件都可以附在一个\[ \]符号中：
> /nbaplayer/team[1]：选择nbaplayer下第一个team子元素
> //city[@name]：选择带有name属性的team节点

<h4 name="selectorgadget">SelectorGadget自动生成XPath表达式</h4>

使用到一款Chrome 插件：<font color="blue">SelectorGadget</font>

具体使用方法请点击阅读原文，或查看插件官方手册

<a name="base-http"><h3>基础知识3：[HTTP协议](https://mp.weixin.qq.com/s/Z4TbCtBMNEY0RCNNiwv6aw) [<sup>目录</sup>](#content)</h3></a>

HTTP(HypterText Transfer Protocol)，即所谓超文本传输协议。HTTP就是浏览器或者爬虫工具如何来接收网页HTML的方法。

例子：

客户端会话信息：

```
About to connect() to china.nba.com port 80 (#0)
  Trying 127.0.0.1:50049... connected
Connected to china.nba.com(127.0.0.1:50049) port 80 (#0)
Connected #0 to host china.nba.com left intact
```

建立连接之后，服务器会等待请求，浏览器会向服务器发送如下的HTTP请求：

```
GET /index.html  HTTP/1.1
Host: china.nba.com
Accept: text/html,application/xhtml+xml,application/xml;
q=0.9,image/webp,*/*;q=0.8
Proxy-Connection: keep-alive
User-Agent: Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 
(KHTML, like Gecko)Chrome/49.0.2623.221 Safari/537.36 SE 
2.X MetaSr 1.0
```

服务器响应浏览器请求:

```
HTTP/1.1 200 OK
Date:Thu, 14 Sep 2017 06:34:35 GMT
Server:squid/3.5.20
Keep-Alive:timeout=38
Vary:Accept-Encoding
...
<!DOCTYPE html>
<html>
<head>
```

在接受了所有数据之后，连接会被浏览器再次关闭，一次访问就算结束了。

```
closing connection #0
```

<h4 name="url">URL语法</h4>

所谓URL，就是我们平常所说的网址，它的全称叫做统一资源定位符（Uniform Resource Locators）。

总体的URL例子可以表示为：

```
scheme://hostname:port/path?querystring#fragment
```

> - scheme 表示URL的模式，它定义了浏览器和服务器之间通信所采用的协议，NBA主页的例子里采用的模式就是http
> - hostname 主机名,提供了存放我们感兴趣资源的服务器的名字，它是一个服务器的唯一识别符
> - port 端口号,一般默认为80
> 
> 主机名和端口号之后的路径用来确定被请求的资源在服务器上的位置，跟文件系统类似，也是用/符号来分段的。

<h4 name="http-message">HTTP消息</h4>

HTTP消息无论是请求模式还是响应模式，都由起始行（start line）、标头（headers）（也叫消息报头）和正文（body）三部分组成。

> - **起始行（start line）** 每个HTTP消息的第一行，它定义了请求使用的方法，以及所请求资源的路径和浏览器能够处理的HTTP最高版本。
> 
> - **标头（headers）** 浏览器和服务器提供了元信息，以“名字-取值”形式表示的一套标头字段。
>
> - **正文（body）** 正文部分包含纯文本或者二进制数据，这由标头信息中的content-type声明决定。
>
> - **MIME（多用途互联网邮件扩展）类型声明** 这个声明的作用是告诉浏览器或服务器传输过来的是哪种类型的数据。

在请求模式中，最常用的请求方法是**GET**和**POST**方法，在爬虫过程中至关重要。这两个方法都是从服务器请求一个资源，但是在正文的使用上有所不同，**GET方法不会在请求的正文中发送任何内容，但POST会用正文来发送数据**。

GET请求如下：

```
GET/form.html HTTP/1.1(CRLF)
```

在R中，RCurl包提供了一些高级函数来执行GET请求从Web服务器上获取某个资源，最常用的函数的getURL()，这个函数会自动确定主机、端口以及请求的资源。

```
library(RCurl)
getURL（http://nbachina.qq.com/a/20170914/004815.htm)
```

POST请求如下：

```
POST/greetings.html HTTP/1.1
```
在R中执行POST请求，无需手动构建，而是可以使用postForm()函数：

```
url<-“http://www.r-datacollection.com/materials/http/POSTexample.php”
cat(postForm(url,name=“Kobe”,age=39,style=“post”))
Hello Kobe!
You are 39 years old.
```

常见的HTTP请求方法如下：

| 方法 | 描述 |
|:-----|:-----|
|GET|从服务器检索资源|
|POST|利用消息向服务器发送数据，然后从服务器检索资源|
|HEAD|从服务器检索资源，但只响应起始行和标头|
|PUT|将请求的正文保存在服务器上|
|DELETE|从服务器删除一个资源|
|TRACE|追踪消息到达服务器沿途的路径|
|OPTIONS|返回支持的HTTP方法清单|
|CONNECT|建立一个网络连接|

常见的HTTP状态码如下所示：

> 1xx：指示信息--表示请求已接收，继续处理
> 2xx：成功--表示请求已被成功接收、理解、接受
> 3xx：重定向--要完成请求必须进行更进一步的操作
> 4xx：客户端错误--请求有语法错误或请求无法实现
> 5xx：服务器端错误--服务器未能实现合法的请求

常见的200表示成功找到资源，404表示未找到资源，500表示服务器内部错误，502表示错误网关等。

<a name="ajax"><h3>基础知识4：[AJAX与动态网页介绍](https://mp.weixin.qq.com/s/9SbbbP2PPMhRW-GgLe3kvg) [<sup>目录</sup>](#content)</h3></a>

<p align="center"><img src=/picture/ajax.jpg width="700"></img></p>

一个动态网页的例子：知乎首页是一个动态网站（DHTML），具体表现就是从首页不断下拉，网页内容在不断变化但url却一直都是https://www.zhihu.com/ ，或者是点击了某个地方内容也发生了变化但抬头一看地址栏，url依然没有变化。对于动态网页，再用静态网页的方法来抓取已经行不通了。

网络技术实现从静态到动态转变的一个关键角色是汇总于AJAX这个术语下的一组技术，所谓AJAX，全称叫做异步JavaScript和XML（Asynchronous JavaScript and XML）,它是一组技术，不同的浏览器有自己的AJAX实现组件，有了AJAX技术之后，就不需要对整个网页进行刷新了，局部更新既不占用宽带又可以提高加载速度有没有。

简单来说，就是在静态的html中添加了可以动态执行的JS脚本（以<script>标签的形式添加），JS脚本的执行能刷新原先静态html的部分内容，从而实现网页的动态显示。在当前浏览器显示中对HTML信息进行修改称之为DOM操作（文档对象模型），这些操作构成了产生产生动态浏览器行为的基本过程，JavaScript可供支持的修改操作有很多，HTML元素和属性可以添加移动删除，CSS样式也可以修改。
