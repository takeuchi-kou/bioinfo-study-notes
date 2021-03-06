# Hi-C数据分析相关的基本概念
## 染色质的层级结构
![](https://files.mdnice.com/user/20439/2055cff1-87d7-40cb-8920-90888f3808e9.png)
研究不同层级结构的互作关系需要不同的分辨率精度。上图示意了不同分辨率分别能反映的互作结构层次。

- 老生常谈的基因组三维层级结构示意图

![](https://files.mdnice.com/user/20439/1bb05fbc-4146-4125-b959-a23af57a6463.png)
a） 染色体有各自的区室，并非随意分布  
b） 靠近核小斑（nuclear speckle）的区域为 compartment A，呈现转录活跃状态；而 compartment B 一般分布在靠近核仁或核膜的位置，呈现转录抑制状态。

![](https://files.mdnice.com/user/20439/22f6fbca-0a1d-4078-b05e-d7fb8e13835d.png)
c） TAD之间依靠CTCF蛋白进行分隔，TAD中间存在多个loop结构  
d） NIPBL-MAU2蛋白复合物与黏连蛋白（cohesin）相互作用，挤压DNA结构形成loop

- 敲除 TAD 相关蛋白对 TAD 和 compartment 结构产生不同影响

![](https://files.mdnice.com/user/20439/cf6e6179-6e5d-4b27-9b3d-5960f5695b73.png)
上边几张图可以看出敲除后的TADs peak不见了，下面几张则显示敲除NIPBL的compartment反而显示出了更加精细的结构。  
这显示了TAD和compartment结构受不同调控机制影响。

- 总结：  

1. 转录性质类似元件的聚集与Compartment A/B的形成相关
2. Loop extrusion调控了局部的promoter-enhancer之间的互作，并对全局的转录、分区环境可能起相反作用。
3. The genome is organized in a hierarchical manner through mechanisms such as loop extrusion and transcription-related compartmentalization

## 计数矩阵、热图及分辨率
![](https://files.mdnice.com/user/20439/40d98446-6055-4980-b9e9-eb32d18864db.png)
分辨率指的是进行互作关系统计时DNA被分割成的bin的长度。 

## cis互作和trans互作

![](https://files.mdnice.com/user/20439/c8cf6698-0a2b-4aa9-8e06-ec9e9c805773.png)
同一条染色质上的互作关系为cis，不同染色质上为trans

# Hi-C分析流程
## 需要用到的软件概览

![](https://files.mdnice.com/user/20439/3455ff4a-f52c-4bc2-9a92-48847daad71d.png)
## Hi-C pro
### HiC-Pro workflow
![](https://files.mdnice.com/user/20439/7004e17e-ee6a-4a55-a2ab-189e3c130ef8.png)
### HiC-Pro的安装：基于py2.7
在安装HiC-Pro之前需要先配置好如下环境
```
conda create --prefix=PATHWAY python=2.7
source activate PATHWAY
conda install -p PATHWAY -c bioconda bx-python
conda install -p PATHWAY -c ipyrad pysam
conda install -p PATHWAY -c conda-forge numpy
conda install -p PATHWAY -c conda-forge scipy
conda install -p PATHWAY -c bioconda iced
```
然后源码编译安装
```
tar -zxvf HiC-Pro-2.11.4.tar.gz
cd HiC-Pro-2.11.4
vi config-install.txt
make configure
make install
```
由于HiC-Pro是一个集成包，所以在初始化时需要定义一下调用的包的安装位置，如下图。
![](https://files.mdnice.com/user/20439/aa329b21-735e-4b3c-9c97-8591c1858b36.png)

### HiC-Pro的使用
https://github.com/nservant/HiC-Pro

- Usage: 
```
HiC-Pro -i INPUT -o OUTPUT -c CONFIG [-s ANALYSIS_STEP] [-p] [-h] [-v]
-i --input INPUT: input data folder; Must contains a folder per sample with input files
-o --output OUTPUT: output folder
-c --conf CONFIG: configuration file for Hi-C processing
```
-i：存放数据的文件夹，此文件夹下的每对双端测序文件都
需要重新建立一个文件夹。如下图
![](https://files.mdnice.com/user/20439/7de2b651-ed3b-4399-963c-c0156cda2d66.png)

- Config：

数据后缀名称的设置，需要与输入的fq.gz文件后缀一致
![](https://files.mdnice.com/user/20439/fcb4add9-718a-4627-8fb7-1e582ae755fc.png)

bowtie比对的参数设置。一般使用默认参数即可，仅需将`BOWTIE2_IDX_PATH`设置为索引文件所在路径。
![](https://files.mdnice.com/user/20439/c5e22d5a-52bd-41a6-8612-6c3b63a78ddb.png)

`Annotation files`设置使用的参考基因组名称以及基因组大小。后者在软件安装时有专门的注释文件可供使用。
Digestion Hi-C的`GENOME_FRAGMENT`时酶切位点文件，同样为软件自备，只需选择合适的即可。
![](https://files.mdnice.com/user/20439/8220f628-e3f0-4c67-9ba0-0cd6a622e4ce.png)

`BIN_SIZE`就是矩阵分辨率，输入需要的数值即可。
![](https://files.mdnice.com/user/20439/53de6b59-5967-4744-b0f9-f7a57b3bb894.png)

- results

官方文档：http://nservant.github.io/HiC-Pro/RESULTS.html

![](https://files.mdnice.com/user/20439/2d5da6c1-06e3-4db0-a3ce-84666b7bc383.png)
results分为bowtie比对结果和Hi-C结果

Hi-C结果中，最重要的就是`*.allValidPairs`这个文件，后续数据分析都是基于这个文件。

matrix中的iced是校正后的数据，用的是ice方法。

\*.allValidPairs一般包含12列信息：read name, chr_read1, pos_reads1, strand_read1, chr_read2, pos_reads2, 
strand_read2, fragment_size, res_frag_name1, res_frag_name2, mapq_read1, mapq_read2

![](https://files.mdnice.com/user/20439/5f28ae37-b899-4630-8df7-94d753aa6758.png)

从*.allValidPairs可得到hicpro（\*.matrix & \*.bed）文件

.bed文件储存着bin的位置信息
![](https://files.mdnice.com/user/20439/01b9e8c0-e131-4407-8e7b-306fff224f71.png)
.matrix储存互作count数量。
![](https://files.mdnice.com/user/20439/fbfc4261-3197-4219-a3e4-9ba49497b26c.png)

## 评估最高分辨率：juicer

https://github.com/aidenlab/juicer

- 判断标准：

某分辨率下，大于1000交互的bin所占的比例，
是否大于80%。

- 用法：

找到`PATHWAY/juicer/juicer/misc/calculate_map_resolution.sh`脚本，运行即可

```
nohup sh calculate_map_resolution.sh *.allValidPairs coverage_output &
```
`coverage_output`是自己指定的输出文件。
运行结果：
![](https://files.mdnice.com/user/20439/fd5817e9-bf03-49ce-8aba-1964cd7f3ad5.png)

## Call A/B compartment

### A/B compartment
![](https://files.mdnice.com/user/20439/bf6f82df-636b-468a-9de1-1ec0e8e7ad27.png)
（Lieberman-Aiden et al., Science, 2010）

在这篇最早发表的文章中，作者发现把Hi-C结果标准化后，出现了区别非常明显的格子形状（c图）。将数据用PCA降维后，出现了AB两个主成分，一个是正的一个是负的，即A/B compartment。其中正的为A，负的为B。

![](https://files.mdnice.com/user/20439/64a99f08-3062-46a1-856e-31a3ff854b88.png)

**A compartments**：开放的染色质，表达活跃，基因丰富，具有较高的GC含量，包含用于主动转录的组蛋白标记，通常位于细胞核的内部。

**B compartments**：关闭的染色质，表达不活跃，基因缺乏，结构紧凑，含有基因沉默的组蛋白标志物，位于核的外围

![](https://files.mdnice.com/user/20439/7140edef-40a9-4f56-af3c-30345aa0c2de.png)
最后一行Eigenvecotr的正负区分了A/B compartments，结合上面几行图像信息可以明显看出这种特点
### 工具1：HiTC(pca.hic)

![](https://files.mdnice.com/user/20439/9c60b8d6-68c5-4407-a347-b2c9b2de4c8c.png)

### 工具2：juicer (eigenvector)

https://github.com/aidenlab/juicer/wiki/Eigenvector

juicer只能单个染色体分析，所以要写循环。
```
for chr in {1..22}
do
java -jar juicer_tools.jar eigenvector <NONE/VC/VC_SQRT/KR> *.hic $chr BP <binsize> 
./juicer_output/${chr}_eigen.txt
done
```

**<NONE/VC/VC_SQRT/KR>**：
选择标准化方式。关于这几种标准化方式的特点，在github上查到了答案但是看不懂，先放这吧。不知有无大佬能解答：
>VC: vanilla coverage (overcorrects low coverage loci)

>VC_SQRT: square root vanilla coverage (creates a matrix whose row and column sums are all approximately equal)

>KR : Knight and Ruiz normalization (works at both low and high resolutions.)

>Ref: Knight, P., and Ruiz, D. (2012). A fast algorithm for matrix balancing. IMA J. Numer. Anal. Published online October 26, 2012. http://dx.doi.org/10.1093/imanum/drs019.

https://github.com/aidenlab/juicer/issues/19

 **< binsize>** :选择分辨率大小
 
**`*.hic`到`*.allValidPairs`的格式转换：**


`*.hic`是需要输入的数据文件。而将数据格式从`*.allValidPairs`转换为`*.hic`需要用到HiC-Pro的一个脚本：

```
PATHWAY/HiC-Pro_2.11.4/bin/utils/hicpro2juicebox.sh -i ./*.allValidPairs -g PATHWAY/HiCPro_2.11.4/annotation/chrom_hg19.sizes -j PATHWAY/juicer/scripts/CPU/common/juicer_tools.jar 
-t tmp -o ./
```
命令里除了`*.allValidPairs`文件位置，还需要给出基因组注释大小文件的位置以及juicer的安装位置。

## Call TADs
### TADs 和 loop
TADs：topological associated domains 拓扑相关结构域。是指在染色质区室中，互相作用相对频繁的基因组区域。（第一行的每一个红色三角就是一个TAD）
![](https://files.mdnice.com/user/20439/dd1e3727-1c38-458d-9f7f-84b44a9515bf.png)

在哺乳动物基因组中，TAD通常由**CTCF这个转录抑制因子**给分割开来。CTCF还会和**Cohesin蛋白复合物**结合，帮助基因组形成相对稳定的三维结构。

![](https://files.mdnice.com/user/20439/5ba0a958-e775-4e90-9a13-f0214fbdf9c5.png)

正由于此，**两个TAD之间的转录活性非常低**（转录需要打开DNA），而结合CTCF等转录抑制因子的DNA元件，也被称为insulator（绝缘子）。

**在TAD内部转录非常活跃**。CTCF在帮助基因组DNA凹造型的同时，把DNA元件给绑到了一起。而这样相互作用的元件，通常是**enhancer（增强子）和promoter（启动子）**，他们往往分布在相距很远的染色质区域，却因为CTCF蛋白在三维空间中聚集在一起，我们把这种结构称之为**loop**。

![](https://files.mdnice.com/user/20439/498f1b79-209f-40b9-87bf-a44f26cf8cec.png)

（部分解释引自知乎https://www.zhihu.com/question/50270622/answer/1715108352）

### Call TADs

由于TAD结构对功能有非常大的影响，因此识别TAD边界至关重要。  
现有有如下几种方法：

>Directionality Index，DI   
Insulation Score  
Arrowhead  
TADbit  
DomainCaller  
TADtree  

其中前三种较为常用。

### Call TADs: DI

![](https://files.mdnice.com/user/20439/735e4a29-83f4-4dfb-a49a-9856faf0d46e.png)
对于TAD边界位置来说，它的左右位置互作方向是相反的。如上图，A点偏好上游，而B点偏好下游。directionality index（方向指数）这个方法就是根据这个原理设计的。

![](https://files.mdnice.com/user/20439/c75ee95c-211f-4061-a956-c00d9588c23c.png)

DI-HMM 方法首先采用方向指数(directionality index)来表征一个染色体区域与上下游交互作用的偏差，当这个偏差出现符号跳转时，意味着可能出现 TAD 的边界．在方向指数中利用隐马尔可夫模型可以推断出 TAD 的具体位置。**（但是HMM模型到底是如何在算法中起作用的？）**

![](https://files.mdnice.com/user/20439/ae437fed-9db3-4e75-8418-a9692411b73c.png)

### Call TADs: Insulation Score

https://github.com/dekkerlab/crane-nature-2015  
![](https://files.mdnice.com/user/20439/72a82d67-6a4f-4e07-ba28-ee4de7cad25f.png)
首先在热图上定义一个滑动窗口，在滑动过程中统计每个窗口中互作的数量，这样可以得到下面那条线。这样就可以得到互作数量发生转变的位置。


```
perl matrix2insulation.pl -i *.dense_header.matrix -is 500000 -ids 250000 -im mean -nt 0.1 -o ./  
```
---
- 参数解释  

i: 交互矩阵  
is: insulation square，默认500000，动物推荐500000，植物推荐200000-250000.  
ids: insulation delta span，默认250000，植物推荐100000-200000. is必须要比ids大. 
nt: delta bound阈值，提高阈值可以过滤一些边界不明显的TAD.  
bmoe: boundary margin of error.一般不予设定。  

---
- 如何得到交互矩阵

hicpro(*.matrix & *.bed) → IS input matrix

**Step1:** 将稀疏矩阵转成稠密矩阵。  
（稀疏矩阵:矩阵中0元素的个数远大于非零,且0元素分布无规律；稠密矩阵反之）


```
Python2.7 PATHWAY/HiC-Pro_2.11.4/bin/utils/sparseToDense.py *_50k_iced.matrix -b *_50k_abs.bed --perchr > mESC_50k.log 2>&1
```
**Step2:**  添加表头  
采用R或者python/perl  
表头形式`bin6000000|ce10|chrX:1-10001`
bin的顺序|物种参考基因组的名字|染色体位置信息  

下图是利用R添加表头的代码示例
![](https://files.mdnice.com/user/20439/bb0bf011-378b-49ac-9e68-18c911db9d9b.png)
注意：  
line5调用的参考基因组，之前用的哪个版本比对，这时候就调用哪个版本。  
line6要输入数据分析所使用的分辨率数值

---
- 输出结果  

输出4个文件
![](https://files.mdnice.com/user/20439/09a7c2b7-2367-46c3-aaed-af53d65a49a9.png)

.insulation.bedGraph
![](https://files.mdnice.com/user/20439/efdf9a55-be0c-403f-8d33-df02a38b7cfb.png)
前三列是每个BIN的位置信息，最后一列是insulation score的得分。

.insulation.boundaries.bed
![](https://files.mdnice.com/user/20439/09282ec9-33d3-41ed-a2fb-204a90b465b5.png)
边界信息文件。第四列是包含位置信息的表头，最后一列是边界强度的得分情况

### Call TADs: Arrowhead
https://github.com/aidenlab/juicer/wiki/Arrowhead  
是一个juicer工具。

```
java -Xmx8g -jar PATHWAY/juicer/scripts/CPU/common/juicer_tools.jar arrowhead ./*.hic --
threads 8 -r 10000 -k KR ./contact_domains_10kb
```

## Call loops: HiCCUPS

https://github.com/aidenlab/juicer/wiki/HiCCUPS

同样是juicer工具。可以用GPU或CPU运算，一般还是选择CPU。但是GPU运算速度快一点。
```
java -Xmx8g -jar PATHWAY/juicer/scripts/CPU/common/juicer_tools.jar hiccups --cpu
--threads 8 -r 5000 -k KR ./*.hic ./
```

## 可视化工具

有以下几种：

• Juicebox  
• WashU: http://epigenomegateway.wustl.edu/  
• HiTC  
• HiCPlotter  
• HiCExplorer  
• R  

这个比较直观，就不记了。用到的时候再说吧。
