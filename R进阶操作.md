<a name="content">目录</a>

[R进阶操作](#title)
- [R包安装](#install-Rpackages)
	- [设置默认的CRAN网站镜像](#setting-default-cran-mirror)
	- [从本地安装R包](#install-Rpackages-locally)






<h1 name="title">R进阶操作</h1>

<p align="center"><img src=./picture/R-advanced-Rlogo.png width="500" /></p>

<a name="install-Rpackages"><h3>R包安装 [<sup>目录</sup>](#content)</h3></a>

<a name="setting-default-cran-mirror"><h4>设置默认的CRAN网站镜像 [<sup>目录</sup>](#content)</h4></a>

在每次安装R包的过程中都会使用相同的CRAN镜像（即离本地最近的镜像地址）。你可能对于R重复地询问选择镜像感到厌烦。按照上述方法所给出的解决方案进行操作，便设定了默认的镜像，R每次就不再询问了。

```
# 1. 调用chooseCRANmirror函数
> chooseCRANmirror()

# HTTPS CRAN mirror 

# 1: 0-Cloud [https]             2: Austria [https]          
# 3: China (Beijing 4) [https]   4: China (Hefei) [https]    
# 5: Colombia (Cali) [https]     6: France (Lyon 2) [https]  
# 7: Iceland [https]             8: Russia (Moscow 1) [https]
# 9: Switzerland [https]        10: UK (Bristol) [https]     
#11: UK (Cambridge) [https]     12: USA (CA 1) [https]       
#13: USA (KS) [https]           14: USA (MI 1) [https]       
#15: USA (TN) [https]           16: USA (TX) [https]         
#17: USA (WA) [https]           18: (HTTP mirrors) 

# 2. 从列表中选择镜像并点击确定。

# 3. 通过查看repos选项的第一个元素来获取所选镜像的URL地址
> options("repos")[[1]][1]

# 4. 将下面的命令添加至.Rprofile 文件中
options(repos="URL")
```

<a name="install-Rpackages-locally"><h4>从本地安装R包 [<sup>目录</sup>](#content)</h4></a>

下载安装包，从CRNA官网中获取R包的下载链接，然后用wget进行下载

```
wget -c -P PATH URL
```

查看默认包的安装路径

```
.libPaths()
```

安装包

```
# 本地tar文件
install.packages("/PATH/Biobase_2.4.1.tar.gz") 
```

如果依赖的包都装好了，如果R版本正确，一般不会有什么问题；若有依赖的包，就先安装依赖的包。



参考资料：


(1) [设定默认CRAN网站镜像](https://book.2cto.com/201305/21966.html)

(2) [Linux 没网络，安装本地的R包](http://blog.sina.com.cn/s/blog_86c8fc120102w4a5.html)
