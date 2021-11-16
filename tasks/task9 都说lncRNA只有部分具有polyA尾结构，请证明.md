

## 作业题目

**lncRNA的特征总结：**

1. 长度在200-100,000nt

2. 没有编码蛋白质潜能

3. 具有细胞或组织类型特异性

4. 表达量和保守性比mRNA低

5. 部分lncRNA不含有polyA尾巴

6. 部分也会翻译小肽段

既然都说lncRNA只有部分具有polyA尾结构，我这里出一个学徒作业，希望大家可以下载人和鼠的gtf文件，以及转录本fasta序列文件，自己去探索一下：

**gtf文件记录了多少个基因，多少个是蛋白编码基因多少个是lncRNA呢？其中各自的具有polyA尾结构的比例是多少呢？**

## 下载和读取gtf/fa格式文件


```
#小鼠基因文件
wget ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/latest_release/gencode.vM27.annotation.gtf.gz
wget ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/latest_release/gencode.vM27.transcripts.fa.gz
#人类基因文件，gtf用上次下载的即可
wget ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/latest_release/gencode.v38.transcripts.fa.gz
```
fa文件使用biostrings包进行读取。参考：https://www.freesion.com/article/78161093375/

先用fread试着读一下，序列以换行符为分隔符断成了几部分。
用biostrings读后，出现了一个S4

```
rm(list = ls())
setwd('~/Desktop/homework/9th')
library("Biostrings")
library("data.table")
Option(stringsAsFactors=F)
mouse_treseq1 = fread("~/database/gencode_mouse/gencode.vM27.transcripts.fa", sep = ">"  ,header = FALSE)
mouse_trseq = readBStringSet("~/database/gencode_mouse/gencode.vM27.transcripts.fa",
                             format = "fasta")
```
fread读取fasta的结果
![](https://files.mdnice.com/user/20439/3cb1ad9b-7e3f-4128-871e-74dcfffdb4c0.png)
biostrings读取fasta的结果
![](https://files.mdnice.com/user/20439/2e2fa414-4097-4303-bf52-7ecf0e22e168.png)

![](https://files.mdnice.com/user/20439/db05ef1c-9677-41b4-ae65-3a344cbf67c9.png)

用fread读取gtf的结果，主要是V9里的各个描述难以分开
![](https://files.mdnice.com/user/20439/fecbec80-f22e-4a71-aa91-303ac3d1a229.png)

用python包gtfparse解析gtf数据
```
import os
os.getcwd()
os.chdir("/home/erica/Desktop/homework/9th")
os.getcwd()
#导入模块
from gtfparse.read_gtf import read_gtf

# 读取gtf文件
df = read_gtf("gencode.vM27.annotation.gtf")
df.to_csv("/home/erica/Desktop/homework/9th/mouse_annotation.csv")
df = read_gtf("gencode.v38.annotation.gtf")
df.to_csv("/home/erica/Desktop/homework/9th/human_annotation.csv")
```


