# snakemake 简介

- 基于Python3
- 快速搭建需要重复实现的分析流程
- 官网doc：https://snakemake.readthedocs.io/en/stable/



# ATAC-seq pipline



- raw FASAQ cutadapt
- FASTQ mapping with bowtie2
- mapping results sort as BAM
- remove PCR duplication
- peak calling



# 用snakemake搭建ATAC-seq分析流程

## 语法规则

snakemake以rule为基本书写单位，格式如图

![snakemake格式概览](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211116084740307.png)

由于snakemake是用python书写的，所以需要注意语法缩进。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211116085016763.png)



### 。。

那个啥，脚本写一半不想写了。。。先这样吧、、
