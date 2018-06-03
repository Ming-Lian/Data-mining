<a name="content">目录</a>

[NCBI批量检索与翻译](#title)
- [实现目标](#goal)
- [思路](#idea)
- [手动下载摘要信息](#download-abstract-by-hand)
- [格式整理](#modify-format)
	- [perl脚本](#perlscript)
	- [批量格式化](#batch-format)
- [googletrans批量翻译](#use-googletrans)
	- [googletrans安装与基本操作](#install-and-basic-usage)
	- [批量翻译](#batch-translate)



<h1 name="title">NCBI批量检索与翻译</h1>

<a name="goal"><h2>实现目标 [<sup>目录</sup>](#content)</h2></a>

有这样一个Drug-Gene Pairs的表格

|Drug| Gene|
|:---:|:---:|
|	amitriptyline	|	ABCB1	|
|	citalopram	|	ABCB1	|
|	clomipramine	|	ABCB1	|
|	fluoxetine	|	ABCB1	|
|	fluvoxamine	|	ABCB1	|
|	nefazodone	|	ABCB1	|
|	nortriptyline	|	ABCB1	|
| ... | ... |

根据Drug-Gene Pairs到NCBI的PubMed数据库里下载这个Drug-Gene Pairs的前沿研究的文献的摘要

然后用谷歌翻译将这些摘要翻译成中文

<a name="idea"><h2>思路 [<sup>目录</sup>](#content)</h2></a>

本来想通过爬虫来批量检索与下载，不过目前没有想到合适的实现方式

所以仍然采用最原始的手动方法，好在Drug-Gene Pairs的数量不多，总共400多条

用perl脚本整理这些下载下来的摘要，整理成比较工整的格式

然后用谷歌翻译API googletrans进行批量翻译

最终结果要整合成以下格式：

| | | | | |
|:---|:---|:---|:---|:---|
|Drug|Gene|doi|abstract|translation|

<a name="download-abstract-by-hand"><h2>手动下载摘要信息 [<sup>目录</sup>](#content)</h2></a>

设置检索条件

<p align="center"><img src=./picture/Batch-download-and-translate-from-NCBI-1.png width=800/></p>

下载目标数据

<p align="center"><img src=./picture/Batch-download-and-translate-from-NCBI-2.png width=800/></p>

保存为`Drug-Gene.txt`形式

<a name="modify-format"><h2>格式整理 [<sup>目录</sup>](#content)</h2></a>

<a name="perlscript"><h3>perl脚本 [<sup>目录</sup>](#content)</h3></a>

原文件每一条记录的格式为：

```
1. J Neurol Sci. 2016 Jul 15;366:149-154. doi: 10.1016/j.jns.2016.05.019. Epub 2016 
May 13.

Association of MDR1, CYP2D6, and CYP2C19 gene polymorphisms with prophylactic
migraine treatment response.

Atasayar G(1), Eryilmaz IE(2), Karli N(3), Egeli U(2), Zarifoglu M(1), Cecener
G(2), Taskapilioglu O(1), Tunca B(2), Yildirim O(1), Ak S(2), Tezcan G(2), Can
FE(4).

Author information: 
(1)Uludag University, Faculty of Medicine, Department of Neurology, Turkey.
(2)Uludag University, Faculty of Medicine, Department of Medical Biology, Turkey.
(3)Uludag University, Faculty of Medicine, Department of Neurology, Turkey.
Electronic address: nkarli@uludag.edu.tr.
(4)Uludag University, Faculty of Medicine, Department of Biostatistics, Turkey.

Prophylactic therapy response varies in migraine patients. The present study
investigated the relationship between the resistance to the drugs commonly used
in prophylactic therapy and the possible polymorphic variants of proteins
involved in the metabolism of these drugs. Migraine patients with the MDR1 3435TT
genotype exhibited a better treatment response to topiramate than migraine
patients with the CC and CT genotypes (p=0.020). The MDR1 C3435T polymorphism was
also found to be a higher risk factor for topiramate treatment failure in a
comparison of the number of days with migraine (β2=1.152, p=0.015). However,
there was no significant relationship between the treatment response to
topiramate and either the CYP2D6 or CYP2C19 polymorphism, and there were no
significant correlations between the treatment responses to amitriptyline,
propranolol, and valproic acid and the MDR1, CYP2D6 and CYP2C19 gene
polymorphisms. This is the first study to investigate the effect of the
polymorphic variants on prophylactic therapy response in migraine patients.

Copyright © 2016. Published by Elsevier B.V.

DOI: 10.1016/j.jns.2016.05.019 
PMID: 27288795  [Indexed for MEDLINE]
```

要整理成的目标格式为
> - 以`>`为记录起始符，接着doi信息
> - 接着是摘要信息，将多行合并为一行

如下（以下为了方便展示，没有合并成一行）：

```
> 1. J Neurol Sci. 2016 Jul 15;366:149-154. doi: 10.1016/j.jns.2016.05.019. Epub 2016 May 13.
Prophylactic therapy response varies in migraine patients. The present study
investigated the relationship between the resistance to the drugs commonly used
in prophylactic therapy and the possible polymorphic variants of proteins
involved in the metabolism of these drugs. Migraine patients with the MDR1 3435TT
genotype exhibited a better treatment response to topiramate than migraine
patients with the CC and CT genotypes (p=0.020). The MDR1 C3435T polymorphism was
also found to be a higher risk factor for topiramate treatment failure in a
comparison of the number of days with migraine (β2=1.152, p=0.015). However,
there was no significant relationship between the treatment response to
topiramate and either the CYP2D6 or CYP2C19 polymorphism, and there were no
significant correlations between the treatment responses to amitriptyline,
propranolol, and valproic acid and the MDR1, CYP2D6 and CYP2C19 gene
polymorphisms. This is the first study to investigate the effect of the
polymorphic variants on prophylactic therapy response in migraine patients.
```

perl脚本：`format_NCBI_abstract.pl`
> 主要实现的思想：每条记录都包含以下7个部分
> - 头信息
> - 文章题目
> - 作者
> - 作者信息
> - 摘要
> - Copyright
> - DOI与PMID
> 
> 且每部分都用空格隔开，这样如果当前行非空，而前一行为空，则说明进入了一个新的部分，同时对当前所处的部分进行计数，只保留第一部分和第五部分，即头信息和摘要，写进格式化的文本里

```
open FILE,"<$ARGV[0]" or die "$!\n";
$count=0;
$line_previous='';
while(<FILE>){
	chomp;
	$line_current=$_;
	# 判断是否进入新的信息区块
	if((!$line_previous) && $line_current){
		$count++;
	}
	# 判断是否进入新的记录
	if($line_current =~ /^\d+\./){
		$count=1;
		print ">";
	}
	# 写出记录的第一和第五部分
	if($count==1||$count==5){
		# 非空行继续追加，遇到空行则换行
		if($line_current){
			print $line_current;
		}else{
			print "\n";
		}
	}
	$line_previous=$line_current;
}

close(FILE);
```

<a name="batch-format"><h3>批量格式化 [<sup>目录</sup>](#content)</h3></a>

首先，将之前下载好的摘要信息文件放在一个文件夹下

去除window与linux换行符不同对文本处理带来的干扰

```
$ dos2unix /Path/To/*
```

开始批量格式化

```
$ ls /Path/To/Dir | while read i;
do
	perl /Path/To/format_NCBI_abstract.pl /Path/To/Dir/$i >/Path/To/Dir/${i}.format
done
```

<a name="use-googletrans"><h2>googletrans批量翻译 [<sup>目录</sup>](#content)</h2></a>

<a name="install-and-basic-usage"><h3>googletrans安装与基本操作 [<sup>目录</sup>](#content)</h3></a>

Googletrans是一个非官方的python库，能调用谷歌翻译API，能进行语言的检查和翻译

底层依赖的模块：requests and future

安装：

```
$ pip install googletrans
```

基本操作：

```
>>> from googletrans import Translator
>>> translator = Translator(service_urls=['translate.google.cn']) # 工具默认service URL为translate.google.com，需要改为translate.google.cn
>>> translator.translate('안녕하세요.',src='la',dest='ja')
# src可以设置source language，若未给定，会进行自动检测
# src可以设置目标语言，若未给定，默认为英语en，想设为中文，则为zh-CN
# 执行该语句只能得到一个translator对象，若想得到翻译后的文本，需要执行
#     print(translator.translate('안녕하세요.',src='la',dest='ja').text)
```

<a name="batch-translate"><h3>批量翻译 [<sup>目录</sup>](#content)</h3></a>

用python脚本实现这部分的操作

实现思路：

> - 首先准备好Drug-Gene Pairs的文本 (`drug_gene_pairs.txt`)
> - 从该文本中读取Drug-Gene Pairs，根据Drug-Gene Pairs找到对应的格式化的摘要信息文本，从中读取每一条记录，用googletrans进行翻译
> 
>    每翻译完一条记录输出一次结果报告，格式如下：

| | | | | |
|:---|:---|:---|:---|:---|
|Drug|Gene|doi|abstract|translation|

python脚本：`batch_translation.py`

```
import sys
import re
import os
from googletrans import Translator

# 生成translator对象
translator = Translator(service_urls=['translate.google.cn'])

# 获取脚本传入参数
fname_pairs=sys.argv[1]
dir_abstract=sys.argv[2]

# 读入Drug-Gene Pairs文本
f_pairs=open(fname_pairs,'r')
for line_pairs in f_pairs:
	line_pairs=line_pairs.rstrip()
	line_pairs=line_pairs.split('\t')
	# 打开Drug-Gene Pair对应的摘要信息文本
	# 先判断文件是否存在
	if os.path.exists(dir_abstract+line_pairs[0]+"_"+line_pairs[1]+".txt.format"):
		f_abstract=open(dir_abstract+line_pairs[0]+"_"+line_pairs[1]+".txt.format",'r')
		line_abstract=f_abstract.readline().rstrip()
		while line_abstract:
			# 用正则表达式匹配记录起始行
			if re.match('^>',line_abstract):
				header=line_abstract
				# 考虑摘要部分为空的情况
				nextline_abstract=f_abstract.readline().rstrip()
				if re.match('^>',nextline_abstract):	# 摘要部分为空时
					print(line_pairs[0]+"\t"+line_pairs[1]+"\t"+header+"\tNot available\tNot available\n",end='')
				else :	# 摘要部分为存在时
					abstract=nextline_abstract
					# 翻译摘要
					abstract_trans=translator.translate(abstract,dest='zh-CN').text
					print(line_pairs[0]+"\t"+line_pairs[1]+"\t"+header+"\t"+abstract+"\t"+abstract_trans+"\n",end='')
					line_abstract=f_abstract.readline().rstrip()
	else :
		print(line_pairs[0]+"\t"+line_pairs[1]+"\tNot available\tNot available\tNot available\n",end='')
```




参考资料：

[googletrans使用手册](#https://github.com/ssut/py-googletrans)
