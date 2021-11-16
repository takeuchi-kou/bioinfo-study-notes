# 第一篇文章的词云制作

## 1. 利用R爬取文章标题
以下方法参考b站视频：
**R语言期末实践报告——R网络爬虫**（https://www.bilibili.com/video/BV1Db4y1k7oy/?spm_id_from=333.788.recommend_more_video.8）

爬虫其实就是索引HTML标签
在想爬取的页面上按F12在右侧打开网页源代码，点击左上角那个鼠标箭头，然后在左侧页面上选取想爬取的部分，右侧会自动匹配索引位置，然后按照标签一级一级找就可以了。

![](https://files.mdnice.com/user/20439/9be12ac4-8288-458f-9e55-7ae814289781.png)

```
rm(list = ls())
setwd("~/homework/6th")

install.packages('selectr')
install.packages('xml2')
install.packages('rvest')

#加载包：
library(xml2) 
library(rvest) 
library(stringr)

#指定要爬取的网页
url <- "Welcome to the Pan-Cancer Atlas.html"
#从页面读取HTML内容
webpage <- read_html(url)

##scrape title of the product
title_html <- html_nodes(webpage, 'h1#title')
itle <- html_text(title_html)
head(title)

#获取当前页面div class=InlineHTML的所有内容
inline = html_nodes(webpage, xpath='//div[@class = "InlineHTML"]')

library("dplyr")
#逐条爬取，注意此处索引用的是alt，而爬取内容用的是attribute【alt】的值，而非文本`html_text()
title_articles <- html_nodes(inline, xpath='.//ul//li/a[@alt]') %>% html_attr('alt')

```
因为从服务器直接连接待爬取页面url（https://www.cell.com/pb-assets/consortium/pancanceratlas/pancani3/index.html#group-resources-Cs76RnLm9R）失败，所以先下载网页到本地后上传至服务器工作目录再操作。

结果以字符串向量形式储存在`title_article`里面
![](https://files.mdnice.com/user/20439/31545d6e-74dd-482a-8bb9-3c4044f2aad2.png)

接下来可以下一步操作

## 2. 制作词云

（具体可参考作业5内容）
```
##制作词云
# Load
library("tm")
library("SnowballC")
library("wordcloud")
library("RColorBrewer")
# Load the data as a corpus
docs <- Corpus(VectorSource(title_articles))
# 检查文档内容
inspect(docs)


#统一小写
docs <- tm_map(docs, content_transformer(tolower))
# Remove numbers
docs <- tm_map(docs, removeNumbers)
#替换“：” “，”为空格：
toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
docs <- tm_map(docs, toSpace, ", ")
docs <- tm_map(docs, toSpace, ": ")
#同义词替换
toStr <- content_transformer(function(x, from, to) gsub(from, to, x))
docs <- tm_map(docs, toStr, "the cancer genome atlas", "tcga")
#Text stemming
docs <- tm_map(docs, stemDocument)
# Remove english common stopwords
docs <- tm_map(docs, removeWords, stopwords("english"))


#制作词频矩阵
dtm <- TermDocumentMatrix(docs)
m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
head(d, 15)

#画图
library('wordcloud2')
wordcloud2(d,size = 1, minRotation = -pi/6, maxRotation = -pi/6,rotateRatio = 1)
```

结果：
![](https://files.mdnice.com/user/20439/6af89e04-bb48-43d3-a61b-39b04bf95dec.png)

# 第二篇文章

跟上篇操作一样，直接放代码了

```
##第二篇文章

#指定要爬取的网页
url2 <- 'Pan-Cancer Analysis of Whole Genomes.html'
#从页面读取HTML内容
webpage2 <- read_html(url2)

#获取当前页面div class=InlineHTML的所有内容
title_abstract = html_nodes(webpage2, xpath='//h3[@class="c-article-item__title u-serif"]')

#逐条爬取
titles2 <- html_nodes(title_abstract, xpath='.//a/articletitle') %>% html_text()
titles2


# Load the data as a corpus
docs <- Corpus(VectorSource(titles2))
# 检查文档内容
inspect(docs)

#统一小写
docs <- tm_map(docs, content_transformer(tolower))
# Remove numbers
docs <- tm_map(docs, removeNumbers)
#替换“：” “，”为空格：
toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
docs <- tm_map(docs, toSpace, ", ")
docs <- tm_map(docs, toSpace, ": ")
#Text stemming
docs <- tm_map(docs, stemDocument)

#同义词替换
toStr <- content_transformer(function(x, from, to) gsub(from, to, x))
docs <- tm_map(docs, toStr, "human-canc", "human-cancer")
docs <- tm_map(docs, toStr, "human cancer", "human-cancer")
docs <- tm_map(docs, toStr, "whole genom", "whole-genome")

# Remove english common stopwords
docs <- tm_map(docs, removeWords, stopwords("english"))


#制作词频矩阵
dtm <- TermDocumentMatrix(docs)
m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
head(d, 15)

library('wordcloud2')
wordcloud2(d,size = 0.8, minRotation = -pi/6, maxRotation = -pi/6,rotateRatio = 1)

```
结果
![](https://files.mdnice.com/user/20439/072f4c99-55c7-4670-9d98-e8a2bca621e8.png)


