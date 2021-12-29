# KMP算法

## 1. KMP算法的直观认识

「天勤公开课」KMP算法易懂版：https://www.bilibili.com/video/BV1jb411V78H?from=search&seid=3868126634992789946&spm_id_from=333.337.0.0

KMP算法是用来进行字符串匹配的，它能够快速将模式串匹配到主串中，并且指针不回溯。（即时间复杂度为线性）

与之相对的是 Naive 算法（brute force），这就是最简单暴力的每个字符精确匹配。遇到不匹配的再从头后移一位开始匹配。

###  KMP流程图解

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

## 2. 进一步认识KPM算法

### 2.1. 构造模式串的前缀表(prefix table)

例如模式串为[a,b,a,b,c]，那么它的前缀一共有五个：

![image-20211224113804992](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224113804992.png)

在每个前缀中，都找比自身短的最长公共前后缀。

- 以[a,b,a,b]为例：

比自身短的最长前后缀分别为：前[a,b,a] 后[b,a,b]；

前后缀不同，缩短一位： 前[a,b] 后[a,b]；

此时前后缀相同，那么[a,b]就是[a,b,a,b]的公共前后缀；

由于[a,b]长度为2，所以在前缀表中记为2。

![image-20211224114934033](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224114934033.png)

- 以[a,b,a]为例：

比自身短的最长公共前后缀为[a]；

由于其长度为1.所以在前缀表中记为1.

前缀表中的其他字符都找不到任何公共前后缀，所以记为0。

![image-20211224115046388](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224115046388.png)

最后得到的一串数字(数组)，就是模式串的前缀表(prefix table)。

但是有时会把最后一个数删掉，然后在最前面加上一个-1。

最终得到：

![image-20211224115711324](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224115711324.png)

### 2.2. 把模式串与主串匹配

先把模式串的位置索引添加到模式串上方；

然后从主串的第一位开始比对：

发现在模式串第[3]位

![image-20211224121501356](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224121501356.png)

第三位对应的前缀表的数字为[1]，就把索引为[1]的位置移动到未匹配上的位置：

![image-20211224122718463](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224122718463.png)

移动过后仍然匹配不上，这个位置的前缀表数字为[0]，所以找到模式串位置索引为[0]的地方，移动到当前位置：

![image-20211224123437906](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224123437906.png)

当前位置匹配上了，下一位未匹配上，对应的前缀表数字为[0]：![image-20211224124317810](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224124317810.png)

所以把位置[0]对应的字符拉过来对齐

![image-20211224124418646](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224124418646.png)

现在位置[0]不能匹配，对应的前缀表数字为[-1]

![image-20211224142537526](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224142537526.png)

然后把[-1]位置与未匹配的位置对齐

![image-20211224145424879](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224145424879.png)

这次完全匹配上了

![image-20211224145546671](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224145546671.png)

继续匹配。因为当前位置前缀表数字为[2]，所以把位置[2]的字符移动到当前位置

![image-20211224145720140](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224145720140.png)

没有匹配上。由于主串已经匹配完成，所以不需要继续移动了。

![image-20211224145744858](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224145744858.png)

## 3. KMP算法：代码实现

### 3.1 模式串的prefix table

模式串及其匹配结果如下：

![image-20211224151039194](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224151039194.png)

![image-20211224151008639](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224151008639.png)

填入数组：

![image-20211224151326793](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224151326793.png)

到此为止是我们已经熟知的方法。现在提出新问题：

**在已经知道前几位的prefix 数组，该怎么快速填入下一位呢？**

例如：已知前五位的prefix，如何快速填入第六位数字。

![image-20211224155915989](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224155915989.png)

先观察前五位，第5位的数字是[1]，这代表了前5位的最长公共前后缀的长度为1；

如果我们想让最长公共前后缀的长度+1的话，第6位需要满足的条件是：

与第2位的字符相同，在这个例子中是字母'B'。

由于第6位确实是字母B，所以prefix table中的数字+1

**这个过程使用计算机语言如何实现？**

以上面那个过程为例：

```
## 伪代码 ##
# 已知模式串[0-5]的最长公共前后缀的长度为 1
len = 1
# 令接下来需要填入prefix table的位置为 i, 则当前
i = 6
# 判断第[i]位字符是否与前缀的第[1]位相同，若相同则len += 1
if P[len] == P[i]:
	len += 1
	prefix[i] = len
```

这样一来，增加prefix table中的数字的过程判断就完成了，下面看如果没有匹配上该怎么办。

不能让数字直接归零，因为就算不能匹配最长前缀，也有可能与较短的前缀相同，例如：

![image-20211224174438424](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224174438424.png)

前7位的最长公共前后缀长度为3，但是第[8]位无法匹配上。然而不能归零，因为P[8]和P[0]可以形成公共前后缀。

我们需要先往前找，如果找不到，再归零。

在本例中，len=3的情况下，下一位匹配不上，于是我们往前找

![image-20211224185031992](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224185031992.png)

len=3的前一位，prefix=1，那么len指针移动到P[1]。此时prefix=0，验证后一位是否与第8位字符相同，判断相同后，len+1.最终得到prefix[8]=1.

```
### 伪代码 ###
# 已知模式串[0-7]的最长公共前后缀的长度为 3
len = 3
# 接下来需要填入prefix table的位置为 i, 则当前
i = 8
# 判断第[i]位字符是否与前缀的第[3]位相同
if P[len] == P[i]: #不相同
	len += 1
	prefix[i] = len
# 往前找一位
else:
	len = prefix[len-1]  # len = 1 
#退格重新找
if P[len] == P[i]: # 不相同
# 往前找一位
else：
	len = prefix[len-1]  #len = 0
#退格重新找
if P[len] == P[i]:   #相同
	len += 1		#len = 1
	prefix[i] = len		prefix[8] = 1
```

最后别忘了把所有数字往后移位，第一个数字变为-1.

![image-20211224194833357](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224194833357.png)

### 3.1 模式串与主串对比

为避免混淆，先规定：

模式串pattern指针用j，一共n位；

文本串text指针用i，一共m位。

即：

```
P[j] = char_in_pattern
prefix[i] = num
len(P) = n

T[i] = char_in_text
len(T) = m
```

![image-20211224194928136](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224194928136.png)

比对过程很简单，就是看当前的P与当前的T的字符是否相等,如果相等的话就继续比对下一位：

```
if T[i] == 	P[j]:
	i += 1
	j += 1
```

如果比对不上的话，就把指针移动到prefix对应的位置上

![image-20211224195816051](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224195816051.png)

移动后

![image-20211224200119135](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224200119135.png)

```
if T[i] != P[j]:
	j = prefix[j]
```

还有一种情况，就是如果P[0]位置，也就是prefix=-1的位置不匹配，那么j和i都往后移动一位（因为没有任何线索）

```
if T[i] != P[j]:
	j = prefix[j]
	if j == -1:
		i += 1
		j += 1
```

当在text里面找到了一个完整的pattern的时候：

![image-20211224201246699](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224201246699.png)

因为最后一位prefix=3，所以把指针移动到P=3的位置

![image-20211224201445537](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211224201445537.png)

这是因为这样可以保证前三位都是匹配上的，然后继续往后找。

```
if j == n-1 && text[i] == pattern[j]:
	print(f"Found pattern at %d", i-j)
	j = prefix[j]
```



# 继续KMP数组（代码版），然后是Z算法。

https://www.bilibili.com/video/BV1LK4y1X74N?from=search&seid=5740237619893869905&spm_id_from=333.337.0.0

