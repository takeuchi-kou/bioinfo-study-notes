# Markov processes

## 1. 离散马尔科夫过程

与泊松过程和伯努利过程的无记忆性不同，马尔科夫过程认为过去和未来是有关联的，并且未来可以从过去预测。预测方法是通过状态(state)，及其概率分布。用数学语言表达，则为状态(t+1)是状态t的函数，加上某些噪音的影响。

![image-20211230085346236](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211230085346236.png)

### 例子：超市排队

对超市收银台排队结账进行建模：

顾客的到来：参数为p的伯努利分布

顾客接受结账服务的时间(多久离开)：参数为q的几何分布，这两个分布互相独立

状态X：正在排队的顾客数量有n个

约束：超市规定，同时排队的顾客不能超过10个。

所以，全部的state为0-10。

假设现在处于状态2，那么会发生4种情况：来了一个顾客，状态会变为3；走了一个顾客，状态会回到2；没有顾客来和走，状态不变；同时有一个顾客来一个顾客走，状态不变。

根据这四种情况以及概率乘法，我们可以计算状态转换的概率，状态转换的概率与当前状态无关(除非在两头0或10)，并且对于某一状态来说，所有状态转换的概率相加为1.

根据状态转换的概率画出的如下图像，被称为转移概率图(transition probability graph)，它能够体现离散时间有限状态马尔科夫链模型

![image-20211230103132262](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211230103132262.png)

## 2. 离散有限状态马尔科夫链的一些性质

我们现在研究的是在离散时间内有限状态的马尔科夫链，初始状态可以指定或随机给出。

时间同质性(time homogeneous)：即状态转换的概率与具体状态序列号无关(第1状态转换到第0，与第n+1状态转换到第n，概率是一样的)

全概率相加为1：一个state的所有转换可能性相加等于1；

有了当前状态，则历史状态无关紧要：无论我们知不知道状态转移的历史，都不影响当前状态转移概率；

model specification：只要知道了当前状态，其转移的可能性和转移的概率也都可知。

![image-20211230114547565](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211230114547565.png)

### n步后的状态及概率

为了简化问题，我们假设仅存在i和j两种状态，从一步i走到j的概率为$p_{ij}$。假设X由初始状态$X_0=i$，在走了N步变为$X_N=j$，问N步后的状态转变概率。

- 从后往前递归

我们先定义一个给定state k，是从i走了n-1步后的状态(任意一个此时可能的状态),那么通过从i走到j的概率，即为从i走到k的概率乘以从k走到j的概率。即$r_{ikj}=r_{ik}(n-1) \times p_{kj}$；

但k使我们任意选择的一个可能的状态，并不是全部的可能性；

由全概率公式我们知道，需要让所有k的可能性相加，才可以得到$r_{ij}$的完整可能性(下图右上角)；

于是我们就得到了一如下图黑框中的递归公式，以及与之相对应的概率公式

- 从前往后递归

刚才我们是假设距离最终状态j只有一步，相当于从后往前思考，其实也可以从前往后递归：

我们同样先定义一个给定state k，是从i走了1步后的状态；

那么它距离j就还有n-1步，因此我们可以得到一个不同的递归公式
$$
r_{ij}(n)= \sum_{k=1}^mp_{ik}r_{kj}(n-1)
$$
![image-20211230141247079](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211230141247079.png)

### 一个n-step转移概率的例子

给定一个概率图，初始状态为1，我们想求在n步后状态依然为1的概率。

我们依然从后往前递归：

假设在n步后状态为1，那么在n-1步时，状态有可能是1，也有可能是2，根据全概率公式，有：$r_{11}(n)=r_{11}(n-1) \times0.5+r_{12}(n-1)\times0.2$

另外，在n步后，状态若不为1，那就为2，所以根据全概率公式有：$r_{12}(n)=1-r_{11}(n)$

根据这两个公式，可以开始填写表格：

n=0是0步跃迁，所以不存在概率；

n=1为一步跃迁， 直接根据概率图写下即可；

n=2为两步，因为我们已经知道n=1的情形，所以只需按照概率图继续计算即可

我们已经知道n=100时的情形，就可以根据概率图计算n=101时的情形(收敛于常数2/7和5/7).

通过计算我们还可以发现，初始无论是1还是2.其最后结束于状态1或2的概率都会收敛于同样的常数2/7和5/7，这告诉我们一个事实：当n足够大时，初始状态将不再重要。这可以被总结为一个特征：**马尔科夫链最终会进入稳定状态**。

稳定状态的意思并不是不再跃迁，而是在任意n的时候，链系统处于1和2的概率都不再发生变化。

![image-20211230145433407](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211230145433407.png)

### 马尔科夫链的收敛问题

对于比较完好的马尔科夫链(上)，在非常多步骤以后，概率通常都会收敛。但是对于某些不太好的马尔科夫链(下)，则不能完成收敛。

![image-20211231141609028](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211231141609028.png)

### 马尔科夫链的稳态(recurrent)和瞬态(transient)

如果我们从state i开始，无论走哪一步，都能回到i，则我们称i为稳态；

如果不是稳态，即为瞬态。

一个马尔科夫系统中，稳态可以分为不同的类(classes)。同一类中有可以用于互相沟通的路径，而不同类中则没有

![image-20211231155509500](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211231155509500.png)