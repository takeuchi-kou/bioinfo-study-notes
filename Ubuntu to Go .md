
# （一） 组合出需要的数据

解析xml文件得到 研究gene的id/文章pmid/发表年份 的对应关系

详见文末，这里直接读取大佬解析的文件
```
rm(list=ls())
setwd("~/homework/7th")

##筛选出人类的GENE ID和对应的Pubmed ID
library(data.table)
library('R.utils')
#fread函数可以快速读取大数据表格
a=fread('gene2pubmed.gz',data.table = F)  
a=a[a$`#tax_id`==9606,]
head(a)
#Homo_GeneID_PubMed_ID_year.txt是解析xml得到的结果与gene2pubmed合并后的文件
gid_pmid_year <- fread('Homo_GeneID_PubMed_ID_year.txt', data.table = F)
head(gid_pmid_year)
table(gid_pmid_year$`#tax_id`)
#去掉tax_id
gid_pmid_year <- gid_pmid_year[,-1]

```
查看读取后的文件格式：
![](https://files.mdnice.com/user/20439/b28b45e9-2c8f-4cc3-b7e4-b272bb835a4c.png)

从数据库中获取gene id-symbol对应关系
```
library(dplyr)
library(org.Hs.eg.db)
ids=toTable(org.Hs.egSYMBOL)
head(ids)
colnames(ids)[1] <- c('GeneID')

```
结果
![](https://files.mdnice.com/user/20439/fc4a4f1e-258a-40ca-a0fd-562848cd8da4.png)
把id-symbol信息合并到之前的数据集中
```
gid_pmid_year$GeneID <- as.character(gid_pmid_year$GeneID)  
symbol_id_year_all <- gid_pmid_year %>% left_join(ids, by = c('GeneID'))
head(symbol_id_year_all)
```
得到四列数据的对应关系。
这是本次作业所需要的数据集，后面都是从这个数据框里筛选需要的信息
![](https://files.mdnice.com/user/20439/4061ab6a-923e-42d6-a3ef-8e3dfccfc99c.png)
统计每年研究TP53这个基因的文章数目（这步是为了对答案）
```
TP53_all <- symbol_id_year_all[symbol_id_year_all$symbol=='TP53', ]
head(TP53_all)
tp53_year_tb <- table(TP53_all$year)
tp53_year_tb

```
得到：
![](https://files.mdnice.com/user/20439/c06dc09d-b009-4a73-ba44-eca59ffebb6a.png)

# （二） 根据要求分析数据

以折线图表现最热门的10个基因在最近30年的热度（每年文章发表数）变化情况

找出最热门的10个gene symbol
```
top10_symbol_count <- symbol_id_year_all %>% 
  group_by(symbol) %>% 
  count() %>% arrange(desc(n)) %>% 
  ungroup() %>% top_n(10)
top10_symbol_count
top10_symbol <- top10_symbol_count$symbol
```
结果如下
![](https://files.mdnice.com/user/20439/7d570212-6dc1-4695-9f45-d1588c9dc100.png)
统计每个基因在每年的发文数量
```
symbol_year_count <- symbol_id_year_all %>% 
  group_by(symbol,year) %>% 
  summarise(count=n()) %>% 
  arrange(desc(count)) %>%
  dplyr::filter(year != 2021)
