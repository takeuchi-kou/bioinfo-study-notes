# 1. 绘制人类基因被研究次数最多的词云

## （1）先根据基因ID绘制出词云
```
rm(list=ls())
setwd("~/homework/7th")

##筛选出人类的GENE ID和对应的Pubmed ID
library(data.table)
library('R.utils')
#fread函数可以快速读取大数据表格
a=fread('gene2pubmed.gz',data.table = F)  
head(a)
# https://www.uniprot.org/taxonomy/9606 找到homo sapiens的物种id为9606
a=a[a$`#tax_id`==9606,]

##制作词频矩阵
library("tm")
library("SnowballC")
library("wordcloud2")
library("RColorBrewer")

#用table()函数对每个gene id进行计数，按照频数从高到低排序，并取top100
tb=as.data.frame(table(a$GeneID))
head(tb)
tb=tb[order(tb$Freq,decreasing = T),]
tb=head(tb,100)  
colnames(tb)[1]='gene_id'
set.seed(1234)
wordLengths=c(0,Inf)
#绘制词云
wordcloud2(tb,size = 0.5, minRotation = -pi/6, maxRotation = -pi/6,rotateRatio = 1)

```
这是词频矩阵最后的结果：

```
     gene_id  Freq
5608    7157 10539
1550    1956  6094
5580    7124  6072
2778    3569  4889
5827    7422  4882
292      348  4650
```

Taxonamy的相关知识参考：https://zhuanlan.zhihu.com/p/90747645?from_voters_page=true

绘图结果：
![](https://files.mdnice.com/user/20439/a962d84b-a03b-46f2-8048-0381fec1b647.png)

## （2） 把Gene ID替换为 gene symbol


```
library(org.Hs.eg.db)
#加载id和symbol的对应关系
ids=toTable(org.Hs.egSYMBOL)
head(ids)
#id和symbol两个表格合并
tbs=merge(ids,tb,by='gene_id')
head(tbs)
# 去除id那列
tbs <- tbs[,-1]
wordcloud2(tbs,size = 0.5, minRotation = -pi/6, maxRotation = -pi/6,rotateRatio = 1)

```

两个表格的格式如下
```
> head(ids)
  gene_id symbol
1       1   A1BG
2       2    A2M
3       3  A2MP1
4       9   NAT1
5      10   NAT2
6      11   NATP
> head(tbs)
        symbol Freq
1       CDKN1A 1743
2       CDKN2A 2497
3         CFTR 1771
4 LOC110806262 2409
5         CCR5 1586
6         COMT 1944
```

绘图结果
![](https://files.mdnice.com/user/20439/9894846d-e2b8-45a3-bcd6-831a0313c49e.png)

# 作业：拓展探索

>>>学徒作业：比如这样的top 100的基因词云，其实可以做出来最近30年的变化规律，只需要你去找到文献的时间年份信息，进行拆分，每个年份独立统计绘图即可。

了解XML：https://www.runoob.com/r/r-input-xml-file.html

了解DTD：https://www.runoob.com/dtd/dtd-intro.html

R对xml的处理：https://www.cnblogs.com/shangfr/p/5564167.html


