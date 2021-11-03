## 作业要求
本次[作业题目](https://mp.weixin.qq.com/s/WVRHrTL2UnrWttEyLGkhVw)展示的是单细胞测序结果的降维聚类，示例中使用的方法是t-SNE和UMAP.

> ### 作业
>
> 去 gencode数据库拿到最新的人类的gtf文件，仅仅是挑选蛋白编码基因即可，约2万个，然后把基因名字按照字母顺序排好，取前面的三分之一，对它进行一些基因分布特征的检验，比如是否集中于某条染色体，或者其它一切你能想到的检验。
>


## Gencode数据库
用wget从ftp站点下载数据：http://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/
下载`gencode.v38.annotation.gtf.gz`这个文件
下载官方提供的gtf解析工具：https://github.com/openvax/gtfparse

### gtf文件的解析和基因筛选（官方python模块的使用记录）

折腾了一天Python，真是学到用时方很少！以前为啥不挤时间复习以下基本操作呢
吭哧吭哧学了一天半，终于用上了官方轮子。。惨。记一下使用这个python包的流程吧

首先`git clone`上面那个包，打开以后可以看到里面有一个`setup.py`

py文件的运行方法是在terminal输入`python setup.py`,然后会提示需要参数

按照提示输入`python setup.py install`这样就会把`gtfparse`安装到当前目录。（其实不应该在当前目录安装，但是后期可以移动）

然后是配置python环境

在pycharm里面选择新建一个命名为gtfparse的环境，注意python版本需要为3.4

在Python中输入`import sys`以及`sys.path`得到当前搜索r包的系统路径

把gtfparse包移动到这些路径中的一个里面，也可以用`sys.path.append`添加指定路径

把readme里面的内容复制到新的scratch里面，运行第一行代码，会提示安装很多包，注意当前环境需要选定为gtfparse，然后在terminal里一个一个安装即可。

但是import一直报错，显示module是uncallable的。原因是有重名文件夹（环境名与模块名重名），所以在import的时候不可以直接用`import xxx from yyy`,而是要用`import xxx.yyy from yyy`.

然后是读取问题，虽然用了完整路径但是一直显示不存在文件。解决方法是获取当前project的绝对路径，然后把需要读取的文件拖动到这个路径里面再读取

```
#获取project的绝对路径，然后把要读取的文件拖动到这个路径下
import os
print (os.path.abspath('.'))

#导入模块
from gtfparse.read_gtf import read_gtf

# 读取gtf文件
df = read_gtf("gencode.gtf")

# 筛选数据
df_genes = df[df["feature"] == "gene"]
df_prot_cod = df_genes[df_genes["gene_type"] == "protein_coding"]
```

以下为读取结果：

![](https://files.mdnice.com/user/20439/b4de3d49-18ec-4560-a083-076efcbeca7e.png)


确实是2w个左右

python做data science初体验到此结束吧，两眼一抹黑效率实在太低了，还不如一开始就用R。还是赶紧导出csv然后用熟悉的R进行下面的分析吧。

```
df_prot_cod.to_csv("/home/erica/Desktop/homework/8th/protein_coding.csv")
```

### 用R处理解析得到的数据

```
setwd("~/Desktop/homework/8th")
library("data.table")
library("dplyr")
library('ggplot2')

genes <- fread("protein_coding.csv")

```

看一下gtfparse把文件分解成了哪些格式

![](https://files.mdnice.com/user/20439/d4d8d8e0-5c3d-42a8-a011-8ead19825c1c.png)

清理一下数据（主要是NA值）

```R
#inspect of na pattern
gene.md <- md.pattern(genes)

##data cleaning and mutation
genes <- genes %>% 
  top_n(n = 6000, wt = gene_name) %>% 
  mutate(length = end-start) %>%
  select_if(~!any(is.na(.))) %>%  #delete col including na
  select(-V1)
```

mice这个包是用来查看和处理缺失值的，md.pattern可以查看缺失值的模式。两行数据，第一行1代表无缺失值，0代表有缺失值。可以看到前面的变量全部没有缺失，后面全都没有数据，这是要因为我们选取的是基因而非转录本。可以直接去除有缺失的列。

![](https://files.mdnice.com/user/20439/2f860241-52d6-4b78-b659-b679e108c350.png)


数据清理和变形

```
##data cleaning and mutation
genes <- genes %>% 
  top_n(n = 6000, wt = gene_name) %>%  #use top 1/3
  mutate(length = end-start) %>%
  select_if(~!any(is.na(.))) %>%  #delete col including na
  select(-V1)
```

把观测变量分成数值型和字符型，其实这步不做也可以，写的时候没仔细看数据，以为可能发现什么。结果没啥用。

```
#split attributes into character and integer 
class(genes$hgnc_id)
class(genes$start)
int_attr <- genes %>% 
  select(which(lapply(genes,class) == "integer"))
char_attr <- genes %>% 
  select(which(lapply(genes,class) == "character"))

```

下面这步是想看看字符类观测变量有没有有意义的因子，结果发现不是每个都一样就是每个都不一样。

```
##inspect of character variable
tb_char <- lapply(char_attr[,seqname:tag],table)
len_tbc <- lapply(tb_char[1:10],length)
```

数量适中的只有seqname和tag。tag里的变量很麻烦，有一个位置写两个标签的，所以没有深究。最后只分析了seqname

```
##seqname bar plot  
seqname_barplot <- char_attr %>% group_by(seqname) %>% count() %>%
  ggplot(aes(x=reorder(seqname,-n),y=n)) +
    geom_col(fill = 'lightblue') +
    theme_minimal() +
    geom_text(mapping = aes(label = n))
seqname_barplot
```

![](https://files.mdnice.com/user/20439/c9100c19-441e-4b89-9e57-436a5321154c.png)

数值型观测变量有意义的只有start和end，另外还有一个相减算出的length

```
#continuouse data plot histogram & ECDF
##start place
genestart_plot <- ggplot(int_attr,aes(x = start/10000000)) +
  geom_histogram(bins = 20, fill = 'pink', color = 'grey') +
  scale_x_continuous(breaks = seq(0, 26, by = 4)) +
  theme_minimal()
genestart_plot
```

![](https://files.mdnice.com/user/20439/21faa722-20a6-4350-b9ed-bc87a4ee7e5d.png)

```

##gene length
length_hist <- ggplot(int_attr,aes(x = length/10000)) +
  geom_histogram(binwidth = 1,fill='lightblue', color = 'white',alpha=0.9) +
  scale_x_continuous(limits = c(0, 15), breaks = seq(0, 15, by = 1)) +
  theme_bw()
length_hist

length_ecdf <- ggplot(genes,aes(length)) +
  stat_ecdf(geom = 'line') +
  xlim(0,150000) +
  scale_y_continuous(breaks = seq(0, 1, by = 0.2)) +
  geom_line(y=0.8 ,linetype = 'dotted', color = 'red')
length_ecdf
```


![](https://files.mdnice.com/user/20439/6535e73c-e2a7-46dd-bfcd-498a9cc8fb80.png)


![](https://files.mdnice.com/user/20439/5222b5a9-aba1-4968-961a-9a97683625e9.png)



