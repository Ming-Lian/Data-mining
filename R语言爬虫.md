<a name="content">目录</a>

[R语言爬虫](#title)
- [基础知识1:HTML基础与R语言解析](#base-html)
	- [在R语言中解析HTML](#decode-html)
- [基础知识2：XML&XPath表达式](#base-xml)
	- [在R语言中解析XML](#decode-xml)
	- [XPath表达式](#xpath)
	- [SelectorGadget自动生成XPath表达式](#selectorgadget)
- [基础知识3：HTTP协议](#base-http)
	- [URL语法](#url)




<h1 name="title">R语言爬虫</h1>
<p align="right">整理自公众号<strong>数据科学家养成记</strong>系列文章《R语言爬虫系列》</p>

<img src=/picture/Web-crawler.jpg></img>

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

<h4 name="url>URL语法</h4>

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
