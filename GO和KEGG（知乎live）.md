# Basic Concept

## What is Gene Ontology?

基因"本体论"：是对**基因产物**的一种描述方式。  

1. **Cellular component, CC.** 一般用来描述基因产物的发挥作用的位置，比如一个蛋白可能定位在细胞核中，也可能定位在核糖体中；
2. **Biological process, BP**  描述的是指基因产物所联系的一个大的生物功能，或者说是它们要完成的一个大的生物目标，例如有丝分裂或嘌呤代谢；
3. **Molecular function, MF**  主要是指基因产物分子所执行的任务，例如一个蛋白质可能一个转录因子或是一个载体蛋白。  

这些构成了基因的注释信息（annotation）。



## When do we use GO analysis?

### 1. RNA-seq （ctrl， treatment）

- cufflink: 计算基因表达量

  **ctrl** gene expression distribution    

  **treatment** gene expression distribution  

- cuffdiff: 计算基因表达差异  

  ctrl vs. treatment $\to$​ **DEG**  

  **DEG**: differential expression genes

### 2. DEG $\to$ GO annotation

通过查看差异表达基因（DEGs）的GO注释（CC/BP/MF），判断treatment对基因的改变在哪些地方产生了富集（enrichment），从而明确生物学意义。

## how to test if GO is enriched?

方法是通过**GO enrichment analysis（GO 富集分析）**来看富集情况，也就是DEGs相对于背景是否集中于某一通路/定位/功能。    

- 如果研究对象是模式生物（model organism），那么一般已经存在着 annotated database  ，可以直接参考；
- 如果研究对象是非模式生物（non-model organism），那么我们需要寻找是否有其他人已经注释好的database；
- 如果找不到对应的database，那么需要用blast方法把query序列与已知database进行对比，推测其注释信息。（blast2GO)

GO富集分析的过程其实就是分配GO term的过程，模式生物就是已经分配好term了。一般有参考基因组的物种都能找到相应的annotation database.

## Other analyses

- **KEGG PATHWAY**

  6种通路：细胞过程（Cellular Processes）、环境信息处理（Environmental Information Processing）、遗传信息处理（Genetic Information Processing）、人类疾病（Human Diseases）、新陈代谢（Metabolism）、生物体系统（Organismal Systems）

- **DO**：disease ontology。对临床具有较大意义。

# 分析操作流程

## 前置工作

1. RNA-seq 得到了fq文件，通过tophat2/hisat/star等比对软件得到BAM文件
2. 使用cufflink利用已知转录本对表达情况进行量化
3. 使用cuffdiff比较samples之间基因表达的差异性。

## 代码

就是手动设置差异基因的筛选条件，再调用几个现成的包，感觉这么分析出来大概不如GSEA全面，就不仔细学习了。

