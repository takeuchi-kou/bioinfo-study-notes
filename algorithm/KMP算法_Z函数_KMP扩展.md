# KMP算法

「天勤公开课」KMP算法易懂版：https://www.bilibili.com/video/BV1jb411V78H?from=search&seid=3868126634992789946&spm_id_from=333.337.0.0

## 1. KMP算法是做什么的

KMP算法是用来进行字符串匹配的，它能够快速将模式串匹配到主串中，并且指针不回溯。（即时间复杂度为线性）

与之相对的是 Naive 算法（brute force），这就是最简单暴力的每个字符精确匹配。遇到不匹配的再从头后移一位开始匹配。

## 2.2 KMP操作流程

以下图为例，先从头开始匹配。发现前5个能匹配，而第6个不匹配：

![image-20211221192124607](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221192124607.png)

我们仔细观察匹配上的前5个字符，在模式串中，用白框框出来的部分是前后相同的（不管主串，这里只看模式串），这两个框被称为模式串的公共前后缀；如果模式串中有多个公共前后缀，应当取最长的那一对。

![image-20211221192225628](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221192225628.png)

移动模式串，使得原本的前缀移动到后缀所在的位置，这一步保证公共前后缀里的部分，在模式串与主串之间是匹配的

![image-20211221192346759](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221192346759.png)

继续匹配模式串和主串，发现在第7位出现了不匹配

![image-20211221193956554](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221193956554.png)

此时观察模式串，找到最长公共前后缀

![image-20211221194027482](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221194027482.png)

把前缀移动到后缀位置，发现这时模式串已经超出了主串长度，判定为匹配失败。

![image-20211221194051623](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221194051623.png)

以上为一个完整的KMP匹配过程。

还有一些意外情况，例如：

在寻找公共前后缀时，由于回文序列导致公共前后缀就是匹配的子串本身，这样的话模式串就无法移动了。所以我们在找公告前后缀时还有一个约束条件：公共前后缀的长度必须小于匹配上的子串的长度，例如下图是不行的

![image-20211221194440744](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221194440744.png)

要这样取才行

![image-20211221194517612](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221194517612.png)

## 2.3 转换成计算机语言

由于字符串是储存在数组中的，而数组之间的比较是不能像动画一样移动的，因此我们要转换表述方式。

通过上面的KMP流程演示可以观察到，模式串在“移动”的过程中其实是不需要主串参与的，其“移动”的位置完全由公共前后缀的长度所决定，如下图，当公共前后缀长度为3的时候

![image-20211221195513788](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221195513788.png)

模式串就从4号位开始与主串比较。模式串与主串比较的起始位置总是等于最大公共前后缀长度+1

![image-20211221195432369](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221195432369.png)

把整个数组对应的比较位置全部列出

![image-20211221200050422](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221200050422.png)

只保留位置序号

![image-20211221200112457](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221200112457.png)

把下标与位置信息放在同一个数组中

![image-20211221200139112](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221200139112.png)

这个数组指示的是，在某一位置发现不匹配时，模式串“后移”的长度，代码表示为从第几位开始与主串对比。

这个数组即为next数组。

# 继续KMP数组（代码版），然后是Z算法。

https://www.bilibili.com/video/BV1LK4y1X74N?from=search&seid=5740237619893869905&spm_id_from=333.337.0.0
