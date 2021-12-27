![image-20211226125026435](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226125026435.png)

## 1. Definition

泊松过程的定义和性质都可以通过和伯努利过程做类比来理解。

### 定义

把伯努利过程的每个time slot都设为极小的间隔，在极限情况下得到的就是泊松过程。

### 独立性

伯努利过程每个time slot的事件是独立发生的；

泊松过程每个不相交的时间段(time interval)的事件是独立的。

### Time homogeneity

伯努利过程每个time slot发生事件的概率p不随slot的改变而发生变化；

泊松过程每个time interval发生的事件有2个参数：该interval中发生的事件数量k，interval的duration$\tau$；其中duration $\tau$是常数，也就是说interval的长度是固定的，而事件概率$P(k,\tau)$是关于事件发生数k的函数。

### Small interval probabilities

为了避免在泊松过程中存在同一时间点超过一个arrival的情况，我们对$P(k,\delta)$规定，当$\delta$极小时($\delta$代表极小的time interval)：

- 超过1个arrival的可能性为0；

- 在单位事件内有1个arrival的概率为$\lambda$，那么在$\delta$时间内有1个arrival的概率就与$\delta$成正比，并且系数为$\lambda$，即$P=\lambda \delta$；

- 在$\delta$时间内没有arrival的概率为$P=1- \lambda \delta$。

虽然在$\delta$​极小时，k=1和k>1都有P=0，但是这是一个近似情况：在二阶的情况下，二者是存在区别的，虽然与一阶相比，二阶项可以被忽略。

另外，通过上述分析我们可以知道$\lambda$是一个重要系数，如果它变化，那么在time interval中发生的arrival也会随之变化，这个系数被称为 arrival rate。

## 2. 在固定time interval($\tau$)中有k个arrival的泊松PMF

首先我们论证在使用伯努利过程对泊松过程近似的过程中，当时间间隔$\delta$越小的时候，其描述越准确。所以我们直接套用二项式的PMF然后取极限，就能得到泊松过程的PMF。

具体过程是，先把每一个$\tau$​分成非常大的n份，每一份的间隔为$\delta$；

之前我们已经论证了在每一个$\delta$里有1个arrival的概率是$P=\lambda \delta$(这里已经把二阶项近似掉了)；

那么每一个$\tau$里的arrival的个数就应该是$np=n\lambda \delta=\lambda \tau$；

在上章我们已经得到了泊松对伯努利PMF的近似，但当时设$\lambda=np$，这里改成$np=\lambda \tau$就得到了泊松过程对k的的PMF，如下图右下角

![image-20211226143931733](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226143931733.png)

### 泊松PMF的期望和方差

我们可以通过普通的期望和方差计算方法去计算泊松PMF的方差，但是这里有一个简便方法：

由于我们的泊松PMF是通过对二项式的近似得到的，而当n越大这个近似就越准确，所以我们可以直接套用二项分布的期望和方差，然后把离散形式的近似公式代入，就得到了直观而简便的期望和方差公式。

观察这个期望公式可以发现，$\tau$ time interval中的arrival个数的期望值为$\lambda \tau$，所以$\lambda$被称为到达率（或到达过程的强度）是非常自然的事情。

另外，泊松PMF的一个奇特性质是其方差与期望值相等。

![image-20211226145019205](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226145019205.png)

## 3. 在arrival出现前的时间$T_1$

在泊松过程中秋$T_1$​，可以类比伯努利过程中的几何分布，但变为连续变量。

对于求连续过程的PDF，我们总是从CDF开始：

由于其PDF的条件是，$T_1=t$，在时间t时出现了第一个arrival；

那么其CDF所隐含的意义为，从一开始到时间t出现arrival的概率之和，即时间t或者时间t之前出现了第一个arrival；这也就是说，第一次arrival并不出现在t之后，总结成公式就是：$P(T_1<t)=1-P(T_1>t)$；

又因为$P(T_1>t)$意味着t之前没出现过arrival，这就可以套用上一小节求出的泊松过程的PMF公式了，其中k=0，$\tau=t$.

对等式两边求导，我们发现这是一个指数分布的PDF。

并且指数分布也具有无记忆性。

![image-20211226162151374](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226162151374.png)

### 出现第k次arrival的时间$Y_k$

同样先从CDF开始。

因为$Y_k$的PDF的含义是在时间点y的时候出现了第k次arrival，所以其CDF的含义是，时间点y的时候至少出现了k次arrival，也就是大于k次，其概率可以通过泊松PMF求出：在y时间点之前出现了n>k次arrival的概率之和。

但其实还有一种更加简便(离散化)的方式。

设想，在时间y时有k次arrival，那么这第k次就是在(y,y+$\delta$​)这个slot里发生的，其中(k-1)次是在(0,y)这个interval发生的，这样就可以利用泊松PMF公式了；

综上述，有下图红框中的等式：k 阶Erlang distribution

如果k=1的话，就是我们求过的指数随机变量$T_1$

![image-20211226174639283](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211226174639283.png)

### $Y_k：无记忆性$

假如$T_i$是第(i-1)次arrival到第i次arrival的time interval，那么每个$T_i$之间其实都是互相独立、同分布的指数分布随机变量，所以$Y_k$的期望与方差可以通过k乘以$T_i$的期望和方差求得，其中$Y_k=T_1+ \cdots + T_k$

并且，由于Y是通过把T相加求得，所以我们还可以把泊松过程定义为一系列T累加得到的过程，即k个iid泊松过程的arrival$T_i$相加得到的依旧是泊松过程。同时泊松过程也可以反过来通过这个方法去构造。

![image-20211227112442210](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227112442210.png)

## 4. 比较泊松/伯努利过程

- 性质：连续/离散

- 参数：单位时间的到达强度或概率"到达率"/每次试验的成功率

- PMF(到达次数)：泊松分布/二项分布

- 第一次到达前：指数分布/几何分布

- K次到达：Erlang分布/Pascal分布

在推导泊松分布的时候，我们先从伯努利分布开始，把time slot分为非常小而多的slot，然后取极限。

![image-20211227113623415](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211227113623415.png)