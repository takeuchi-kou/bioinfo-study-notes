# 1.生物信息学理解：
  使用信息的手段解决生物问题，信息是手段，生物是核心。生物（生物问题）比较重要。
  生信历史。**郝柏林院士的个人主页**。

# 2.入门基本四个方面：

  **linux、编程、统计、生物能力。**

##   2.1.使用linux操作系统，不难，从鼠标到键盘的改变，单文件到批处理。

​     打开查看文件命令：cat,vim,less。复制文件命令：cp。移动文件命令：mv。
​     批处理：（个人补一下），shell循环脚本对文件批处理，正则表达式的应用和管道命令等。

##   2.2.编程能力，将想法做出来，多练容易

​     1.处初期数据，前处理，python/perl。（个人补一下，python语法严谨，便于修改与阅读，且各种库类完全，易于维护。perl使用简单，简单命令可以完成较多事情。两个语言运算速度均没有编译型语言如C，JAVA快，相差10倍左右吧，但是除非写很成熟的软件或者图形界面，一般入 门用不到）

​    2.简易多文件处理：linux shell脚本。

​    3.统计学分析/可视化：R（个人补充：在项目做完要用图片进行显示，图片需要进一步修改美化处理，还需要掌握AI等绘图工具，且论文统计图建议使用矢量图，因为不需要去调整分辨率，实验图片使用高分辨率位图，一般300/600ppi即可，位图色彩丰富，但是图片处理是一个双刃剑，忌科研造假，诚信就是生命）

​    半自助处理数据：重点在R和shell



## 2.3.统计能力，从数据中提取正确信息，比较难，统计决定生信分析的上限

​     统计思维的养成，需要长时间训练和看书，反复纠正和学习

​     例子：BS-Seq：平均突变率0.3%，某位点C10条，T3条，该位点是否为甲基化位点。二项分布计算p值，p值很小认为甲基化位点
​               GO分析：差异表达分析，差异表达基因进行功能注释（DAVID软件），已全部基因为背景，进行统计检验，看差异基因是否能显著富集到某通路。某物种20000基因，A通路1000个基因。找到差异基因500个，300个在通路A，这500个基因是否富集在通路A？
​                              计算p值。p=C300,1000 X C200,19000 / C500,20000

​              数据降维，多元统计，基因为横轴，细胞为纵轴，根据这个矩阵进行聚类，聚类方法：PCA,SVD,层次聚类，tSNE等。

​              序列分析：call motif。y轴坐标值和e值（此处up好卑微……23333）y轴值为4中碱基概率加和。2=log2,4 e值类比为blast e值，随机碰撞的概率。



## 2.4 生物背景：

1.那张芯片。2.一条测序泳道。3.tile是那个能显示荧光的那个点？4.序列。5.引物。6.接头。7.双端测序的比对后加上中间形成的片段。
                    RPKM=1/2FPKM.reads定义和Fragment定义



  # 3.分析推荐：
## 3.1.方向：

DNA：WGS/WES 全基因组/外显子测序。找SNV,SNP,CNV,SV与疾病相连。（商业化最好的是这个）
               RNA：RNA-Seq
               表观：BS-seq，Chip-Seq，ATAC-Seq。

## 3.2.流程：
​     输入 fastq文件fastqc软件做质检cutadapt软件去接头比对（RNA：Hisat2,tophat2,STAR。DNA：BS-Seq:bismark；chip-seq，ATAC，WGS： 
​     bowtie2.bwa)bam文件后续分析（根据项目，GSE，GO,KEGG,motif，基因融合，editing等等等等）

# 4.资料推荐
   半自主：1年左右，熟练操作数据，linux，软件认识，画图。
   再向上就难了，不过学到这里工资和发展很不错呦，23333：分析数据/开发软件

## 4.1.一元统计和多元统计，生信常用算法
​    linux：罗老师三个文件（三块板砖就想吊打linux？你还是太天真了，2333，入门是够了）
​               鸟哥的linux私房菜：工具书

## 4.2. 统计+R（换电脑没钱……）

-  R语言入门
       R语言数据科学（书很好，代码有用）
       R语言编程艺术
       统计基础：
       概率论与数理统计（浙大）
       概率论与数理统计（陈希孺老师）
       概率论基础教程（Ross）

- 应用：
       医学统计学（李晓松）
       一元统计：概率，随机变量，累积分布函数，分布，各种检验与应用。检验值，回归分析，相关系数。
      多元统计资料：
      线性代数及应用
      多元线性分析
      线性代数及应用
      统计学习方法

## 4.3.python
​     廖雪峰老师官方网站
​     跟老齐学python

 课程：
     北京大学生物信息学学习方法
     machine learning in genetics , youtube上有
     邓明华老师，生物信息学中的方法/算法