# RNA-seq上游分析

## 1. 软件安装(直接使用conda)

1. sratoolkits

   下载，操作，验证NCBI SRA中二代测序数据

   测试：`prefetch -v`

2. fastqc

   可视化展示二代测序数据质量

   测试：`fastqc -v`

3. htslib、bcftools

4. samtools

   安装：`mamba install samtools=1.15.1`

   测试：`samtools --version`

   存放高通量测序比对结果的标准格式sam

5. hisat2

   将测序结果比对到参考基因组上

   测试：`hisat2 -h`

6. htseq

   根据比对结果统计基因count

   测试：`python`

   ​			`import HTSeq`

7. featureCounts

   安装：`mamba install subread`

   测试：`featureCounts -v`

## 2. 获取数据

### 从NCBI获取SRA文件

![image-20220428204640235](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220428204640235.png)

![image-20220428204828075](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220428204828075.png)

![image-20220428204922814](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220428204922814.png)

由于prefetch速度实在太慢了，没找到其他能够批量下载文件的方法，只能一个一个点了。

网上说用aspera下载速度比较快，但是试了半天这种方法好像只能下载fastq文件不能下载sra文件。使用方法先放在这里吧。

### 附：用aspera下载fastq文件

- aspera connect下载地址：

https://d3gcli72yxqn2z.cloudfront.net/connect_latest/v4/bin/ibm-aspera-connect_4.1.3.93_linux.tar.gz

- aspera的安装：

  ```
  tar -zxvf ibm-aspera-connect_4.1.3.93_linux.tar.gz
  sh ibm-aspera-connect_4.1.3.93_linux.sh
  # 加入环境变量
  echo 'export PATH=~/.aspera/connect/bin:$PATH' >> ~/.bashrc
  source ~/.bashrc
  ascp --help  #ok
  ```

- aspera的用法

  > ascp [参数] 目标文件 保存路径
  >  -v verbose mode 实时知道程序在干啥
  >  -T 取消加密，否则有时候数据下载不了
  >  -i 提供私钥文件的地址
  >  -l 设置最大传输速度，一般200m到500m，如果不设置，反而速度会比较低，可能有个较低的默认值
  >  -k 断点续传，一般设置为值1
  >  -Q 一般加上它
  >  -P 提供SSH port，端口一般是33001

- aspera下载示例

  ```
  # 下载SRR3589956_1.fastq.gz文件
  ascp -v -QT -l 300m -P33001 -k1 -i ~/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:vol1/fastq/SRR358/006/SRR3589956/SRR3589956_1.fastq.gz ./
  ```

- fastq文件下载地址的查找方法

  ebi官网输入SRR值，例如`SRR3589956`

  找到如图位置，复制链接即可

  ![image-20220428203541169](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220428203541169.png)

## 3. sra转fastq格式

```
# fastq-dump --gzip --split-3 -A SRR3589958.sra
# --gzip 表示转换后的依然为压缩格式； --split-3 由于双端测序所以需要此参数
# 使用通配符进行批量处理
nohup ls *sra | while read id; do fastq-dump --gzip --split-3 $id; done &
```

## 4. fastqc质控

```
# fastqc -t 8 -q SRR3589958_2.fastq.gz
# -t 线程数，每个线程需要250m内存。 默认输出到当前目录，否则用-o参数修改
# -q 安静模式
nohup ls *fastq.gz |while read id; do fastqc -t 8 -q $id; done &
# 下载需要的质控报告后，删除文件
rm *fastqc*
```

## 5. 序列比对

### 获取参考序列的index

直接Google搜索hisat2，找到hg19的下载链接进行下载

得到压缩文件，解压，得到

![image-20220429095834505](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220429095834505.png)

### hisat2 把序列比对到参考基因组index上

```
# hisat2 -t -x 参考基因组索引位置 -1 双端测序中第一个fasq文件的位置 -2 第二个fastq文件的位置 -S 输出sam文件的位置

for i in `seq 56 58`
 do
 hisat2 -t -x /home/data/t0302032/ncbi_dowloads/refseq/human/hg19/genome -1 /home/data/t0302032/ncbi_dowloads/sra/SRR35899${i}_1.fastq.gz -2 /home/data/t0302032/ncbi_dowloads/sra/SRR35899${i}_2.fastq.gz -S /home/data/t0302032/ncbi_dowloads/sra/aligned/SRR35899${i}.sam
 done
```

![image-20220430100414328](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220430100414328.png)

*一个疑惑：hisat2的官网上对于各种常用的参考基因组都进行了index的制作。所以index的制作原理到底是什么呢？在网上搜索了一下，果然用的也是BW transform和FM index。下次专门学习一下这两个算法。*

> 其他关于hisat/hisat2 的信息：
>
> 1. HISAT= Hierarchical Indexing for Spliced Alignment of Transcripts
> 2. HISAT 是一种splice-aware RNA-seq read aligner。（因为RNA测序得到的read是已经去除intron的，但是参考基因组中存在intron。所以除非read短于50bp，否则推荐使用splice-aware aligner例如hisat，star等）
> 3. 先使用global FM index把每个reads的一部分mapping上去，找到备选位置；然后再进行local alignment。

### samtools：转换sam为bam

```
for i in `seq 56 58`
> do
> samtools view -S /home/data/t0302032/ncbi_dowloads/sra/aligned/SRR35899${i}.sam -b > /home/data/t0302032/ncbi_dowloads/sra/sorted/SRR35899${i}.bam
> done
```

### samtools：按照位置对reads进行排序

```
for i in `seq 56 58`
> do
> samtools sort /home/data/t0302032/ncbi_dowloads/sra/sorted/SRR35899${i}.bam -o /home/data/t0302032/ncbi_dowloads/sra/sorted/SRR35899${i}sorted.bam
> done
```

### HTseq：利用基因组注释信息对reads所属基因计数

```
for i in `seq 56 58`; do htseq-count -r pos -f bam /home/data/t0302032/ncbi_dowloads/sra/sorted/SRR35899${i}sorted.bam /home/data/refdir/server/reference/gtf/gencode/gencode.v25lift37.annotation.gtf > SRR35899${i}.count; done
```

```
less  SRR3589956.count
```

![image-20220430194941579](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220430194941579.png)

## 参考目录

aspera使用 https://www.jianshu.com/p/2987843d97e3

HISAT/HISAT2 https://github.com/griffithlab/rnabio.org/blob/master/assets/lectures/cshl/2021/mini/RNASeq_MiniLecture_02_01_Alignment.pdf

