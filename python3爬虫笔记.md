<a name="content">目录</a>

[python3爬虫笔记](#title)
- [1. 搭建爬虫环境](#setup-webcrawler)
	- [1.1. 树莓派中安装Anaconda/Miniconda](#install-conda)
	- [1.2. 安装请求库](#install-requestlib)
		- [1.2.1. 安装requests、selenium：pip](#pip-requests-selenium)
		- [1.2.2. 安装浏览器驱动](#browser-driver)
		- [1.2.3. 异步web服务：aiohttp](#install-aiohttp)
	- [1.3. 安装解析库](#install-resolver-library)
	- [1.4. 安装爬虫框架](#install-webspider-framework)
		- [1.4.1. pyspider](#install-webspider-framework-pyspider)
		- [1.4.2. Scrapy](#install-webspider-framework-scrapy)
		- [1.4.3. Scrapy-Splash](#install-webspider-framework-scrapy-splash)
- [2. 爬虫基本库的使用](#usage-of-basic-library)
	- [2.1. urllib](#usage-urllib) 
		- [2.1.1. 发送请求](#usage-urllib-send-request)
		- [2.1.2. 处理异常](#usage-urllib-solve-error)
		- [2.1.3. 解析链接](#usage-urllib-parse-url)
		- [2.1.4. 分析Robots协议](#usage-urllib-parse-robots-protocol)
	- [2.2. requests](#usage-requests)
		- [2.2.1. 基本用法](#usage-requests-basic)
		- [2.2.2. 高级用法](#usage-requests-advanced)
			- [2.2.2.1. 文件上传](#usage-requests-advanced-file-upload)
			- [2.2.2.2. Cookies](#usage-requests-advanced-cookies)
			- [2.2.2.3. 会话维持](#usage-requests-advanced-keep-session)
			- [2.2.2.4. SSL证书验证](#usage-requests-advanced-verify-ssl)
			- [2.2.2.5. 代理设置](#usage-requests-advanced-set-proxy)
			- [2.2.2.6. 身份认证](#usage-requests-advanced-auth)
			- [2.2.2.7. Prepared Request](#usage-requests-advanced-prepared-request)
	- [2.3. 正则表达式](#regex)




<h1 name="title">python3爬虫笔记</h1>

<a name="setup-webcrawler"><h2>1. 搭建爬虫环境 [<sup>目录</sup>](#content)</h2></a>

如果是在Linux操作系统下，建议建立一个私人的python开发环境

可以virtalenv也可以用anaconda/miniconda

注意：由于树莓派系统是基于ARM架构，在一些软件/安装包的安装上可能会出现一些问题

<a name="install-conda"><h3>1.1. 树莓派中安装Anaconda/Miniconda [<sup>目录</sup>](#content)</h3></a>

树莓派的CPU是基于ARM构架，不同于常用的Intel的X86构架，因此在Anaconda/Miniconda官网上直接下载的安装文件，在树莓派上安装会报错，此时的解决方案是获取专门为ARM编写的安装文件：

```
wget http://repo.continuum.io/miniconda/Miniconda3-latest-Linux-armv7l.sh
bash Miniconda3-latest-Linux-armv7l.sh
```

<a name="install-requestlib"><h3>1.2. 安装请求库 [<sup>目录</sup>](#content)</h3></a>

<a name="pip-requests-selenium"><h4>1.2.1. 安装requests、selenium：pip [<sup>目录</sup>](#content)</h4></a>

用pip安装python包，若安装失败，可以尝试升级pip

> 升级pip的方法：
> 
> ```
> $ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
> 
> $ sudo python get-pip.py
> ```


安装requests

```
$ pip3 install requests
```

安装Selenium

```
$ pip3 install selenium
```

<a name="browser-driver"><h4>1.2.2. 安装浏览器驱动 [<sup>目录</sup>](#content)</h4></a>

然后要安装浏览器驱动，Chrome用ChromeDriver，Firefox用GeckoDriver

- **ChromeDriver**

先确定当前使用的Chrome浏览器的版本，然后到google官网上下载对应版本的ChromeDriver，并将其文件路径添加到环境变量中

> 在Windows中添加环境变量的方法：
> 
> 计算机 => 属性 => 高级系统设置 => 环境变量 => 系统变量中选择"Path" => 点击下方的编辑 => 在Path环境变量后继续追加路径，**用分号分隔**

- **GeckoDriver**

Github地址： `https://github.com/mozilla/geckodriver`

在README主页上寻找release链接，打开后找到适合当前Firefox浏览器版本的文件下载下来

然后与ChromeDriver的操作类似，并将其文件路径添加到环境变量中

测试 ChromeDriver / GeckoDriver

先启动浏览器驱动：

<p align="center"><img src=./picture/Python-webcrawler-ChromeDriver.png width=600 /></p>

<p align="center"><img src=./picture/Python-webcrawler-GeckoDriver.png width=600 /></p>

然后在python交互环境中，通过命令尝试打开一个空白浏览器界面

```
from selenium import webdriver

# 通过ChromeDriver打开Chrome浏览器
browser = webdriver.Chrome()

# 通过GeckoDriver打开Firefox浏览器
browser = webdriver.Firefox()
```

使用ChromeDriver和GeckoDriver我们就可以利用浏览器进行网页的抓取，但是这样可能有个不方便的地方：程序运行过程中需要一直开着浏览器，在爬取网页的过程中浏览器可能一直动来动去。所以这个时候还有另外一种选择——安装一个无界面浏览器：PantomJS

- **PantomJS**

PhantomJS是一个无界面的、可脚本编辑的WebKit浏览器引擎

Selenium支持PhantomJS，运行的时候不会再弹出一个浏览器界面了

PhantomJS官网：`http://phantomjs.org/`，Windows下安装过程如上

Linux下基于源码的安装方法：

```
# 获取source code
git clone git://github.com/ariya/phantomjs.git
cd phantomjs
git checkout 2.1.1
git submodule init
git submodule update

# 编译源码，这一步可能很耗时，请耐心等待
python build.py
```

也可以基于apt进行一键式安装（推荐）：

```
$ sudo apt-get install phantomjs
```

测试PantomJS

```
from selenium import webdriver
browser = webdriver.PhantomJS()
browser.get('https://www.baidu.com')
print(browser.current_url)
```

<a name="install-aiohttp"><h4>1.2.3. 异步web服务：aiohttp [<sup>目录</sup>](#content)</h4></a>

之前提到的requests是一个阻塞式HTTP请求库，当发送一个请求后程序会一直等待服务器响应，知道得到响应才会进行下一步处理，这样很耗费时间

aiohttp则是一个提供异步web服务的库

```
pip install aiohttp
```

另外，官方还推荐安装如下两个库：

> - cchardet 字符编码检测库
> - aiodns 加速DNS解析库

```
pip install cchardet aiodns
```

<a name="install-resolver-library"><h3>1.3. 安装解析库 [<sup>目录</sup>](#content)</h3></a>

抓取网页后要做的是从网页中提取信息。提取信息的方式多种多样，可以使用正则表达式来提取，但是写起来比较繁琐。可以使用许多强大的解析库，此外还提供了强大的解析方法，如XPath解析和CSS选择器解析

- **lxml**

lxml支持HTML和XML的解析，支持XPath解析方式

```
pip install lxml
```

- **Beautiful Soup**

lxml支持HTML和XML的解析，其依赖于lxml

```
pip install beautifulsoup4
```

验证：

```
from bs4 import BeautifulSoup
soup = BeautifulSoup('<p>Hello</p>','lxml')
print(soup.p.string)
```

注意：虽然安装的是beautifulsoup4这个包，但是在引入的时候确实bs4，这是因为这个包源代码本身的库文件夹名称就是bs4

- **pyquery**

pyquery提供了和jQuery类似的语法来解析HTML，支持CSS选择器

```
pip install pyquery
```

- **tesserocr / pytesseract**

在爬虫过程中难免会遇到各种各样的验证码，而大多数的验证码还是图像验证码，这时我们可以使用OCR来是识别

> OCR，即 Optical Character Recognition，光学字符识别，是指通过扫描字符，通过其形状将其翻译成电子文本的过程。
> 
> 对应网页中的验证码，我们可以使用OCR技术将其转化为电子文本，然后爬虫将识别结果提交给服务器，从而达到自动识别验证码的过程

tesserocr使Python的OCR识别库，但其实是对tesseract的一层Python API封装，它的核心是tesseract。因此在安装tesserocr之前，需要先安装tesseract

1、安装tesseract

> - windows
> 
> 下载地址：`https://digi.bib.uni-mannheim.de/tesseract/`
> 
> 注意：安装的时候要勾选Additional language data(download)，来安装OCR识别支持的语言包，这样OCR就可以识别多国语言
> 
> - Linux
> 
> 在linux下安装tesseract
> 
> ```
> # 用apt安装
> $ sudo apt-get install tesseract-ocr libtesseract-dev libleptonica-dev
> 
> # 用conda安装
> $ conda install -c bioconda tesseract
> 
> # 从源码安装
> wget https://github.com/tesseract-ocr/tesseract/archive/3.05.01.tar.gz
> tar -zxvf 3.05.01.tar.gz
> cd tesseract-3.05.01
> ./autogen.sh
> PKG_CONFIG_PATH=/usr/local/lib/pkgconfig LIBLEPT_HEADERSDIR=/usr/local/include ./configure --with-extra-includes=/usr/local/include --with-extra-libraries=/usr/local/lib
> LDFLAGS="-L/usr/local/lib" CFLAGS="-I/usr/local/include" make
> make install
> ```
> 
> 添加语言库
> 
> ```
> git clone https://github.com/tesseract-ocr/tessdata.git
> cp tessdata/* /usr/share/tesseract-ocr/
> ```

测试tesseract

<p align="center"><img src=./picture/Python-webcrawler-tesseract-example-image-english.png width=300 /></p>

<p align="center"><img src=./picture/Python-webcrawler-tesseract-example-image-chinese.png width=300 /></p>

```
$ tesseract image-english.png result -l eng
$ tesseract image-chinese.png result -l chi-sim
```

2、安装tesserocr / pytesseract

```
# 安装tesserocr
pip install tesserocr pillow

安装pytesseract
pip install pytesseract
```

若安装失败，可以尝试使用conda

```
> conda install -c simonflueckiger tesserocr
```

测试tesserocr

```
import tesserocr
from PIL import Image
image = Image.open('image.png')
print(tesserocr.image_to_text(image))
```

<a name="install-webspider-framework"><h3>1.4. 安装爬虫框架 [<sup>目录</sup>](#content)</h3></a>

<a name="install-webspider-framework-pyspider"><h4>1.4.1. pyspider [<sup>目录</sup>](#content)</h4></a>

pyspider是国人binux编写的强大的网络爬虫框架

pyspider支持JavaScript渲染，而这个过程依赖于PhantomJS，所以需要同时安装好PhantomJS

```
pip install pyspider
```

测试，直接在命令行下执行：`spider all`，出现如下输出：

<p align="center"><img src=./picture/Python-webcrawler-spiderframework-pyspider.png width=700 /></p>

这时pyspider的Web服务就会在本地的5000端口运行，直接在浏览器中打开`http://localhost:5000`即可进入pyspider的WebUI管理界面

<p align="center"><img src=./picture/Python-webcrawler-spiderframework-pyspider-WebUI.png width=700 /></p>

<a name="install-webspider-framework-scrapy"><h4>1.4.2. Scrapy [<sup>目录</sup>](#content)</h4></a>

Scrapy是一个十分强大的爬虫框架，依赖的库比较多，至少包括Twisted、lxml和pyOpenSSL，此时使用conda进行安装最为方便

```
$ conda install Scrapy
```

测试：在命令行输入`scrapy`，若输出帮助文档则说明安装成功

<a name="usage-of-basic-library"><h2>2. 爬虫基本库的使用 [<sup>目录</sup>](#content)</h2></a>

<a name="usage-urllib"><h3>2.1. urllib [<sup>目录</sup>](#content)</h3></a>

urllib包含以下3个主要模块：

> - request 最基本的HTTP请求模块，可以用来模拟发送请求
> - error 异常处理模块，如果出现请求错误，可以捕获这些异常，然后进行重试或者其他操作以保证程序不会意外终止
> - parse 一个工具模块，提供了许多URL处理方法，比如拆分解析、合并等

<a name="usage-urllib-send-request"><h4>2.1.1. 发送请求 [<sup>目录</sup>](#content)</h4></a>

**1、urlopen**

- **基本用法**

	利用最基本的urlopen( )方法，可以完成最基本的简单网页的GET请求抓取
	
	```
	import urllib.request
	
	response = urllib.request.urlopen('https://www.python.org')
	print(response.read().decode('utf-8'))
	```
	
	查看响应类型：
	
	```
	type(response)
	
	<class 'http.client.HTTPResponse'>
	```
	
	一些方法的使用：
	
	```
	>>> print(response.status)
	200
	
	>>> print(response.getheaders())
	[('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), ('X-Frame-Options', 'SAMEORIGIN'), ('x-xss-protection', '1; mode=block'), ('X-Clacks-Overhead', 'GNU Terry Pratchett'), ('Via', '1.1 varnish'), ('Content-Length', '48809'), ('Accept-Ranges', 'bytes'), ('Date', 'Fri, 17 Aug 2018 06:31:57 GMT'), ('Via', '1.1 varnish'), ('Age', '3242'), ('Connection', 'close'), ('X-Served-By', 'cache-iad2145-IAD, cache-hkg17926-HKG'), ('X-Cache', 'HIT, HIT'), ('X-Cache-Hits', '9, 10'), ('X-Timer', 'S1534487518.955612,VS0,VE0'), ('Vary', 'Cookie'), ('Strict-Transport-Security', 'max-age=63072000; includeSubDomains')]
	
	>>> print(response.getheader('Server'))
	nginx
	```

- **传递参数**

	1. data参数：添加额外数据
	
		```
		import urllib.parse
		import urllib.request
		
		data = bytes(urllib.parse.urlencode({'word':'hello'}),encoding='utf-8')
		response = urllib.request.urlopen('http://httpbin.org/post',data=data)
		print(response.read())
		```

	2. timeout参数：设置超时时间，单位为秒
	
		```
		response = urllib.request.urlopen('http://httpbin.org/get',timeout=0.1) # 按常理，0.1秒是基本不可能得到服务器响应的
		```

		出现以下报错信息：

		```
		Traceback (most recent call last):
		  File "<stdin>", line 1, in <module>
		  File "/home/pi/miniconda3/lib/python3.4/urllib/request.py", line 161, in urlopen
		    return opener.open(url, data, timeout)
		  ...
		urllib.error.URLError: <urlopen error timed out>
		```

		其抛出的是URLError异常

		可以利用try excep语句来实现，若长时间没有响应就跳过它的抓取

		```
		import socket
		import urllib.request
		import urllib.error
		
		try:
			response = urllib.request.urlopen('http://httpbin.org/get',timeout=0.1)
		except urllib.error.URLError as e:
			if isinstance(e.reason,socket.timeout):
				print('TIME OUT')
		```

**2、Request**

利用urlopen( ) 方法可以实现最基本的请求发起，但是这几个简单的参数还不足够构建一个完整的请求，如果想在请求中加入Header等信息，可以利用Request：

```
import urllib.request

request = urllib.request.Request('https://python.org')
response = urllib.request.urlopen(request)
```

依然使用的是urlopen( ) 来发生请求，只不过这次该方法的参数不再是URL，而是一个Request类型对象

构造Request对象：

```
urllib.request.Request(url,data=None,headers={},origin_req_host=None,method=None)
```

**3、高级用法**

处理高级操作：Cookies处理，代理设置等等

需要使用到Handler工具，它是urllib.request模块里的BaseHandler类，它是所有Handler的父类
其中一个重要的Handler类是OpenerDirector类，可以称之为Opener

1. **验证**

	有些网站在打开的时候会弹出提示框，直接提示你输入用户名和密码，验证成功后才能查看页面
	
	如果要请求这样的页面，需要借助 HTTPBasicAuthHandler
	
	```
	from urllib.request import HTTPPasswordMgrWithDefaultRealm,HTTPBasicAuthHandler,build_opener
	from urllib.error import URLError
	
	username = 'username'
	password = 'password'
	url = 'https://...'

	p = HTTPPasswordMgrWithDefaultRealm()
	p.add_password(None,url,username,password)
	auth_handler = HTTPBasicAuthHandler(p)
	opener = build_opener(auth_handler)

	try:
		result = opener.open(url)
		html = result.read().decode('utf-8')
		print(html)
	except URLError as e:
		print(e.reason)
	```

2. **代理**

	```
	from urllib.error import URLError
	from urllib.request import ProxyHandler,build_opener
	
	proxy_handler=ProxyHandler({'http':'http://localhost:9743','https':'https://localhost:9743'})
	opener=build_opener(proxy_handler)
	try:
	        response=opener.open('https://www.baidu.com')
	        print(response.read().decode('utf-8'))
	except URLError as e:
	        print(e.reason)
	```

3. **Cookies**

	```
	import http.cookiejar, urllib.request
	
	cookie = http.cookiejar.CookieJar()
	handler = urllib.request.HTTPCookieProcessor(cookie)
	opener = urllib.request.build_opener(handler)
	response = opener.open('http://www.baidu.com')
	for item in cookie:
	    print(item.name+"="+item.value)
	```

<a name="usage-urllib-solve-error"><h4>2.1.2. 处理异常 [<sup>目录</sup>](#content)</h4></a>

在发送请求后可能出现异常，如果不处理这些异常，程序很可能因报错而终止，所以异常处理十分必要

urllib的error模块定义了由request模块产生的异常，如果出现了问题，request模块便会抛出error模块中定义的异常：

> URLError和HTTPError类，其中HTTPError类URLError类的子类

URLError

```
from urllib import request, error
try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.URLError as e:
    print(e.reason)
```

HTTPError

```
from urllib import request,error
try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers, sep='\n')
```

通常会先去捕获子类的错误，再去捕获父类的错误

```
from urllib import request, error

try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers, sep='\n')
except error.URLError as e:
    print(e.reason)
else:
    print('Request Successfully')
```

<a name="usage-urllib-parse-url"><h4>2.1.3. 解析链接 [<sup>目录</sup>](#content)</h4></a>

1、 urlparse

该方法可以实现URL的识别和分段：

```
from urllib.parse import urlparse

result = urlparse('http://www.baidu.com/index.html;user?id=5#comment')
print(type(result), result)


<class 'urllib.parse.ParseResult'> ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='user', query='id=5', fragment='comment')
```

2、 urlunparse

它是作业与urlparse相反，其接受的参数**长度必须为6**

```
from urllib.parse import urlunparse

data = ['http', 'www.baidu.com', 'index.html', 'user', 'a=6', 'comment']
print(urlunparse(data))


http://www.baidu.com/index.html;user?a=6#comment
```

3、urlsplit

与urlparse相似，不过它不再单独解析parms这一部分，只返回5个结果

```
from urllib.parse import urlsplit

result = urlsplit('http://www.baidu.com/index.html;user?id=5#comment')
print(result)


SplitResult(scheme='http', netloc='www.baidu.com', path='/index.html;user', query='id=5', fragment='comment')
```

4、urlunsplit

与urlunparse相似，唯一区别是长度必须是5

```
from urllib.parse import urlunsplit

data = ['http', 'www.baidu.com', 'index.html', 'a=6', 'comment']
print(urlunsplit(data))
```

5、urljoin

用法：`urljoin(url_1,url_2)`

提供一个base_url作为第一个参数，将新的链接作为第二个参数，该方法会解析base_url的scheme、netloc和path这3个内容并对新链接缺失的部分进行补充

```
from urllib.parse import urljoin

print(urljoin('http://www.baidu.com', 'FAQ.html'))
print(urljoin('http://www.baidu.com', 'https://cuiqingcai.com/FAQ.html'))
print(urljoin('http://www.baidu.com/about.html', 'https://cuiqingcai.com/FAQ.html'))
print(urljoin('http://www.baidu.com/about.html', 'https://cuiqingcai.com/FAQ.html?question=2'))
print(urljoin('http://www.baidu.com?wd=abc', 'https://cuiqingcai.com/index.php'))
print(urljoin('http://www.baidu.com', '?category=2#comment'))
print(urljoin('www.baidu.com', '?category=2#comment'))
print(urljoin('www.baidu.com#comment', '?category=2'))
```

输出

```
http://www.baidu.com/FAQ.html
https://cuiqingcai.com/FAQ.html
https://cuiqingcai.com/FAQ.html
https://cuiqingcai.com/FAQ.html?question=2
https://cuiqingcai.com/index.php
http://www.baidu.com?category=2#comment
www.baidu.com?category=2#comment
www.baidu.com?category=2
```

6、urlencode

它在构造GET请求参数的时候非常有用

```
from urllib.parse import urlencode

params = {
    'name': 'germey',
    'age': 22
}
base_url = 'http://www.baidu.com?'
url = base_url + urlencode(params)
print(url)


http://www.baidu.com?age=22&name=germey
```

urlencode( )方法可以将字典形式的变量序列化，转化为GET请求的参数

7、parse_qs

有了序列化，自然就有反序列化

```
from urllib.parse import parse_qs

query = 'name=germey&age=22'
print(parse_qs(query))


{'age': ['22'], 'name': ['germey']}
```

8、quote

URL中带有中文参数时，可能会导致乱码的问题，此时需要将中文字符转化为URL编码

```
from urllib.parse import quote

keyword = '壁纸'
url = 'https://www.baidu.com/s?wd=' + quote(keyword)
print(url)


https://www.baidu.com/s?wd=%E5%A3%81%E7%BA%B8
```

<a name="usage-urllib-parse-robots-protocol"><h4>2.1.4. 分析Robots协议 [<sup>目录</sup>](#content)</h4></a>

每个网站的Robots协议一般保存在站点的根目录下的`robots.txt`文件中

例如：

```
User-agent: Baiduspider
Disallow: /baidu
Disallow: /s?
Disallow: /ulink?
Disallow: /link?
Disallow: /home/news/data/
```

可以使用robotparser模块解析robots.txt

```
from urllib.robotparser import RobotFileParser

rp = RobotFileParser()
rp.set_url('http://www.jianshu.com/robots.txt')
rp.read()
print(rp.can_fetch('*', 'http://www.jianshu.com/p/b67554025d7d'))
print(rp.can_fetch('*', "http://www.jianshu.com/search?q=python&page=1&type=collections"))
```

<a name="usage-requests"><h3>2.2. requests [<sup>目录</sup>](#content)</h3></a>

urllib在使用过程中有一些不方便的地方，比如处理网页页面验证和Cookies时，需要写Opener和Handler来处理

requests可以更加方便的实现这些操作

<a name="usage-requests-basic"><h4>2.2.1. 基本用法 [<sup>目录</sup>](#content)</h4></a>

```
import requests

# GET方法
r= requests.get(url,headers=headers,params=data)	# 以字典形式传入headers和data

# POST方法
r= requests.post(url,headers=headers,data=data)
```

有时需要构造header，否则无法正常请求

```
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36'
}
```

- 抓取网页，然后用正则表达式进行匹配

```
import requests
import re

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36'
}
r = requests.get("https://www.zhihu.com/explore", headers=headers)
pattern = re.compile('explore-feed.*?question_link.*?>(.*?)</a>', re.S)
titles = re.findall(pattern, r.text)
print(titles)
```

- 抓取二进制数据


```
import requests

r = requests.get("https://github.com/favicon.ico")
with open('favicon.ico', 'wb') as f:
    f.write(r.content)
```

以字符串形式直接打印会乱码： `print(r.text)`

以二进制形式打印：

```
print(r.content)

b'\x00\x00\x01\x00\x02\x00\x10\x10\x00\x00\x01\x00 ...'
```

- 响应

requests提供了一个内置的状态码查询对象requests.codess，可以用来判断请求是否成功

```
import requests

r = requests.get('http://www.jianshu.com')
exit() if not r.status_code == requests.codes.ok else print('Request Successfully')
```

<a name="usage-requests-advanced"><h4>2.2.2. 高级用法 [<sup>目录</sup>](#content)</h4></a>

<a name="usage-requests-advanced-file-upload"><h4>2.2.2.1. 文件上传 [<sup>目录</sup>](#content)</h4></a>

```
import requests

files = {'file': open('favicon.ico', 'rb')}
r = requests.post('http://httpbin.org/post', files=files)
print(r.text)
```

<a name="usage-requests-advanced-cookies"><h4>2.2.2.2. Cookies [<sup>目录</sup>](#content)</h4></a>

获取Cookies

```
import requests

r = requests.get('https://www.baidu.com')
print(r.cookies)
for key, value in r.cookies.items():
    print(key + '=' + value)
```

可以将Cookies在发送请求时加入headers中维持登录状态

```
import requests

headers = {
    'Cookie': 'q_c1=31653b264a074fc9a57816d1ea93ed8b|1474273938000|1474273938000; d_c0="AGDAs254kAqPTr6NW1U3XTLFzKhMPQ6H_nc=|1474273938"; __utmv=51854390.100-1|2=registration_date=20130902=1^3=entry_date=20130902=1;a_t="2.0AACAfbwdAAAXAAAAso0QWAAAgH28HQAAAGDAs254kAoXAAAAYQJVTQ4FCVgA360us8BAklzLYNEHUd6kmHtRQX5a6hiZxKCynnycerLQ3gIkoJLOCQ==";z_c0=Mi4wQUFDQWZid2RBQUFBWU1DemJuaVFDaGNBQUFCaEFsVk5EZ1VKV0FEZnJTNnp3RUNTWE10ZzBRZFIzcVNZZTFGQmZn|1474887858|64b4d4234a21de774c42c837fe0b672fdb5763b0',
    'Host': 'www.zhihu.com',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36',
}
r = requests.get('https://www.zhihu.com', headers=headers)
print(r.text)
```

也可以通过cookies参数来设置，不过这需要构造一个RequestsCookieJar对象

```
import requests

cookies = 'q_c1=31653b264a074fc9a57816d1ea93ed8b|1474273938000|1474273938000; d_c0="AGDAs254kAqPTr6NW1U3XTLFzKhMPQ6H_nc=|1474273938"; __utmv=51854390.100-1|2=registration_date=20130902=1^3=entry_date=20130902=1;a_t="2.0AACAfbwdAAAXAAAAso0QWAAAgH28HQAAAGDAs254kAoXAAAAYQJVTQ4FCVgA360us8BAklzLYNEHUd6kmHtRQX5a6hiZxKCynnycerLQ3gIkoJLOCQ==";z_c0=Mi4wQUFDQWZid2RBQUFBWU1DemJuaVFDaGNBQUFCaEFsVk5EZ1VKV0FEZnJTNnp3RUNTWE10ZzBRZFIzcVNZZTFGQmZn|1474887858|64b4d4234a21de774c42c837fe0b672fdb5763b0'
jar = requests.cookies.RequestsCookieJar()
headers = {
    'Host': 'www.zhihu.com',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36'
}
for cookie in cookies.split(';'):
    key, value = cookie.split('=', 1)
    jar.set(key, value)
r = requests.get('http://www.zhihu.com', cookies=jar, headers=headers)
print(r.text)
```

<a name="usage-requests-advanced-keep-session"><h4>2.2.2.3. 会话维持 [<sup>目录</sup>](#content)</h4></a>

在requests中，如果直接用get( ) 或post( )方法的确可以做到模拟网页的请求，但实际上相当于不同的会话

> 设想一个这样的场景：
> 
> 第一个请求用post( ) 方法登录了某个网站，第二次想获取成功登录后的个人信息，你又用了一次get( ) 方法去请求个人信息的页面。实际上这相当于打开了第二个浏览器，是完全不同的会话，肯定不能获得你想要的个人信息

```
import requests

requests.get('http://httpbin.org/cookies/set/number/123456789')	# 请求测试网站，并设置一个cookie，名称为number，值为123456789
r = requests.get('http://httpbin.org/cookies')	# 请求http://httpbin.org/cookies网站，获取当前的cookie
print(r.text)


{
  "cookies": {}
}
```

用session试试：

```
import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/number/123456789')
r = s.get('http://httpbin.org/cookies')
print(r.text)


{
  "cookies": {
    "number": "123456789"
  }
}
```

<a name="usage-requests-advanced-verify-ssl"><h4>2.2.2.4. SSL证书验证 [<sup>目录</sup>](#content)</h4></a>

当发送HTTP请求的时候，它会检查SSL证书，我们可以使用verify参数控制是否检查次证书。默认会自动进行证书检查

测试12306网站的SSL证书

```
import requests

response = requests.get('https://www.12306.cn')	# 认证失败报错
print(response.status_code)
```

关闭自动认证：

```
response = requests.get('https://www.12306.cn', verify=False)	# 虽然关闭了认证，但任然会给出一条警告信息
print(response.status_code)

200
```

屏蔽警告信息的方法：

1、

```
import requests
from requests.packages import urllib3

urllib3.disable_warnings()
response = requests.get('https://www.12306.cn', verify=False)
print(response.status_code)
```

2、

```
import logging
import requests
logging.captureWarnings(True)
response = requests.get('https://www.12306.cn', verify=False)
print(response.status_code)
```

我们也可以指定本地的认证证书

```
import requests

response = requests.get('https://www.12306.cn', cert=('/path/server.crt', '/path/key'))
print(response.status_code)
```

<a name="usage-requests-advanced-set-proxy"><h4>2.2.2.5. 代理设置 [<sup>目录</sup>](#content)</h4></a>

```
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',	# 请换成自己的有效代理
  'https': 'http://10.10.1.10:1080',
}

requests.get('https://www.taobao.com', proxies=proxies)
```

若代理需要使用HTTP Basic Auth，可以使用类似 `http://user:password@host:port` 这样的语法来设置代理

```
import requests

proxies = {
    'https': 'http://user:password@10.10.1.10:3128/',
}
requests.get('https://www.taobao.com', proxies=proxies)
```

<a name="usage-requests-advanced-auth"><h4>2.2.2.6. 身份认证 [<sup>目录</sup>](#content)</h4></a>

在访问网站时，可能会遇到认证界面，此时可以使用requests自带的身份认证功能

```
import requests
from requests.auth import HTTPBasicAuth

r = requests.get('http://localhost:5000', auth=HTTPBasicAuth('username', 'password'))
print(r.status_code)
```

更简单的写法（不需要传入一个HTTPBasicAuth类）：

```
import requests

r = requests.get('http://localhost:5000', auth=('username', 'password'))
print(r.status_code)
```

<a name="usage-requests-advanced-prepared-request"><h4>2.2.2.7. Prepared Request [<sup>目录</sup>](#content)</h4></a>

如同urllib中提到的，我们可以将请求表示为数据结构，这个数据结构就叫 **Prepared Request**

```
from requests import Request, Session

url = 'http://httpbin.org/post'
data = {
    'name': 'germey'
}
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36'
}
s = Session()
req = Request('POST', url, data=data, headers=headers)
prepped = s.prepare_request(req)
r = s.send(prepped)
print(r.text)
```

<a name="regex"><h3>2.3. 正则表达式 [<sup>目录</sup>](#content)</h3></a>

Python的re库提供了整个正则表达式的实现

**1、 match( )**

match( )方法会尝试**从字符串的起始位置匹配正则表达式**

如果匹配就返回匹配成功的结果；如果不匹配，就返回None

```
match(regex, str)
```

匹配得到的结果是一个SRE_Match对象，该对象有两个方法：

> - group( )：输出匹配到的内容
> 
> - span( )：输出匹配的范围

**2、匹配目标**

如果想从字符串中提取一部分内容，可以使用括号( )将想提取的子字符串括起来

```
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^Hello\s(\d+)\sWorld', content)
print(result)
print(result.group())	# 输出完整的匹配结果
print(result.group(1))	# 输出第一个被括号包围的匹配结果
print(result.span())


<_sre.SRE_Match object; span=(0, 19), match='Hello 1234567 World'>
Hello 1234567 World
1234567
(0, 19)
```
**3、贪婪与非贪婪**

> - `.*` 贪婪匹配
> 
> - `.*?` 非贪婪匹配

贪婪匹配

```
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*(\d+).*Demo$', content)
print(result)
print(result.group(1))


<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
7
```

非贪婪匹配

```
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*?(\d+).*Demo$', content)
print(result)
print(result.group(1))


<_sre.SRE_Match object; span=(0, 40), match='Hello 1234567 World_This is a Regex Demo'>
1234567
```

**4、修饰符**

`.` 匹配的是除换行符之外的任意字符，当遇到换行符时，`.*?`就不能匹配了，所以导致匹配失败

```
import re

content = '''Hello 1234567 World_This
is a Regex Demo
'''
result = re.match('^He.*?(\d+).*?Demo$', content)
print(result.group(1))


Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'NoneType' object has no attribute 'group'
```

这时，只需要添加一个修饰符 re.S

```
result = re.match('^He.*?(\d+).*?Demo$', content,re.S)
```

修饰符 re.S 的作用是使 `.` 匹配包括换行符在内的所有字符

这个 re.S 在网页匹配中经常使用，因为HTML节点经常会有换行，加上它，就可以匹配节点与节点之间的换行了

其他常用的修饰符：

| 修饰符 | 描述 |
|:---:|:---:|
| re.I | 使匹配对大小写不敏感 |
| re.S | 使 `.` 匹配包括换行符在内的所有字符 |

**5、search**

match( )方法是尝试**从字符串的起始位置匹配正则表达式**，一旦开头不匹配，那么整个匹配就失败了

```
mport re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
result = re.match('Hello.*?(\d+).*?Demo', content)
print(result)


None
```

另外一个方法search( )，它在匹配时会扫描整个字符串，然后返回第一个成功匹配的结果。因此未来匹配方便，我们可以**尽量使用search( )方法**

实例：

> 提取class为active的li节点内部超链接包含的歌手名和歌名
> 
> ```
> import re
> 
> html = '''<div id="songs-list">
>     <h2 class="title">经典老歌</h2>
>     <p class="introduction">
>         经典老歌列表
>     </p>
>     <ul id="list" class="list-group">
>         <li data-view="2">一路上有你</li>
>         <li data-view="7">
>             <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
>         </li>
>         <li data-view="4" class="active">               # 提取范围
>             <a href="/3.mp3" singer="齐秦">往事随风</a>   # 提取范围
>         </li>
>         <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
>         <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
>         <li data-view="5">
>             <a href="/6.mp3" singer="邓丽君"><i class="fa fa-user"></i>但愿人长久</a>
>         </li>
>     </ul>
> </div>'''
> 
> results = re.findall('<li.*?active.*?singer="(.*?)">(.*?)</a>', html, re.S)
> if result:
>	print(result.group(1),result.group(2)
> ```
> 
> 输出：
> 
> ```
> 齐秦往事随风
> ```

**6、findall**

findall( )方法会搜索整个字符串，然后返回匹配正则表达式的所有内容

还是上面的HTML文本，获取所有的a节点的超链接、歌手和歌名

```
results = re.findall('<li.*?href="(.*?)".*?singer="(.*?)">(.*?)</a>', html, re.S)
print(results)
print(type(results))
for result in results:
    print(result)
    print(result[0], result[1], result[2])
```

**7、sub**

使用正则表达式修改文本。可以使用字符串的replace( )方法，但是这样做太繁琐了，这时可以使用sub( )方法

实例：将所有数字去掉

```
import re

content = '54aK54yr5oiR54ix5L2g'
content = re.sub('\d+', '', content)
print(content)


aKyroiRixLg
```

上面的HTML文本中，可以先用sub( )方法将a节点去掉，只留下文本，然后再利用findall( )提取

```
html = re.sub('<a.*?>|</a>', '', html)
print(html)
results = re.findall('<li.*?>(.*?)</li>', html, re.S)
for result in results:
    print(result.strip())
```

**8、compile**

compile( )方法可以将正则字符串编译成正则表达式对象，以便在后面的匹配中复用

```
import re

content1 = '2016-12-15 12:00'
content2 = '2016-12-17 12:55'
content3 = '2016-12-22 13:21'
pattern = re.compile('\d{2}:\d{2}')
result1 = re.sub(pattern, '', content1)
result2 = re.sub(pattern, '', content2)
result3 = re.sub(pattern, '', content3)
print(result1, result2, result3)
```




参考资料：

(1) 崔庆才 《Python3网络爬虫开发实战》
