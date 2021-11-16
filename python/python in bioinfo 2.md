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
