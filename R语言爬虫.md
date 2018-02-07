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
- [基础知识4：AJAX与动态网页介绍](#base-ajax)
- [基础知识5：正则表达式与字符串处理函数](#base-regexp)
	- [基础文本处理函数中正则表达式的应用](#regexp-base-function)
	- [stringr包文本处理函数中的正则表达式的应用](#regexp-stringr-function)
- [实战1：静态数据抓取范例](#practice-1-stable)
- [实战2：动态数据抓取范例](#practice-2-dynamic)
	- [GET请求抓取微信好友列表数据](#wechat-friendlist)
	- [POST请求抓取网易云课堂数据](#netease-study)




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

<p align="center"><img src=/picture/xml.jpg width="600"></img></p>

XML（eXtensible Markup Language）全称叫做可扩展标记语言，首先它和HTML一样，是一门标记语言，那它就该有标记语言的全部特征，这是XML的共性。XML当然必须也有自己的特性，XML是被设计用来传输和存储数据的，这和HTML用来显示数据大不一样，所以XML又有网络数据交换最流行格式的美誉。

参考以下例子：

<img src="/picture/xml-example.png"></img>

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

<p align="center"><img src=/picture/http.png width="600"></img></p>

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

<a name="base-ajax"><h3>基础知识4：[AJAX与动态网页介绍](https://mp.weixin.qq.com/s/9SbbbP2PPMhRW-GgLe3kvg) [<sup>目录</sup>](#content)</h3></a>

<p align="center"><img src=/picture/ajax.jpg width="700"></img></p>

一个动态网页的例子：知乎首页是一个动态网站（DHTML），具体表现就是从首页不断下拉，网页内容在不断变化但url却一直都是https://www.zhihu.com/ ，或者是点击了某个地方内容也发生了变化但抬头一看地址栏，url依然没有变化。对于动态网页，再用静态网页的方法来抓取已经行不通了。

网络技术实现从静态到动态转变的一个关键角色是汇总于AJAX这个术语下的一组技术，所谓AJAX，全称叫做异步JavaScript和XML（Asynchronous JavaScript and XML）,它是一组技术，不同的浏览器有自己的AJAX实现组件，有了AJAX技术之后，就不需要对整个网页进行刷新了，局部更新既不占用宽带又可以提高加载速度有没有。

简单来说，就是在静态的html中添加了可以动态执行的JS脚本（以<script>标签的形式添加），JS脚本的执行能刷新原先静态html的部分内容，从而实现网页的动态显示。在当前浏览器显示中对HTML信息进行修改称之为DOM操作（文档对象模型），这些操作构成了产生产生动态浏览器行为的基本过程，JavaScript可供支持的修改操作有很多，HTML元素和属性可以添加移动删除，CSS样式也可以修改。

XHR：DHTML中数据的获取机制是怎么样的？
> 如果说JavaScript是将HTML变成DHTML的话，那么XHR就是将传统的HTTP协议同步请求通信变成异步发起HTTP请求。传统上HTTP协议的同步通信通常意味着在网络服务器处理一个新的网页过程中，用户和浏览器之间的交互是无效的。而支持在浏览器与Web服务器之间进行持续的信息交换的方法就是所谓的XHR（XMLHttpRequest）。
> 
> XHR在DHTML中的数据获取机制如下：
> 1. 用户开始通过任何浏览器可识别的事件发起一个AJAX请求，比如说点击一个按钮，下拉一个菜单之类的，然后JavaScript会将这个把这个请求作为一个实例化的XHR对象；
> 2. 这个XHR对象会向服务器发起一个对特定文件的请求，请求一般从后台发出，所以不影响用户与网页的交互；
> 3. 请求在服务器端会被接受和处理，相应的数据就会通过XHR对象发回给浏览器客户端；
> 4. 数据到了客户端会被接受，该事件就会被触发然后被某个事件处理器所捕获。

在XHR的实际使用过程中，一般可以加载HTML/XML和JSON等数据类型。

**使用Web开发者工具查看真实的requests地址**

- 键盘按F12键进入web开发者工具
- 在WDT（Web Development Tools）面板选择网络标签，点击该标签下的XHR子标签

![](/picture/WDT-tutorial1.png)

- 按F5刷新网页，可以看到XHR资源列表，每个资源包含包括文件名、状态码和类型等信息

![](/picture/WDT-tutorial2.png)

- 鼠标左键点击选中其中一个XHR资源，在工具右侧面板会显示该资源的具体信息，由7个标签构成，分别为：消息头、Cookie、参数等。其中**消息头**标签下的请求网址就是我们真实要请求的url

![](/picture/WDT-tutorial3.png)

再看**参数**标签

![](/picture/WDT-tutorial4.png)

可以看出url的构造规律：
```
https://mp.weixin.qq.com/mp/appmsgreport?action=page_time&uin=&key=&pass_ticket=&wxtoken=&devicetype=&clientversion=&appmsg_token=&x5=0&f=json

action:page_time
appmsg_token:	
clientversion:	
devicetype:
f:json
key:
pass_ticket:
uin:
wxtoken:
x5:0
```

则可以根据规律，通过字符串的拼接即可构造准确的url资源请求，然后按照批量下载的方式利用RCurl包对其进行解析即可。

<a name="base-regexp"><h3>基础知识5：[正则表达式与字符串处理函数](https://mp.weixin.qq.com/s/wA2uinKA59Fmt90UdSC69A) [<sup>目录</sup>](#content)</h3></a>

<p align="center"><img src=/picture/regexp.jpg width="600"></img></p>

在R语言中，有两种风格的正则表达式可以实现，**一种**就是在基本的正则表达式基础上进行扩展，这和相应的R字符串处理函数相关，**另一种**就是Perl正则表达式，这种风格的正则我们在R中一般不常用。

R默认的正则表达式风格包括**基础文本处理函数**和**stringr包**中的文本处理函数。在R中二者都支持正则表达式，也都具备基本的文本处理能力，但基础函数的一致性要弱很多，在函数命名和参数定义上很难让人印象深刻。stringr包是Hadley Wickham开发了一款专门进行文本处理的R包，它对基础的文本处理函数进行了扩展和整合，在一致性和易于理解性上都要优于基础函数。

正则表达式语法参考：http://www.jb51.net/shouce/jquery1.82/regexp.html

R中基础文本处理函数和stringr包文本处理函数对于正则表达式的支持情况如下表所示：

![](/picture/regexp-function.png)

<h4 name="regexp-base-function">基础文本处理函数中正则表达式的应用</h4>

```
example_text1 <- c("23333#RRR#PP","35555#CCCC","louwill#2017")
#以#进行字符串切分
unlist(strsplit(example_text1, "#")) 
[1] "23333"   "RRR"     "PP"      "35555"   "CCCC"    "louwill" "2017" 
#以空字符集进行字符串切分  
unlist(strsplit(example_text1, "\\s"))
[1] "23333#RRR#PP" "35555#CCCC"   "louwill#2017"
#以空字符替换字符串第一个#匹配
sub("#","", example_text1)
[1] "23333RRR#PP" "35555CCCC"   "louwill2017"
#以空字符集替换字符串全部#匹配
gsub("#","",example_text1)
[1] "23333RRRPP"  "35555CCCC"   "louwill2017"
#查询字符串中是否存在3333或5555的特征并返回所在位置
grep("[35]{4}", example_text1)
[1] 1 2
#查询字符串中是否存在3333或5555的特征并返回逻辑值
grepl("[35]{4}", example_text1)
[1]  TRUE  TRUE FALSE
#返回匹配特征的字符串
pattern <- "[[:alpha:]]*(,|#)[[:alpha:]]"
m <- regexpr(pattern, example_text1)
regmatches(example_text1, m)
[1] "#R" "#C"
```

<h4 name="regexp-stringr-function">stringr包文本处理函数中的正则表达式的应用</h4>
包内所有函数均以str_开头，后面单词用来说明该函数的含义，相较于基础文本处理函数，stringr包函数更容易直观地理解。

```
example_text2 <- "1. A small sentence. - 2. Another tiny sentence."
library(stringr)
#提取small特征字符
str_extract(example_text2, "small")
[1] "small"
#提取包含sentence特征的全部字符串
unlist(str_extract_all(example_text2, "sentence"))
[1] "sentence" "sentence"
#提取以1开始的字符串
str_extract(example_text2, "^1")
[1] "1"
#提取以句号结尾的字符
unlist(str_extract_all(example_text2, ".$"))
[1] "."
#提取包含tiny或者sentence特征的字符串
unlist(str_extract_all(example_text2, "tiny|sentence"))
[1] "sentence" "tiny"     "sentence"
#点号进行模糊匹配
str_extract(example_text2, "sm.ll")
[1] "small"
#中括号内表示可选字符串
str_extract(example_text2, "sm[abc]ll")
[1] "small"
str_extract(example_text2, "sm[a-p]ll")
[1] "small"
```

R预先定义了一些字符集方便大家调用，如下表所示:

![](/picture/regexp-strsets.png)

<a name="practice-1-stable"><h3>实战1：[静态数据抓取范例]() [<sup>目录</sup>](#content)</h3></p>

rvest包+SelectorGadget抓取链家杭州二手房数据 

```
#加载所需的包
library("xml2")
library("rvest")
library("dplyr")
library("stringr")

#对爬取页数进行设定并创建数据框

i<-1:100
house_inf<-data.frame()

#使用for循环进行批量数据爬取（发现url的规律，写for循环语句）
for (i in 1:100){
web<- read_html(str_c("http://hz.lianjia.com/ershoufang/pg",i),encoding="UTF-8")

#用SelectorGadget定位节点信息并爬取房名
house_name<-web%>%html_nodes(".houseInfo a")%>%html_text()
#爬取二手房基本信息并消除空格
house_basic_inf<-web%>%html_nodes(".houseInfo")%>%html_text()
house_basic_inf<-str_replace_all(house_basic_inf," ","")

#SelectorGadget定位节点信息爬取地址
house_address<-web%>%html_nodes(".positionInfo a")%>%html_text()
#SelectorGadget定位节点信息爬取总价
house_totalprice<-web%>%html_nodes(".totalPrice")%>%html_text()
#SelectorGadget定位节点信息爬取单价
house_unitprice<-web%>%html_nodes(".unitPrice span")%>%html_text()

#创建数据框存储以上信息
house<-data_frame(house_name,house_basic_inf,house_address,house_totalprice,house_unitprice)
 house_inf<-rbind(house_inf,house)
}

#将数据写入csv文档
write.csv(house_inf,file="D:/Rdata/datasets/house_inf.csv")
```

<a name="practice-2-dynamic"><h3>实战2：[动态数据抓取范例](https://mp.weixin.qq.com/s/BbtfaX0sHmuk9gmlRiNJKw) [<sup>目录</sup>](#content)</h3></p>

在DHTML中，经常出现的情景是，我明明在网页上看到这个数据了，但到后台HTML中却找不到了，这通常就是XHR在作祟。这时候我们就**不要看原始的HTML数据了，需要进行二次请求，通过web开发者工具找到真实请求的url**。下面就以两个网页为例，分别通过GET和POST请求拿到动态网页数据，全过程主要使用httr包来实现，httr包可谓是RCurl包的精简版，说其短小精悍也不为过。

<h4 name="wechat-friendlist">GET请求抓取微信好友列表数据</h4>

首先登录个人微信网页版，右键打开web开发者工具，下来一大堆请求：

![](/picture/wechat-friendlist1.png)

简单找一下发现网页中的微信好友列表信息并没有呈现在HTML 中，大概可以断定微信好友数据是通过动态加载来显示的，所以直接定位到XHR中，然而XHR列表中的资源太多，哪个里保存着我们需要的数据呢？一个办法是看数据包的大小，估计一下自己的目标数据的大小，缩小查找范围，然后再一一点开剩下的几个候选数据包，比如我们要找的数据包是微信朋友列表，肯定不会小于1kb,然后点开第一个XHR数据包，看**响应**标签里的数据：

![](/picture/wechat-friendlist2.png)

点开其中一个：

![](/picture/wechat-friendlist3.png)

我们要的朋友列表就是它了！

找到真正的url之后，接下来就是获取请求信息了，切换到Headers版块，Header版块下的3个子信息我们都需要关注，它们是我们构造爬虫请求头的关键。

![](/picture/wechat-friendlist4.png)

找到相应的信息之后，我们便可以直接利用httr包在R中构建爬虫请求：

```
headers <- c('Accept'='application/json',
             'Content-Type'='text/plain',          
             'User-Agent'='Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.  36 (KHTML, like Gecko) Chrome/49.0.2623.221 Safari/537.36 SE 2.X Met aSr 1.0',          
             'Referer'='https://wx.qq.com/', 
             'Connection'='keep-alive',
             'cookie'=Cookie
)
```

二次请求实际的url：

```
url <- "https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxgetcontact?r=1507597918348&seq=0&skey=@crypt_ee7cd3e3_70091604da65a07600cfdca47b81cfaf"
```

GET方法单次执行请求：

```
wechat_friends <- GET(url,add_headers(.headers =headers))
```

<h4 name="netease-study">POST请求抓取网易云课堂数据</h4>

虽说动态网站数据请求也有GET方法的，但POST方法才是动态网页的主要的请求方式。

跟GET请求方法一样，切换到Header版块后继续关注General等3个子信息，但POST请求下我们需要注意的一点是：POST请求下没有像GET请求一样的Query String Parameters，而是由Request Payload来构造请求头表单参数，这一点和GET方法大不相同。总而言之，在动态网页的HTTP请求中，如果是GET请求，请求头表单参数以name=value&name1=value1的形式直接附在url后面，如果是POST请求，请求头表单参数以相同的形式放在构造的表单体中。

网易云课堂的XHR数据包不好找，通过尝试和preview，可以发现课程信息都被封装在一个studycourse.json的文件中：

<table>
<tr>
	<td><img src=/picture/netease-study1.png border=0></td>
	<td><img src=/picture/netease-study2.png border=0></td>
</tr>
</table>

所以对于网易云课堂的数据请求在R中构造如下：

```
#构造请求头
#这里小编没有登录账号，cookie就不要了
headers <- c('Accept'='application/json',
             'Content-Type'='application/json',          
             'User-Agent'='ozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.221 Safari/537.36 SE 2.X MetaSr 1.0',          
             'edu-script-token'= '37aa682d1473455c8a77e6a4476e8f9e',
             'Referer'='http://study.163.com/courses', 
             'Connection'='keep-alive'
)
#POST请求需要构造请求头表单参数
payload<-list(
  'pageIndex'=1,
  'pageSize'=50, 
  'relativeOffset'=0,
  'frontCategoryId'=-1
)
```

二次请求实际的url：

```
url <- "http://study.163.com/p/search/studycourse.json"
````

POST方法单次执行请求：

```
netease <- POST(url,add_headers(.headers =headers),body =payload, encode="json")
```
