![](https://files.mdnice.com/user/20439/ee830c10-892b-44c8-94bd-314bf34144f4.png)
其实单细胞的RNAseq上游分析流程跟bulk差不多，只是细节部分不同。比如说需要去dup等。

# 下载数据-sratoolkit
使用sratookit进行数据的下载。

`prefetch` + `SRAaccessionlist`：下载到默认下载路径

`vdb-config -i` ：更改下载设置，可以更改默认路径

`fasterq-dump -S`+`sra_dir` ：把双端测序分成两个fastq文件

本次仅下载一个SRA文件作为练习，实际有一百多个样本

# 质控-fastqc
```
fastqc -q -t 12 -o /output directory/ *.fastq &
```
### 参数：

``-q`` :不输出报告，仅在最后的时候将报告生成文件

``-t``  :使用服务器的核心数目

``-o`` :报告文件输出路径

``*.fq.gz`` :输入文件的名称，此处输入所有后缀为 .fastq的文件。也可以直接读取压缩为gz的序列文件。

结果：
![](https://files.mdnice.com/user/20439/929afed9-325f-4240-9573-f8408dceb264.png)
**单细胞测序由于variation比较高，所以质量相对bulk rna-seq低。**

# 去除接头-cutadapt

以下为脚本。使用时可以把SRR1033853改为写有sra list的文件
```
for case_name in SRR1033853
do
        fastq_1=${case_name}_1.fastq.gz
        fastq_2=${case_name}_2.fastq.gz

        log_file=${case_name}_cutadapt.log

        out_fastq_1=${case_name}_1_cutadapt.fastq.gz
        out_fastq_2=${case_name}_2_cutadapt.fastq.gz

        nohup cutadapt --times 1 -e 0.1 -O 3 --quality-cutoff 6 -m 75 -a AGATCGGAAGAGC -A AGATCGGAAGAGC -o $out_fastq_1 -p $out_fastq_2 $fastq_1 $fastq_2 > $log_file 2>&1 &

done
```
### 参数：
``--times 1`` ：表示每条序列仅去除一次接头

``-e 0.1`` :接头序列和测序结果比对时，可以允许10%的错误率。

``-O 3`` :接头序列和测序序列至少有3对碱基的overlap,这时当做adapt去除掉

``--quality-cutoff 6（或-q）``：可以在去除接头前对低质量末端（默认为3‘端，因为3’质量较低）的第6个碱基开始进行质量检测和trim。

``-m 50``：去除adapter后，序列长度如果短于50则丢弃（pair ends测序则一对reads全部丢弃）。质量不佳。

``-a AGATCGGAAGAGC``：illumina的常用接头序列，对应read1

``-A AGATCGGAAGAGC``：illumina的常用接头序列，对应read2（因为是双端测序所以有两个）

``-o $out_fastq_1``：输出的read1的文件及位置

``-p $out_fastq_2``：输出的read2的文件及位置

``$fastq_1 $fastq_2``：上一步trim输出得到的read1和read2文件名

``> $log_file`` ：把运行过程中的日志保存在指定的log文件中,便于查看


# trim-fastx_toolkit

```
for case_name in SRR1033853
do
        fastq_1=${case_name}_1_cutadapt.fastq.gz
        fastq_2=${case_name}_2_cutadapt.fastq.gz
        
        out_fastq_1=${case_name}_R1_trim.fq.gz
        out_fastq_2=${case_name}_R2_trim.fq.gz
        
        zcat $fastq_1 | fastx_trimmer -f 6 -l 75 -z -o $out_fastq_1 &
        zcat $fastq_2 | fastx_trimmer -f 6 -l 75 -z -o $out_fastq_2 &

done
```
**在trim的时候注意需要把所有序列都trim到一样长，否则无法进行可变剪切的分析。**

# mapping-tophat2

注意tophat2会调用samtools和bowtie2，所以需要预装这两个软件，使用conda即可安装。但tophat2需要源码编译，****而且需要python2.7环境！！务必注意！****

```
mm10_index=/home/data/refdir/server/reference/index/bowtie/mm10

for case_name in SRR1033853
do
        fastq_1=${case_name}_R1_trim.fq.gz
        fastq_2=${case_name}_R2_trim.fq.gz
        
        output_dir=./${case_name}_tophat_result
        log=./${case_name}_tophat_result/${case_name}_tophat.log
        
        mkdir -p $output_dir
        
        nohup tophat2 -p 32 -o $output_dir --transcriptomeindex=$mm10_index $fastq_1 $fastq_2 >$log 2>&1 &
done
```
这里已经有提前构建好的index，省去了构建索引的步骤。
tophat2会输出一系列文件，所以最好每个样品单独创建dir。


![](https://files.mdnice.com/user/20439/61458a42-3e9f-4e83-8735-3d95045c43ee.png)


这是输出的结果文件。

### 文件内容
accepted_hits.bam： 以BAM文件的形式储存着比对信息，这些比对信息是依据染色体的顺序排列储存的。

align_summary.txt：能够显示比对效率以及多少Read能够多处比对。

junctions.bed： 是以Bed格式储存着发现的剪接位点，每个位点都含有左右两个部分，每个部分长度是Read能够跨越剪接位点匹配到基因组的最远距离，最终的得分是跨越剪接位点的Read数量。（中间有intron）

insertions.bed ：包含着查询到的插入位点信息，chromLeft指的是插入到基因组的位置。

deletions.bed ：包含着查询到的缺失位点信息，chromLeft指的是缺失片段的第一个碱基。

prep_reads.info：需要加工一下才能连续比对到基因组上的reads

unmapped.bam：未能map上去的。有可能是基因组没注释的，要么就是测序有问题等等。


`samtools view -h accepted_hits.bam |less -S`查看文件信息

![](https://files.mdnice.com/user/20439/632b2988-4011-464e-90b6-4acc45c1fab0.png)

上面是头部，下面是reads比对信息

# 去除重复序列-picard
picard中文版说明文档：https://cncbi.github.io/Picard-Manual-CN/building.html#BuildingPicardUtilities
picard是java工具，编译安装的话比较麻烦。直接用conda安装吧

```
conda install -c bioconda/label/cf201901 picard
```
接下来是运行脚本

```
mm10_index=/home/data/refdir/server/reference/index/bowtie/mm10
  
for case_name in SRR1033853
do
        input_bam=./${case_name}_tophat_result/accepted_hits.bam
        out_bam=./${case_name}_tophat_result/accepted_hits_rmdup.bam

        matrix_file=./${case_name}_tophat_result/accepted_hits_rmdup.matrix_file
        log_file=./${case_name}_tophat_result/accepted_hits_rmdup.log

        nohup java -Xms16g -Xmx32g -XX:ParallelGCThreads=32 -jar ~/miniconda3/envs/py27/share/picard-2.18.23-0/picard.jar  MarkDuplicates INPUT=$input_bam OUTPUT=$out_bam METRICS_FILE=$matrix_file ASSUME_SORTED=true REMOVE_DUPLICATES=true >$log_file 2>&1 &

done
```
### 参数解释
-Xms16g -Xmx32g：运行的最小内存和最大内存。如果不限制的话可能会占用所有服务器资源。

ASSUME_SORTED=true：去重原理是把所有序列排序然后查看哪些重复，所以在去重复之前需要先sorted。

METRICS_FILE=$matrix_file：没有实际意义，给出文件名即可。


运行结果如下：
![](https://files.mdnice.com/user/20439/c0f6719a-34c4-48d3-8fe4-f6fa4bbffaa1.png)

其中`accepted_hits_rmdup.bam`是去重复之后的bam文件。可以看到去重去掉了一百多m的体积。

**普通的bulk rnaseq是不需要去重复这一步的，但是scRNAseq由于每种细胞的测序深度较低，pcr过程中过度扩增的可能性较高，故通常都需要去重**

# 计算表达量-cufflink

```
mm10_gtf=~/ncbi_dowloads/refseq/gencode.vM27.annotation.gtf

for case_name in SRR1033853
do
        cufflink_dir=./${case_name}_cufflink_result/
        log=./${case_name}_cufflink_result/cufflink.log
        bam_file=./${case_name}_tophat_result/accepted_hits_rmdup.bam
        
        mkdir -p $cufflink_dir
        nohup cufflinks -o $cufflink_dir -p 16 -G $mm10_gtf $bam_file > $log 2>&1 &
done

```

用R读一下结果
```
# loading data
gene_fpkm_data <- read.table(file = "~/ncbi_dowloads/sra/SRR1033853_cufflink_result/genes.fpkm_tracking",header=TRUE,sep ="\t")
table(gene_fpkm_data$FPKM>0)
```
哭了，怎么感觉跟示例不太一样。。

![](https://files.mdnice.com/user/20439/bac3bf05-6e74-416a-bc50-bb1d72722ba3.png)
不知道哪一步有问题，先这样吧。

这次主要熟悉了一下批量处理的脚本和这几个软件的基本使用。其他等有机会再深入学习吧。

教程来自北大孟浩巍的分享：https://www.bilibili.com/video/BV1Ax411p758

Github相关资料：https://github.com/menghaowei/ngs_learning
