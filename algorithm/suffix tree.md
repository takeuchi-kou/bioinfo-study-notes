[算法]轻松掌握suffix tree p1_概念及其应用：https://www.bilibili.com/video/BV1c741137GD?from=search&seid=13612306674557965166&spm_id_from=333.337.0.0

# 1. Main concept

以【abcabxabc$】为例：

每一条路径代表了一种后缀的可能性（如图一共10种,9个字母+$）

红色的数字区间记录了这个节点包含的T串的位置信息；

suffix tree是一种压缩了的字典树。

![image-20211220192250938](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211220192250938.png)

# 2. Application

## 2.1 字符串匹配

以`pattern='abc'`与`text='abcabxabc$'`匹配为例:

从因为p的第一个字母为'a'，所以从root节点开始找'a'，如图为第一条边；

第一条边已经包含了'ab'，所以继续从节点①处找'c'，找到了⑥节点；

⑥节点的子节点有12和13；

其中12节点绿色框内i=0，说明这个含有abc的后缀是从text的位置0开始的；

13节点的绿色框内i=6，说明这个后缀是从text位置6开始的；

这两个后缀就是text中所包含pattern'abc'的全部后缀了。



![image-20211220193435943](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211220193435943.png)

## 2.2 最长重复子串

以p='abc'为例，在t中寻找最长重复子串。

由于node离root越远，意味着其包含的字符个数越多（被压缩的另算）；

并且只要一个node不是leaf，就意味着它包含的字符在text中重复出现；

所以寻找最长重复子串，其实就是在suffix tree中寻找离根节点最远的非叶子节点。

![image-20211220201004032](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211220201004032.png)

## 2.3 最长公共子串

最长公共子串，涉及到两段text的匹配；

方法是在同一棵树中画出两段text，并且用不同结尾符号进行区别；

例如有一新 text：K串，以#符号结尾（原本的T串以$符号结尾）；

那么假如某一非叶节点的子节点既包含$符号又包含#符号，那么这一节点代表的字符串就是这两个text的公共子串；

在此基础上，与寻找最长重复子串一样，选择离root最远的node，就找到了最长公共子串。

## 2.4 最长回文串

步骤基本与寻找最长公共子串相同；只不过这里的新text K串，为原先T串的回文串。只需找到这两个字符串的最长公共子串，就解决了最长回文串问题。

![image-20211220202017313](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211220202017313.png)

-----



[算法]轻松掌握suffix tree p2_两种朴素的构造方法：https://www.bilibili.com/video/BV1d541147Uc/?spm_id_from=333.788.recommend_more_video.0

# 3. 两种朴素的构造方法

两种都是比较暴力的方法，由于方法1时间复杂度太高且不包含什么技巧，所以主要学习方法2.

![image-20211221163632607](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221163632607.png)

事实上，方法2时间复杂度甚至可以到达$n^3$，但由于它包含了UKK算法的部分技巧，所以需要学习和掌握。

## 3.1 方法2——例1：T串无重复字母

绿框中是算法的循环结构，示例T串为[a,b,c]。

看起来，这个算法构造所有的后缀需要1+2+3=6步，

但是其实在没有重复字幕的情况下，这棵树自动压缩所有的边，仅需要3步即可构造所有后缀。

![image-20211221165433476](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221165433476.png)

*其实，按照最后的一次i循环就得到所有后缀来看，似乎i不需要让i从0开始加，如果从一开始就与T串长度一致的话，只要循环3次j即可把后缀树构建完全。*

---

[算法]轻松掌握suffix tree p3_理解隐式树与$符号的含义：https://www.bilibili.com/video/BV19541167cH/?spm_id_from=333.788.recommend_more_video.-1

## 3.2 例2：T串有重复字母（隐式树和$符）

以T串[a,b,c,a]为例。在i=0,1,2的时候，步骤与之前相同；

但i=3的时候，出现重复的字母a，此时该如何把后缀a与后缀abca分开呢？

这里引入隐式树概念，通常用'$'表示；

正因为'$'一定与T串中任何一个其他字符都不同，所以在末尾插入它，就能保证重复字母也可以独立作为后缀写进树中。

![image-20211221174337289](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221174337289.png)

下图为引入$之后的后缀树构造

![image-20211221174803806](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221174803806.png)

如此一来原先的隐式树就显现出来了。

---



[算法]轻松掌握suffix tree p4_更复杂的例子(离理解ukkonen只差一步)：https://www.bilibili.com/video/BV1Vz411b77z/?spm_id_from=333.788.recommend_more_video.-1

## 3.3 例3：重复字符和新字符

在i=0,1,2时，没有重复字符，按照最简单方法构建后缀树；

在i=3,4时，出现了重复的字符，但是现在不做处理，让重复字符依然隐藏在已有的节点中；（此时为隐式树）

在i=5时，出现了重复字符和新字符，处理新字符：把已有的边从中间分开，增加子节点，这样一来，反而能够把所有的后缀都显现出来；

![image-20211221180023959](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221180023959.png)

在i=6时，由于新加入的字母a已经出现过了，所以又出现了一个隐式树"a"；

在i=7时，新加入的b已经出现过了，且"ab"也已经存在，所有此时有2个隐式树"ab" 、"b"；

![image-20211221180708708](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221180708708.png)

在i=8时，新加入的"c"已经出现过，所以此时有3个隐式树"abc" "bc" "c"；并且观察此时的后缀树，我们发现划红下划线的后缀无需我们手动构造，它随着子串的增加自动往后延伸，而画圈的部分也无需我们构造，它们隐含在已有的树中；

在i=9时，加入的是绝对不可能在其他位置出现过的符号$,那么此时为了添加这个stranger，我们需要手动裂变构造节点。如此一来，之前的隐式树全部被迫显现出来了。

![image-20211221181719082](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211221181719082.png)