```
###########################################################
# 2020/3/8  
# test GO analysis and KEGG pathway analysis
############################################################
rm(list = ls())
 
# 1、 RNAseq  fastq -> BAM (tophat2, hiast ,star)
# 2、 cufflink BAM
# 3、 cuffdiff BAM GTF
 
# 1. load cuffdiff result
cuffdiff_result = read.table(file="../Desktop/test_data/rnaseq_test_date/diff_out1/gene_exp.diff",header = T,sep = "\t")
cuffdiff_result$sample_1 = "ctrl"
cuffdiff_result$sample_2 = "treat"
 
#test_id 或者 gene_id 称为 gene samble = samble
 
# 2. select DEG
# Ⅰ. FPKM1 or FPKM2 >1
# Ⅱ. log2(fold change) >1 or < -1
# Ⅲ.  p_value <0.05
select_vector = (cuffdiff_result$value_1 > 1 | cuffdiff_result$value_2 > 1 ) & abs(cuffdiff_result$log2.fold_change.) >= 1 &  (cuffdiff_result$p_value < 0.05)
cuffdiff_result.sign = cuffdiff_result[select_vector,]
 
 
output.gene_id = data.frame(gene_id = cuffdiff_result.sign$gene_id)
write.table(output.gene_id , file = "../Desktop/test_data/GO,kegg(live7)/sign_gene_id_text",col.names = F ,row.names = F,sep = "\t",quote = F)
 
##################################
# 在R上做
# setup R package
###################################
library(clusterProfiler)
# 用来做富集分析
library(topGO)
# GO看图用
library(Rgraphviz)
# 调用上面两个包
library(pathview)
# 看 KEGG pathway
library(org.Hs.eg.db)
# 人的注释文件，去 bioconductor 可以搜其他的注释文件（模式生物都有）
 
###################################
# GO 分析
###################################
DEG.gene_symbol = as.character(output.gene_id$gene_id)
columns(org.Hs.eg.db) #查看常用类型，用于下面 keyType 填写
 
DEG.entrez_id = mapIds(x = org.Hs.eg.db,
                      keys = DEG.gene_symbol,
                      keytype = "SYMBOL",
                      column = "ENTREZID") # 转化ID，一般用 ENTREZID ,其中会有转换不成功的NA
DEG.entrez_id = na.omit(DEG.entrez_id)  #剔除NA
 
 
######## GO.BP
enrich.go.bp = enrichGO(gene = DEG.entrez_id,
                        OrgDb = org.Hs.eg.db,
                        keyType = "ENTREZID",
                        ont = "BP",
                        pvalueCutoff = 0.01,
                        qvalueCutoff = 0.05,
                        readable = T) #pvaluecutoff 是 pvalue 的阈值，富集的统计显著性要小于0.01, q 是 p 的修正值
 
######### GO.CC
enrich.go.cc = enrichGO(gene = DEG.entrez_id,
                        OrgDb = org.Hs.eg.db,
                        keyType = "ENTREZID",
                        ont = "CC",
                        pvalueCutoff = 0.01,
                        qvalueCutoff = 0.05,
                        readable = T)
 
########## GO.MF
enrich.go.mf = enrichGO(gene = DEG.entrez_id,
                        OrgDb = org.Hs.eg.db,
                        keyType = "ENTREZID",
                        ont = "mf",
                        pvalueCutoff = 0.01,
                        qvalueCutoff = 0.05,
                        readable = T)
 
 
########## barblot, dotplot
barplot(enrich.go.bp)
barplot(enrich.go.cc)
barplot(enrich.go.mf)
 
dotplot(enrich.go.bp)
 
########## plotGOgraph (树形图)
pdf(file = "../Desktop/test_data/GO,kegg(live7)/enrich.go.bp.tree.pdf",width = 10,height = 15)  #直接保存成pdf文件
plotGOgraph(enrich.go.bp)
dev.off() # 关闭画图，与pdf一套
 
 
 
#########################
# KEGG pathway analysis
#########################
kegg.out = enrichKEGG(gene = DEG.entrez_id,
                      organism = "hsa",
                      keyType = "kegg",
                      pvalueCutoff = 0.05,
                      pAdjustMethod = "BH",
                      qvalueCutoff = 0.1)
barplot(kegg.out)
 
 
 
 
#############################
# 如果是非模式生物，但是有参考基因组
# 以番茄为例子
source("https://bioconductor.org/biocLite.R")
BiocManager::install("AnnotationHub")
BiocManager::install("biomaRt")
 
# 载入包
library(AnnotationHub)
library(biomaRt)
 
# 制作 OrgDb
hub <- AnnotationHub::AnnotationHub()
 
#使用query在我们制作的OrgDB --> hub里面找到番茄相关的database即org.Solanum_lycopersicum.eg.sqlite 注：Solanum_lycopersicum是番茄的拉丁名和它对应的编号AH59087
 
query(hub, "Solanum")  # Solanum番茄的拉丁名
 
# 下载下来
Solanum.OrgDb <- hub[["AH59087"]]
#此时，番茄的database就会赋值到变量Solanum.OrgDb
```



---

参考：  

孟神视频：https://www.bilibili.com/video/BV14W411q7gi

GO注释和富集分析概念介绍：https://www.jianshu.com/p/7177c372243f  

基于blast进行GO功能注释：https://www.jianshu.com/p/37494fed484a

