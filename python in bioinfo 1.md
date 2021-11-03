# 今天开始练Python
匆匆地过了一遍python基本语法，在看numpy的时候就想着这不是跟R的语法差不多嘛，也没必要继续看了，直接在操作里理解和练习吧。于是在YTB上找到了一个用Python进行Bioinformatics操作的小课程，一共才9个part，感觉还是很容易学完的。所以，下面就开始记录学习过程啦！

（课程链接：https://www.youtube.com/watch?v=Wkx0fI4e0fs&list=PLpSOMAcxEB_jUKMvdl8rHqNiZXFIrtd5G&index=2&t=239s）

# part1课程目标
编写函数完成以下功能：
1. 检查输入内容是否为DNA序列
2. 计算这段序列中ATCG的count数量。

# 代码
代码分成两个script。`DNA_Toolkit.py`用来放函数代码，`main.py`用来检验函数是否正常运作。

## DNA_Toolkit.py
```
# DNA TOOLKIT FILE
import collections
Nucleotides = ["A", "G", "C", "T"]

# Check the sequence to make sure it is a DNA String
def validateSeq(dna_seq):
    tmpseq = dna_seq.upper()
    for nuc in tmpseq:
        if nuc not in Nucleotides:
            return False
    return tmpseq

# Count the nucleotides frequency
def countNucFrequency(seq):
    tmpFreDict = {"A": 0, "C": 0, "G": 0, "T": 0}
    for nuc in seq:
        tmpFreDict[nuc] += 1
    return tmpFreDict
```
第一个`validateSeq(dna_seq)`函数用`.upper()`对序列进行了大写处理。这是因为fa格式的文件里会存在一些高度重复序列是使用小写字母atcg表示的。

还有一个需要注意的地方是，由于Python与R不同，每个循环前后不写`{}`，所以要看好代码对齐的是哪个循环。一开始我没注意，把第二个return跟if对齐，输出内容就是不对的。

第二个`countNucFrequency(seq)`函数我感觉还是非常巧妙的，直接以核苷酸为key创建了一个空字典，然后每遇到一个相同的序列就增加相应key的数值。充分发挥了Python中“字典”这个数据格式的优势。我想如果用R写的话，估计会更啰嗦。

此外，第二个函数还可以引入numpy的counter函数，形成更简洁的代码：
```
def countNucFrequency(seq):
  return dict(collections.Counter(seq))
```
注意counter的输出结果要加个`dict()`返回字典格式，否则会输出
```
Counter({'G': 16, 'C': 12, 'A': 12, 'T': 10})
```
其他counter用法参考：https://blog.csdn.net/qwe1257/article/details/83272340

## main.py
```
# DNA Toolset/Code testing file
import random
from DNA_toolkit import *

rndDNAStr = ''.join([random.choice(Nucleotides)
                     for nuc in range(50)])

DNAstr = validateSeq(rndDNAStr)
print(countNucFrequency(rndDNAStr))
```
这里`rndDNAStr`的随机序列生成我还是看了一会儿才看懂。

首先`.join`的用法：`str.join(seq)`.str是连接符号，seq是字符串序列。输出一个在每两个字符之间都用str进行连接的字符串。

`choice`是random模块中的函数，需要先import random才能调用，作用是随机采样。

整行代码的写法是一行式，相比于普通的for循环更简洁。试着把这行代码拆解为普通循环，如下：
```
tmp = []
for i in range(50):
    nuc = random.choice(Nucleotides)
    tmp = tmp + [nuc]
rndDNAStr = "".join(tmp)
print(rndDNAStr)
```
其实用random.sample函数也可以直接一次性随机从序列中取出几个值，但是函数定义sample不可以大于population，所以不适用于本情况。

这节课的笔记就到这里吧，睡觉去啦~
