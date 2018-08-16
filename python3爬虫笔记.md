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





参考资料：

(1) 崔庆才 《Python3网络爬虫开发实战》
