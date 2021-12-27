# 1. Indexing and k-mer indexes

## preprocessing

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226175412429.png)

根据对于T串是否进行预处理，可以把算法分为online 和 offline，如上图。一般来说，需要反复利用T串的情况下，offline算法是更为合适的。

## Index

### ordering：书籍

关于T串的preprocessing，以书籍打比方，如果我们想快速寻找书籍中的某个内容，制作目录(index)是一个好方法。其中目录中的条目(key term)以字母表顺序排列，每个key后面都对应着书籍的相应章节。我们把搜寻key的过程 称为(query the key)

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226180111984.png)

### grouping：零售店

在零售店里买东西的时候，我们很容易找到要买的物品，这是因为零售店里的货物都是分类(group)摆放的。

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226180240207.png)

## Indexing DNA：k-mer

在DNA的index过程中，我们主要使用ordering的方法。

由于DNA是一长串序列，本身没有特定的主题区分，也没有章节/页的概念；所以我们需要手动把序列分成固定长度，按照顺序去索引。

- the offset order

首先我们把所有含有T的长度为5的片段列出，并且**按顺序**记下这个片段第一个字符出现的位置，如图

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226181207406.png)

- 首字母重复的情形

目前我们首字母排列的顺序为【C G T】，所以当找到下一个长度为5的片段的时候，由于它的首字母与之前的重复， 所以不能放到最后，而是要按照已有的首字母顺序进行排列，如图：

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226181426266.png)

- 片段序列重复的情形

在出现与之前序列相同的片段的时候，只需把再次出现的位置写在后面即可，而非重新列出

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226181657361.png)

按照这些规则继续写出剩下片段的目录

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226181823969.png)

这种索引DNA的方法叫做k-mer，由于我们把序列打断为长度为5的片段，所以刚刚的操作叫做5-mer index。

## Querying the index

现在我们已经用5-mer的方法处理完T串了，可以开始对P串和T串进行匹配。

假设P串有6个字符，其中前五个我们可以用exact naive matching 非常迅速地在T中找到匹配的序列(因为T已经按照首字母索引过了)，在本例中，P串前五位对应的是T串index里offset=3的片段

![](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226183756989.png)

在确认完成前5位的offset后，我们还需要确认(verification)最后一位是否match，本例中字母C成功匹配。

### 2nd 5-mer

前面我们使用的是P串中前5位作为5-mer去匹配index，其实我们也可以使用后五位，过程是一样的：

![image-20211226184159811](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226184159811.png)

图中我们找到2个offset，但是在进行verification的时候，0位的T串不能匹配P第一个字母，4位的匹配成功。

事实证明用后5位和前5位匹配的结果是相同的。

### mismatch 

- verification mismatch

5-mer匹配上了，但是P串剩下的没有匹配上

![image-20211226190532444](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226190532444.png)

- k-mer mismatch

P中的k-mer与T串index没有找到匹配的

![image-20211226190641469](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226190641469.png)

### index hit

每当P串中的k-mer与T串的index相匹配一次，我们就称为一个index hit。例如下图为2个index hit

![image-20211226190754909](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226190754909.png)



---

# 2. Ordered structures for indexing

## data structure

我们给T串用3-mer的方法构建index；

先把所有长度为3的片段及其offset写下来

![image-20211226201048420](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226201048420.png)

然后把他们按照字母表的顺序排列

![image-20211226201120212](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226201120212.png)

于是我们构建好了3-mer index

随后我们使用二分法进行查找。

## Binary search

我们以P串：TGG为例，演示二分法在k-mer中的操作。

首先我们与最中间的片段【GTG】对比

![image-20211226201649887](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226201649887.png)

我们知道按照字母表顺序，T在G后面，所以我们只看后半部分。

我们继续找后半部分的中间的片段，【TGC】，由于G在C后面，所以我们再看后半部分

![image-20211226201813555](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226201813555.png)

最后只剩两个片段，我们一个一个看，发现与offset=7的片段比对上了。

![image-20211226202032186](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226202032186.png)

二分法的速度是$log_2(n)$

## Python package

Python中有个包，名字就叫bisect，专门用来二分查找。

函数有两个参数：

a：ascendent sorted list （数字或字母表顺序）

x：需要在序列a中查找的项目

函数返回值是能使插入a之后还能让a保持原来的顺序的(最左边)位置，例如

![image-20211226202850673](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226202850673.png)

对于k-mer来说，我们也可以调用这个函数

![image-20211226203044178](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226203044178.png)

---

# 3. Hash tables for indexing

## Hash table

哈希表就像是一群空的篮子，其中哈希函数h负责把3-mers map到哈希表上



![image-20211227180937791](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227180937791.png)

- 普通映射

函数h把一个3-mer映射到hash table上面，制造一个entry。

1个entry含有：key(3-mer) 、offset(记录位置) 、reference(扩展)

![image-20211227185159198](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227185159198.png)

- 遇到重复的key

如果遇到重复的3-mer，我们把它映射到已经存在的key的reference里面

![image-20211227185311918](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227185311918.png)

- collision

因为3-mer比hash table中的bucket要多，所以很容易产生2个key挤到一个bucket的情况。

鸽子洞理论：我们预料到有一些不同的key(3-mer)会在同一个篮子里

![image-20211227185633380](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227185633380.png)

在collision后还可以继续添加重复的key，例如下图中的GGG

![image-20211227185816778](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227185816778.png)

## match

我们制作完hash table后，要开始把P串与T串相匹配了。

例如我们想在T串中寻找所有符合GGG的片段：

首先我们通过哈希函数h找到hash table里对应的bucket

![image-20211227190144768](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227190144768.png)

然后我们与这个bucket中的第一个entry中的key相比对，发现不匹配，继续匹配下一个，最终我们匹配到了全部3个匹配的3-mer

![image-20211227190345560](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227190345560.png)

## python dictionary

python中的【字典】本身就是一个哈希表。

如果我们需要找到k-mer的位置，直接输入key即可。

![image-20211227190608433](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227190608433.png)