symbol_year_count
```
按降序排列结果如下
![](https://files.mdnice.com/user/20439/326e8495-1998-471d-8fe0-9ff419827ae0.png)

分别统计top10热门基因在最近30年每年的发文数量
### 方法一，单个统计和绘制
```
top_names <- 0
for(i in 1:10){
  name <- paste(top10_symbol[i],c('_count'),sep = "")
  data <-  symbol_year_count %>% 
    dplyr::filter(symbol==top10_symbol[i]) %>% 
    arrange(desc(year)) %>%
    top_n(20) %>%
    arrange()
  assign(name,data)
  name
  i <- i+1
}
```
这样会出现最近20年top10热门基因每年的发文数量会产生10个数据框，每个数据框如下：

![](https://files.mdnice.com/user/20439/e0401e39-a349-453e-a740-63f918d9fd04.png)

这样一个一个绘图稍显麻烦，用dplyr配合ggplot2的话会方便很多

### 方法二，同时绘制top10
把top10的数据放在同一张表上，其实它们本来就在一张表上只需要筛选最近二十年以及top10热门基因即可
```
count_10 <- symbol_year_count %>% filter(symbol %in% top10_symbol & year > 2000) 
head(count_10)
table(count_10$symbol)
table(count_10$year)
```
筛选结果
![](https://files.mdnice.com/user/20439/5551c5eb-ef24-4426-8e68-7397f9c411de.png)

将数据绘制在同一张图上
```
library(ggplot2)
plot_all <- ggplot(data=count_10,aes(x=year, y=count, group=symbol, color=symbol))+
  geom_line()
plot_all
```
因为1990-2000差别不明显，就选取最近20年的这样差异清楚一些
![](https://files.mdnice.com/user/20439/e39eee0b-f852-4400-9ba1-c1bd68acb5cc.png)

每个基因分别绘图
```
plot_facet <- ggplot(data=count_10, aes(x=year, y=count, group=symbol))+
  facet_wrap(~ symbol)+
  geom_line()
plot_facet
```

![](https://files.mdnice.com/user/20439/685ff42a-2f51-436e-b6a3-8d8c77c21816.png)

参考：
大佬 @小灰侠 作业 https://blog.csdn.net/coding_Joash/article/details/120579109
doplyr数据清洗 https://zhuanlan.zhihu.com/p/160796699
ggplot2分面绘图 https://blog.csdn.net/weixin_39587113/article/details/109920044


## 附：用xml包解析xml数据

自己学习了一下，以下是学习代码。
主要过程总结一下就是：
1. 先看完整的最小结构单元是什么。
2. 然后看如何在这个单元中索引到想要的数据。
3. 最后用for循环遍历所有最小单元。
```
library('XML')
pub_1062 <- xmlParse('pubmed21n1062.xml.gz')
class(pub_1062)
#形成根目录列表数据
xmltop = xmlRoot(pub_1062) 
class(xmltop) #查看类

#此处仅为浏览，无实际用处
xmlName(xmltop) #查看根目录名
xmlSize(xmltop) #查看根目录总数
xmlName(xmltop[[1]]) #查看子目录名
xmltop[[1]] #查看第一个子目录
#子目录节点
xmlSize(xmltop[[1]]) #子目录节点数
xmlSApply(xmltop[[1]], xmlName) #子目录节点名
xmlSApply(xmltop[[1]], xmlAttrs) #子目录节点属性
xmlSApply(xmltop[[1]], xmlSize) #子目录节点大小


#查看第一个子目录的第一个节点
list1 <- xmlToList(xmltop[[1]][[1]])   
list1$Article$Journal$JournalIssue$PubDate$Year
list1$PMID$text
is.atomic(list1$Article$Journal$JournalIssue$PubDate$Year)
is.atomic(list1$PMID$text)

pmid <- 1
year <- 1
n <- 0
for (i in 1:c(xmlSize(xmltop)-1)) {
  n=n+1
  MedlineCitation <- xmltop[[i]][[1]]
  list_medline <- xmlToList(MedlineCitation)
  pmid[n] <- list_medline$PMID$text
  pubdate=list_medline$Article$ArticleDate$Year
  if(!is.null(pubdate)){
    year[n] <- pubdate
  }else{year[n] <- 0}
}

class(pmid)
class(year)
id_year <- data.frame(PubMed_ID=pmid, YEAR=year)
```
得到id_year以后，与gene2pubmed合并一下就得到了之前读取的那个数据文件。
