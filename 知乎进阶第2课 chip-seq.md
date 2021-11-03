# Rosalind 入门小练习
这节都是一些非常基础的python操作，主要是认识Rosalind的学习模式。

Rosalind链接：http://rosalind.info/

它可以提供一些练习题让你在应用中熟悉用算法和代码解决生物问题。
![](https://files.mdnice.com/user/20439/c99cf3ff-c7a6-4cca-ab6e-6a67a11aa1fb.png)
目前来说只看到了Python相关的练习题，以后继续探索。我非常喜欢这种边学边练的模式，接下来我也会按照这个系列顺序继续练习。

这次是village里面的习题，全部是Python基础操作，没什么好说，就放一下代码好了。
## 无聊的代码区
```
# import this
from collections import Counter

# ==== Variables and Some Arithmetic ====
a = 896
b = 938
print(f'{a}^2 + b^2 = {a**2 + b**2}')

# ====Strings and Lists====
word1start = 29
word1end = 35

word2start = 51
word2end = 57

txtstr = "OOGXDETXpgKeQAIGkoMNSIjOEWbALScorpioMnN6avpOtC5L1vZhispidavaugNyz6rDaWbiBB9o6fiDbPWg54VHIMnt0ewhM45AI39tzyEgkUg4fRUS40MlMzMK3lkL2IHLDAuH8kyeCNkzosYqvaU6wHrRWg8dKZTUwYHUc7PyA4yrEIDOIBBGD0y."

# Note : end position is not inclusive
print(
    f'{txtstr[word1start:word1end + 1]} {txtstr[word2start:word2end + 1]}'
)

# ==== Conditions and Loops ====
startPos = 4066
endPos = 8123
result = 0

for x in range(startPos,endPos + 1):
    if x %2 != 0:
        result += x
print(result)

# another way
result = sum(
    [x for x in range(startPos,endPos + 1) if x % 2 != 0]
)

# ==== Working with Files ====
outputFile = []

with open('D:\\DATA_DOCUMENT\\village\\input.txt','r') as f:
    outputFile = [line for pos, line in enumerate(
        f.readlines()) if pos % 2 != 0]

with open(r'D:\DATA_DOCUMENT\village\out.txt', 'w') as f:
    f.write(''.join([line for line in outputFile]))

# ====Dictionaries ====
txtstr = "When I find myself in times of trouble Mother Mary comes to me Speaking words of wisdom let it be And in my hour of darkness she is standing right in front of me Speaking words of wisdom let it be Let it be let it be let it be let it be Whisper words of wisdom let it be And when the broken hearted people living in the world agree There will be an answer let it be For though they may be parted there is still a chance that they will see There will be an answer let it be Let it be let it be let it be let it be There will be an answer let it be Let it be let it be let it be let it be Whisper words of wisdom let it be Let it be let it be let it be let it be Whisper words of wisdom let it be And when the night is cloudy there is still a light that shines on me Shine until tomorrow let it be I wake up to the sound of music Mother Mary comes to me Speaking words of wisdom let it be Let it be let it be let it be yeah let it be There will be an answer let it be Let it be let it be let it be yeah let it be Whisper words of wisdom let it be"

# Generic approach:
wordCountDict = {}

for word in txtstr.split(' '):
    if word in wordCountDict:
        wordCountDict[word] += 1
    else:
        wordCountDict[word] = 1

# Optimized, Pythonic approach, using collections module:
wordCountDict = Counter(txtstr.split(' '))

for key, value in wordCountDict.items():
    print(key,value)
```
## 意外之喜的彩蛋区！
在Rosalind网页上乱点的时候发现在course页面里有很多正在讲授生信相关课程的教授名单，随便点进去一个收获了巨大惊喜！
https://www.huber.embl.de/msmb/course_spring_2020/index.html

![](https://files.mdnice.com/user/20439/356cae45-962b-40ce-be1a-52121d1f9b91.png)
网页包含这门课程的所有资料：电子版书籍全文、课程录像、lab录像以及指导和说明。点点手指，即可获取斯坦福大学的最新生物统计学课程内容。感动死了，已加入豪华waiting list套餐！

# LAB部分

## 从ENCODE下载数据
ENCODE数据库地址：https://www.encodeproject.org/

从DATA-experiment matrix界面，根据需要筛选并点击需要的数据。

![](https://files.mdnice.com/user/20439/64722cb1-9613-41cb-b123-4ae6e36cccb5.png)
点进去后的界面，上部分可以看到这个数据的状态和存在的问题等信息

![](https://files.mdnice.com/user/20439/ec72061f-ac37-494f-b3b3-b0b3c0aebce7.png)
例如这个数据存在的问题是测序深度有些不足够，但橙色属于可以使用的数据，如果是红色警示则为存在严重问题的数据，不能使用。

下半部分是具体的数据。

association graph可以看到数据处理流程
![](https://files.mdnice.com/user/20439/b824f6f8-dc8a-4f15-b028-a5ee8cd126e4.png)
黄色是数据，蓝色是处理过程，黄块中的绿点是质量矩阵文件

genome browser可以对基因进行可视化查找
![](https://files.mdnice.com/user/20439/23c02197-17ac-4903-a3d5-0b7537570173.png)

file details可以下载相应数据文件。上半部分是raw data，下边是处理过的数据。建议使用raw data。
![](https://files.mdnice.com/user/20439/8551f014-05ad-49a5-9e8e-582017d9a535.png)
这里注意chip很多是单端测序。

另外，chip-seq需要control数据以去除假阳性。control下载链接在summary里面。
![](https://files.mdnice.com/user/20439/bea7412f-16ec-43dc-a8d8-755b7947f674.png)
在选择数据时，注意同一个问题尽量使用同一个实验室生产的数据，因为不同设备、不同抗体容易产生较大的批次效应。

## chip-seq数据处理流程概览

![](https://files.mdnice.com/user/20439/899f4ba3-6002-4c3f-8807-5e6996124f97.png)
前三个步骤就是常规的测序、比对、质控。

peak calling：peak对应的是目标蛋白的结合位点。

peak merge：在两个生物学重复的结果中能看到两个peak结果，把两个结果merge后更接近真实位点。

由于教程比较老，使用的工具可能已经更新，但是原理是差不多的。其中MACS2这个工具是刘小乐教授的学生liwei在研究生时期写的，现已成为encode标准流程（不愧是女神的学生呜呜呜，我也想这么厉害！）

### MACS2原理
MACS: Model-based Analysis for ChIP-Seq

官方说明文档：https://github.com/macs3-project/MACS
（好像已经更新到3.0了。。。）

详细原理见paper：https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2592715/（或者直接看女神在ytb放的课程录像！）

MACS2 callpeak 根据有无 control 数据采用了不同的计算方法，但都是依据泊松分布。


![](https://files.mdnice.com/user/20439/180a6bbd-bdab-407b-bfc7-eb2838ccdf2f.png)
peak需要为control的1.5-50倍。过高则可能是PCR的dup过多造成的。

## 啊。。那个
在ytb上发现了一个更详细更新的教程视频，lab过程跟着那个走一遍比较好。

https://www.youtube.com/watch?v=uWM5WT3Dt0k
