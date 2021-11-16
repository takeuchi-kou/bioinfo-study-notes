制作词云的步骤参考：[Text mining and word cloud fundamentals in R : 5 simple steps you should know](http://www.sthda.com/english/wiki/text-mining-and-word-cloud-fundamentals-in-r-5-simple-steps-you-should-know)

- Step 1: Create a text file
- Step 2 : Install and load the required packages
- Step 3 : Text mining
- Step 4 : Build a term-document matrix
- Step 5 : Generate the Word cloud

# Step 1: Create a text file
把[作业中的45篇文章标题](https://mp.weixin.qq.com/s/MGRIJAcSsePtMdLD6hwDwQ)放到txt文档中,上传到R的服务器
# Step 2 : Install and load the required packages
```
setwd("~/homework/5th")
# Install
install.packages("tm")  # for text mining
install.packages("SnowballC") # for text stemming
install.packages("wordcloud") # word-cloud generator 
install.packages("RColorBrewer") # color palettes
# Load
library("tm")
library("SnowballC")
library("wordcloud")
library("RColorBrewer")
```
安装报错

![](https://files.mdnice.com/user/20439/d8aa42d5-45d7-44cb-af7f-4fb4d09e3d59.png)

尝试在shell的R中安装，继续报错
```
> install.packages("tm")
--- Please select a CRAN mirror for use in this session ---
Error in structure(.External(.C_dotTclObjv, objv), class = "tclObj") : 
  [tcl] unknown color name "blue".
> install.packages("slam")
--- Please select a CRAN mirror for use in this session ---
Error in structure(.External(.C_dotTclObjv, objv), class = "tclObj") : 
  [tcl] unknown color name "blue".
```
参考https://blog.csdn.net/zhengxinchang1993/article/details/101372154的解决方案,应该是因为服务器的颜色显示有问题，所以禁止窗口调用
`chooseCRANmirror(graphics=FALSE)`，选择中国的mirror（18）然后再安装，显示成功。另外三个安装包一并在shell R中源码安装。

# Step 3 : Text mining

## 加载文本
使用文本挖掘(tm) 包中的Corpus()函数加载文本。语料库是一个文档列表（在我们的例子中，我们只有一个文档）。

```
#使用交互读入文件
text <- readLines(file.choose()) 
# 如果是链接的话，在函数中输入网址
filePath <- "http://www.sthda.com/sthda/RDoc/example-files/martin-luther-king-i-have-a-dream-speech.txt"
text <- readLines(filePath)

# Load the data as a corpus
docs <- Corpus(VectorSource(text))

# 检查文档内容
inspect(docs)
```

## 清理文本

tm包自带了5种变形函数
```
> getTransformations()
[1] "removeNumbers"     "removePunctuation" "removeWords"       "stemDocument"     
[5] "stripWhitespace"  
```

`removeNumbers`	:去除所有数字

`removePuncuation`	:去除所有标点符号

`removeWords`	:去除指定文字，文字需要自定义，也可以使用自带函数`stopwords()`

`stemDocument`	:提取单词词干

`stripWhitespace`	:去除多余空格

以上五种变形方式可以直接用 tm_map() 应用到语料库中去，如：
``> docs <- tm_map(docs, stemDocument)``

如果被调用的变形函数有参数，在被 tm_map() 调用时，参数紧跟在变形函数后面，如：
``> docs <- tm_map(docs, removeWords, c("can", "finally"))``

2.2 非tm自带的变形函数
如果需要使用tm包以外的函数，或者自定义函数，这些函数需要用 content_transformer() “包装”起来，r然后被 tm_map() 调用。如：
```
#使用tm_map()函数执行转换以替换文本中的特殊字符等。
#替换“/” “@”和“|” 为空格：
toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
docs <- tm_map(docs, toSpace, "/")
docs <- tm_map(docs, toSpace, "@")
docs <- tm_map(docs, toSpace, "\\|")
```
`gsub(pattern, replacement, x,)`函数的作用是替换字符串.其中pattern是需要替换的字符，replacement是替换后的字符。
字符替换的通配符可参考：https://blog.csdn.net/lztttao/article/details/82086346

tm_map() 也可以实现多参数的变形：
```> toStr <- content_transformer(function(x, from, to) gsub(from, to, x))
> docs <- tm_map(docs, toStr, "directory", "folder")
> docs <- tm_map(docs, toStr, "topics", "subject")
```

这部分参考了：https://blog.csdn.net/stat_elliott/article/details/42458487/
以下照搬教程代码
```
# Convert the text to lower case
docs <- tm_map(docs, content_transformer(tolower))
# Remove numbers
docs <- tm_map(docs, removeNumbers)
# Remove english common stopwords
docs <- tm_map(docs, removeWords, stopwords("english"))
# Remove your own stop word
# specify your stopwords as a character vector
docs <- tm_map(docs, removeWords, c("blabla1", "blabla2")) 
# Remove punctuations
docs <- tm_map(docs, removePunctuation)
# Eliminate extra white spaces
docs <- tm_map(docs, stripWhitespace)
# Text stemming
# docs <- tm_map(docs, stemDocument)
```

# Step 4 : Build a term-document matrix

```
dtm <- TermDocumentMatrix(docs)
m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
head(d, 10)
```
产生了奇怪的词频矩阵。。学术论文大概不应该与日常语言用一样的处理方法。
```
                       word freq
mir                     mir   17
cellspdf           cellspdf   16
cancer               cancer   13
cervical           cervical    9
cell                   cell    9
transition       transition    8
pdf                     pdf    7
proliferation proliferation    6
contributes     contributes    6
regulates         regulates    6
```
重新构建document，观察一下该文本特点，然后重新处理一遍
```
# Load the data as a corpus
docs <- Corpus(VectorSource(text))
inspect(docs)

#去掉后缀.pdf
docs <- tm_map(docs, removeWords, c(".pdf")) 
#统一小写
docs <- tm_map(docs, content_transformer(tolower))
# Remove numbers
docs <- tm_map(docs, removeNumbers)
# Remove punctuations
docs <- tm_map(docs, removePunctuation)
#同义词替换
toStr <- content_transformer(function(x, from, to) gsub(from, to, x))
docs <- tm_map(docs, toStr, "mir\\w* ", "micro-RNA ")
docs <- tm_map(docs, toStr, "microrna* ", "micro-RNA ")

#Text stemming
docs <- tm_map(docs, stemDocument)
# Remove english common stopwords
docs <- tm_map(docs, removeWords, stopwords("english"))
```
看起来好多了

```
               word freq
micro-rna micro-rna   34
cell           cell   25
cancer       cancer   16
cervic       cervic    9
transit     transit    8
contribut contribut    8
upregul     upregul    8
promot       promot    8
carcinoma carcinoma    7
prolifer   prolifer    7
```

# Step 5 : Generate the Word cloud

用以下代码生成词云
```
set.seed(1234)
wordcloud(words = d$word, freq = d$freq, min.freq = 1, 
          max.words=1000, random.order=FALSE, rot.per=0.35, 
          colors=brewer.pal(8, "Dark2"))
```
 报错
 
```
Warning message:
In wordcloud(words = d$word, freq = d$freq, min.freq = 1, scale = c(2,  :
  epithelial–mesenchym could not be fit on page. It will not be plotted.
```
查询wordcloud各参数含义
（参考https://blog.csdn.net/hadoopdevelop/article/details/79282881）

函数原型
```
 wordcloud(words,freq,scale=c(4,.5),
     min.freq=3,max.words=Inf,random.order=TRUE, 
     random.color=FALSE, rot.per=.1,colors="black",
     ordered.colors=FALSE,use.r.layout=FALSE,...)
 ```

常用参数

（1）words——关键词列表

（2）freq——关键词对应的词频列表

（3）scale——字号列表。c(最大字号, 最小字号)

（4）min.freq——最小限制频数。低于此频数的关键词将不会被显示。

（5）max.words——限制词云图上关键词的数量。最后出现在词云图上的关键词数量不超过此限制。

（6）random.order——控制关键词在图上的排列顺序。T：关键词随机排列；F：关键词按频数从图中心位置往外降序排列，即频数大的词出现在中心位置。

（7）random.color——控制关键词的字体颜色。T：字体颜色随机分配；F：根据频数分配字体颜色。

（8）rot.per——控制关键词摆放角度。T：水平摆放；F：旋转90度。

（9）colors——字体颜色列表

（10）ordered.colors——控制字体颜色使用顺序。T：按照指定的顺序给出每个关键词字体颜色，（似乎是要求颜色列表中每个颜色一一对应关键词列表）；F：任意给出字体颜色。

```
wordcloud(words = d$word, freq = d$freq, min.freq = 1,scale=c(2,.5),
          max.words=1000, random.order=FALSE, rot.per=0.35, 
          colors=brewer.pal(8, "Dark2"))
```

![](https://files.mdnice.com/user/20439/13d1a18d-0ef6-4be8-abf6-a7f3e14cfdef.png)
效果好丑

试试wordcloud2

```
library("wordcloud2")
wordcloud2(d, size = 0.8,shape = 'cardioid')
```
结果

![](https://files.mdnice.com/user/20439/581e66c9-d78e-40fd-84d3-9ed230cbc762.png)
字体相差太大了，这个wordcloud2好像没有wordcloud那个定义最大字号和最小字号的功能。手动log一下再做

```
library("dplyr")
d %>% mutate(freq = log2(freq)) -> d2
wordcloud2(d2, size = 0.5, shape = 'cardioid')
```

![](https://files.mdnice.com/user/20439/1077bd92-f861-4ef4-b482-5a24da554513.png)






