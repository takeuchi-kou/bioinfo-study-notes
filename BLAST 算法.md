# BLAST 算法

课程视频：https://www.youtube.com/watch?v=g0nSH17psDc

## What is blast ?

BLAST（Basic Local Alignment Search Tool）是一套在蛋白质数据库或DNA数据库中进行相似性比较的分析工具。BLAST程序能迅速与公开数据库进行相似性序列比较。BLAST结果中的得分是对一种对相似性的统计说明。

## Types of blast



<img src="https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110180408622.png" alt="image-20211110180408622" style="zoom:50%;" />

1. BLASTP是蛋白序列到蛋白库中的一种查询。库中存在的每条已知序列将逐一地同每条所查序列作一对一的序列比对。

2. BLASTX是核酸序列到蛋白库中的一种查询。先将核酸序列翻译成蛋白序列（一条核酸序列会被翻译成可能的六条蛋白），再对每一条作一对一的蛋白序列比对。

3. BLASTN是核酸序列到核酸库中的一种查询。库中存在的每条已知序列都将同所查序列作一对一地核酸序列比对。

4. TBLASTN是蛋白序列到核酸库中的一种查询。与BLASTX相反，它是将库中的核酸序列翻译成蛋白序列，再同所查序列作蛋白与蛋白的比对。

5. TBLASTX是核酸序列到核酸库中的一种查询。此种查询将库中的核酸序列和所查的核酸序列都翻译成蛋白（每条核酸序列会产生6条可能的蛋白序列），这样每次比对会产生36种比对阵列。  

其中，最迅速的是DNA-DNA比对以及protein-protein比对。  

## Heuristic Search

![image-20211110181306005](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110181306005.png)

左边有一段序列是相同的，而右边不存在相同序列。这种情况下，BLAST会选择左边而忽略右边。这样使BLAST算法变得更快速。

## Algorithm

算法流程：

![image-20211110181645816](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110181645816.png)

### 1. Find all possible words in query

例如一段氨基酸序列为LIAWHCMPNAAA，把它分为4个k=3的k-mer，根据BLOSUM62矩阵，每个K-mer最高得分如下：

![image-20211110201758190](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110201758190.png)

BLOSUM62 matrix

![image-20211110192831312](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110192831312.png)

BLOSUM62打分细节可以参考：https://blog.csdn.net/weixin_43770577/article/details/104023846

得到分数后，通过指定一个threshold把得分较低的K-mer去掉，只留下WHC 和MPN。

### 2. Scan the database for occurrence of  words

 ![image-20211110205624993](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110205624993.png)

在database中寻找出现在query中的words（这一步计算机可以很快完成）

### 3. Score the alignment

![image-20211110210949823](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110210949823.png)

从k-mer向两个方向延伸，如果variant在矩阵中得分为正则继续延伸（extend），如果无法延伸则跳过这些不能配对的部分（join the gaps）。



等到延伸至无法继续时，计算所有配对的序列，得到一个HSP值

![image-20211110211313359](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110211313359.png)

根据矩阵计算所有匹配得分。另外gap open和gap extension需要另算罚分。最终得分为54分。

![image-20211110211814026](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110211814026.png)

## Analyse the score

![image-20211110212315644](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211110212315644.png)

