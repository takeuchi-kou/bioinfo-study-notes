本次作业复现一片cell文章的一个热图。从CCLE数据库下载RNA-seq表达矩阵，然后根据图里面的基因名字和细胞系名字，取出需要的表达矩阵，然后热图可视化，做出如下这样的图
![](https://files.mdnice.com/user/20439/2b0ae600-b1ee-4038-98ac-6a3e737b7b83.png)
A.The relative expression of complement regulatory proteins (CD55, CD46, CRIg, CR1, Factor H, Factor I, FHL1, C4BP, Properdin and C1INH) in BT474, BT549, MDA-MB-231, HCC1937, MDA-MB-361, MDA-MB-436, MDA-MB-468, AU565, SK-BR-3, MCF-7 and MDA-MB-453 cells were evaluated by using mRNA data from the Barretina Cell Line database.

文章原文地址：PMID:32142650,DOI:10.1016/j.cell.2020.02.015
# 下载和读取数据
CCLE数据库下载地址：https://depmap.org/portal/download/

选择`CCLE_Expression_Entrez_2012-09-29.gct`这个文件下载。

读取文件和稍后会用到的包
```
rm(list = ls())
setwd("~/Desktop/homework/10th")
options(stringsAsFactors=F)
library('stringr')
library("dplyr")
library('data.table')
library("reshape2")
library("magrittr")
library("ggplot2")
ccle_expr <- fread("CCLE_Expression_Entrez_2012-09-29.gct",header = TRUE)
```
看一下数据格式
![](https://files.mdnice.com/user/20439/6be0c989-23eb-4b93-84d1-645c1a3a16fe.png)
列名是细胞系名称，description是序列所编码的蛋白。

# 数据、字符的清理和筛选

由于我们需要的细胞系都是乳腺癌的细胞系，所以先把乳腺癌细胞系筛选出来。

```
#select breast cell line's expression in ccle
ccle_col <- colnames(ccle_expr) 
breast_col <- ccle_col[str_detect(ccle_col,"BREAST")] 
breast_exp <- ccle_expr %>% select(breast_col)
colnames(breast_exp) <- breast_col %>% str_replace_all("_BREAST","")
breast_exp <- cbind(ccle_expr[,1:2],breast_exp)
```
并且去掉了细胞系的后缀。得到的乳腺癌细胞系的基因表达水平。
![](https://files.mdnice.com/user/20439/873d0953-e036-4f47-9972-3e068519e260.png)

接下来根据文章热图的注释，把我们需要的细胞系名称和蛋白名称提取出来，并转变为数据集中的字符格式

```
#making list of wanted proteins expression and cell lines
proteins <- strsplit("CD55, CD46, VSIG4, CR1, CFH, CFI, FHL1, C4BPA, C4BPB, CFP, SERPING1",split = ", ") %>% unlist()
proteins %in% breast_exp$Description

cell_line <- strsplit("BT474, BT549, MDA-MB-231, HCC1937, MDA-MB-361, MDA-MB-436, MDA-MB-468, AU565, SK-BR-3, MCF-7, MDA-MB-453",split = ", ") %>% 
  unlist() %>% str_replace_all("-","")
cell_line %in% colnames(breast_exp)
```
这里要注意的是，提取完名称之后发现有几个基因不在列表中，搜了半天发现是因为有别称。把名称换了以后发现所有基因都在其中了。

```
> proteins %in% breast_exp$Description
 [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE
> cell_line %in% colnames(breast_exp)
 [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE
```
最后按照这些基因和细胞系的名称筛选组成绘制热图需要的数据

```
#select for cell line and filter for proteins
data_heatmap <- breast_exp %>% 
  select(Description, cell_line) %>% 
  filter(Description %in% proteins)
```
最后得到的数据如下
![](https://files.mdnice.com/user/20439/8ae08107-9826-476a-9595-94dc30d2127a.png)
![](https://files.mdnice.com/user/20439/f9555540-b4a9-455c-b347-10f592b8c64f.png)

# 绘制热图
先用melt函数把宽表变成长表，再对value值进行log2和scale，便于热图显示差异。
```
#drawing the heatmap
colnames(data_heatmap)[1] <- "ID"
data_m <- melt(data_heatmap,id.vars = "ID")

data_m <- data_m %>% 
  mutate(vlog2=log2(value)) %>% 
  mutate(vlog2_Scale=scale(vlog2)) %>%
  mutate(vscale=scale(value))
head(data_m)
```
melt后的长表格式
![](https://files.mdnice.com/user/20439/f545fd91-63e4-4c2f-9371-0fdfb5f87453.png)

最后设置一下横纵轴的顺序，使之与文章一致

```
#change the order of rows and columns
proteins <- rev(as.vector(proteins))
data_m$ID <- factor(data_m$ID, levels = proteins, ordered = T)
head(data_m)
```
最后绘图

```
p_hm <- ggplot(data_m,aes(x=variable,y=ID,fill = value)) +
  geom_tile() +
  theme(axis.text.x=element_text(angle=45, hjust=1, vjust=1)) +
  scale_fill_gradient2(low = "blue", high = "red",midpoint = 6)
p_hm

p_hm1 <- ggplot(data_m,aes(x=variable,y=ID,fill = vlog2)) +
  geom_tile() +
  theme(axis.text.x=element_text(angle=45, hjust=1, vjust=1)) +
  scale_fill_gradient2(low = "blue", high = "red",midpoint = 3)
p_hm1

p_hm2 <- ggplot(data_m,aes(x=variable,y=ID,fill = vlog2_Scale)) +
  geom_tile() +
  theme(axis.text.x=element_text(angle=45, hjust=1, vjust=1)) +
  scale_fill_gradient2(low = "blue", high = "red",midpoint = 1)
p_hm2
```
fill=value，表达量未经过转变
![](https://files.mdnice.com/user/20439/1ede3a6c-6269-4c25-baae-459b7a81c099.png)

fill=log2value
![](https://files.mdnice.com/user/20439/43633e0f-2f4d-4063-87ca-91714edca3de.png)

fill=scale(log2value)
![](https://files.mdnice.com/user/20439/6a9c5562-1da9-418f-8f06-4a0a465b78bd.png)

文章热图：
![](https://files.mdnice.com/user/20439/2b0ae600-b1ee-4038-98ac-6a3e737b7b83.png)

感觉差别好大啊。。
