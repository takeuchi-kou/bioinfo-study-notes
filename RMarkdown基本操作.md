# 1. 新建rmd文件

点击新建文件，选择R Markdown。这次选择输出格式为HTML。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122212147944.png)

自动创建的rmd文件内容形式如下

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122212534589.png)

点击knit输出为HTML。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122212606179.png)

把文件保存在本地位置。点开后查看内容

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122212828869.png)

可以看到，文件中包含了**格式化的头文字、title、text、R代码、table、plot**等内容，在text中可以包含外链url。

这是rmd提供给我们的一个模板，接下来修改模板内容，使之符合我们的需求。本次用R内置的Iris数据集进行测试。

# 2. 头部文件YAML

rmd开头的这部分就是头文件YAML。它本身是一种格式化的语言，这里可以用于配置rmd。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122213942242.png)

修改为：

```{R}
---
title: "R Markdown 练习"
author: "Joy_theStrayDog"
date: "2021/11/22"
output: 
  html_document:
    toc: yes
---
```

重新点击knit，rstudio会自动在窗口更新生成的HTML。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122214422250.png)

可以看到相应内容已经进行了修改，并且增加了可跳转的标题目录，这是因为我们在YAML中增加了一行`toc: yes`（toc: Table of Contents)。另外要注意遵守YAML的格式，注意换行和缩进。

# 3. Markdown文本

表现为title和text部分：

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122215726930.png)

这部分与markdown语法规则基本相同，url外链形式为[title]< {url} >，其他不予详述。

# 4. 代码块

同样用三个反引号表示代码块：

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122220437798.png)

这里第一个反引号后面{  }中逗号前的内容`r pressure`，可以用于选择代码语言和起到注释代码内容的作用，逗号后用于参数设置。例如`echo=FALSE`意思是仅输出图不输出表，`warning=FALSE`代表不输出警告信息。

rmd支持的语法包括以下几种，直接点击相应语言即可生成符合该语法的代码块。

![image-20211122221450480](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211122221450480.png)









参考视频教程：<https://www.cnblogs.com/purple5252/p/14699033.html>



