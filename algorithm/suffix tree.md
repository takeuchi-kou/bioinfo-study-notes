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



[算法]轻松掌握suffix tree p2_两种朴素的构造方法：https://www.bilibili.com/video/BV1d541147Uc/?spm_id_from=333.788.recommend_more_video.0

[算法]轻松掌握suffix tree p3_理解隐式树与$符号的含义：https://www.bilibili.com/video/BV19541167cH/?spm_id_from=333.788.recommend_more_video.-1

[算法]轻松掌握suffix tree p4_更复杂的例子(离理解ukkonen只差一步)：https://www.bilibili.com/video/BV1Vz411b77z/?spm_id_from=333.788.recommend_more_video.-1

