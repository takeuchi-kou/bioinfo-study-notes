# EDirect 的使用

assignment1 ：https://github.com/takeuchi-kou/ComputationalGenomicsManual/blob/master/Databases/NCBI_Edirect.md

Edirect 的官方文档：https://www.ncbi.nlm.nih.gov/books/NBK179288/

以下这段是官方文档中关于EDirect的介绍部分：

> Entrez Direct (EDirect) provides access to the NCBI's suite of interconnected databases (publication, sequence, structure, gene, variation, expression, etc.) from a Unix terminal window. Search terms are entered as command-line arguments. Individual operations are connected with Unix pipes to construct multi-step queries. Selected records can then be retrieved in a variety of formats.

简单说，Edirect是entrez提供的访问NCBI数据库的命令行工具，其便利之处在于可以使用管道命令按照需求去构造搜索命令以及下载相应文件。

下图为EDdirect的workflow。

![image-20220118185140560](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220118185140560.png)

图片来自YouTube上NCBI官方发布的视频：NCBI Minute: Using EDirect to Query a Local Installation of PubMed https://www.youtube.com/watch?v=GcZzrljmPZI

workflow里展示了该工具的一些功能，其中最核心的两个功能为 esearch和efetch。

- esearch：从NCBI检索符合条件的记录，并将结果的summary返回client的屏幕上，并没有下载结果，其参数包括`-db`（指定数据库）和`-query`（指定查询条件）两部分。
- efetch：执行结果的下载，可使用参数 `-format`指定结果格式，如xml等。

另外，xtract也是非常强大的工具，可以直接解析xml格式，但是linux命令行不知道能不能用，本篇不涉及。*(其实是因为懒)*

## 安装EDirect工具

```
# 创建并进入目录
cd ~/ncbi_download
mkdir EDirect
cd EDirect

# 获取entrez direct工具包
curl ftp://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/edirect.zip -O
# 解压
unzip edirect.zip
# 查看解压目录
cd edirect
ll

# 配置环境变量
echo "export PATH=\$PATH:\$HOME/ncbi_dowloads/EDirect/edirect" >> $HOME/.bashrc
source ~/.bashrc

# 查看新工具功能
esearch -help
efetch -help
```

help给出了一些使用示范，我们现在可以开始使用新工具了~

一些便利的EDirect用法：https://github.com/NCBI-Hackathons/EDirectCookbook

## 利用EDirect下载基因组数据

### 用esearch搜索指定数据库

例如，我们要在assembly数据库中搜索某一物种，可输入命令

```
esearch -db assembly -query "Faecalibacterium prausnitzii[ORGN]"
```

其中 `-db` 指定了在assembly数据库中进行搜索；

`-query`指定搜索内容，用`“ ”`括起来的部分是用于搜索的关键字，用`[]`括起来的部分指定关键字所属的fields。

不知道怎么翻译field，它指的是下面左边这些，右边是对应的含义

![image-20220118204636833](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220118204636833.png)

在命令行输入上面的语句后，出现以下结果

![image-20220118204854949](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220118204854949.png)

可以看到是XML格式，其中关键的信息是倒数第三行`<Count>49</Count>`，显示的是有多少条查询结果，相当于NICBI的网页中输入

![image-20220118205514791](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220118205514791.png)

得到结果

![image-20220118205547142](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220118205547142.png)

用efetch看看其汇总文档

```
esearch -db assembly -query "Faecalibacterium prausnitzii[ORGN]" | efetch -format docsum | less
```

报错

```
Unable to locate transmute executable. Please execute the following:

  nquire -dwn ftp.ncbi.nlm.nih.gov entrez/entrezdirect transmute.Linux.gz
  gunzip -f transmute.Linux.gz
  chmod +x transmute.Linux

```

按照提示解压和下载相应的工具，之后用awk解析获取我们所需要下载的文件名，用curl进行下载。

```
# 作业提供的脚本
esearch -db assembly -query "Faecalibacterium prausnitzii[ORGN]" | efetch -format docsum | \
xtract -pattern DocumentSummary -element FtpPath_RefSeq | \
awk -F"/" '{print "curl -o "$NF"_genomic.fna.gz " $0"/"$NF"_genomic.fna.gz"}'
```

用作业里这段代码没下载下来，试试单独下载一条。

```
curl ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/019/968/015/GCF_019968015.1_ASM1996801v1/GCF_019968015.1_ASM1996801v1_genomic.fna.gz -O
```

成功了，看来是脚本的问题。脚本问题下次研究吧。。*(我不会)*

![image-20220119194224899](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220119194224899.png)

---

参考：

作业网址：https://github.com/takeuchi-kou/ComputationalGenomicsManual/blob/master/Databases/NCBI_Edirect.md

其他博客：

https://blog.csdn.net/zhanyongjia_cnu/article/details/50717717?spm=1001.2101.3001.6650.19&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-19.pc_relevant_paycolumn_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-19.pc_relevant_paycolumn_v2&utm_relevant_index=26

https://www.cnblogs.com/lmt921108/p/8087474.html

https://blog.csdn.net/sunchengquan/article/details/78586870

https://www.plob.org/article/11362.html