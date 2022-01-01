# Long term Markov Process

## 1. 复习

### 马尔科夫链的性质

离散时间、离散状态的马尔科夫系统，具有：

- 时间齐次：无论初始状态是什么， 都不影响其转移概率

- 仅与当前状态相关：马尔科夫的历史状态与当前的转移概率无关（知道其历史转移轨迹并不能改变当前的转移概率）

- 使用递归公式计算n步后的状态(k=n-1)

![image-20211231191742568](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20211231191742568.png)

### 例子

- 求马尔科夫链中指定路径的概率：1-2-6-7

利用乘法法则，由于历史轨迹与当前概率无关，所以可以简化为3个路径概率的乘积

- 给定起点和步数，求马尔科夫链中某终点的概率：2-?-?-?-7

方法一：brute force，暴力列举所有可能路径，然后按照例题1的方法求解。但这种方法在步数多的时候复杂度会增长得很快(指数级)

方法二：递归公式。

![image-20211231193025961](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20211231193025961.png)

### recurrent and transient states

- recurrent state ：循环态是指从这个状态出发，无论方向是什么，最后还能够回到这个状态；

- transient states ：所有不是稳态的都成为瞬态。
- recurrent class ：一组处于循环态的状态，他们之间有互相转换的路径(属于不同class则无法互相转换)

![image-20220101094747127](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220101094747127.png)

# 2. steady state

## periodic states in a recurrent class

简单来说就是每一步都能够确定会落在哪个state上面,直观来看就是stream只有一个方向。

区别一个recurrent class里面的state 是否为periodic，一个比较简单直接的方法是看有没有self transition。有的话就不是periodic。

![image-20220101094725628](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220101094725628.png)

## steady-state probability：balance equation

某个马尔科夫链系统在初始状态为i，走过n步后，其终止状态为j的概率是否收敛于某个常数$\pi_j$？

- 定理：

其概率是收敛的，如果：

1. 这些循环态如果都处于同一个class中，并且
2. 这个class不是periodic的

- 计算方法

如果该马尔科夫系统稳态，从递归公式我们可以推出：
$$
\pi_j=\sum_{k=1}^mr_{ik}(n-1)p_{kj}
$$
其中我们n取正无穷，并且$\pi_j$所有可能的取值加起来为1.

![image-20220101103042846](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220101103042846.png)

### example

如下图，我们先把图中的概率带入上面得到的公式里，发现得到了$\pi_1 \pi_2$之间的关系，但无法得到解。然后我们知道，图中一共有m种states，m=2，且m种states的可能性加起来一定为1，由此我们得到概率的具体数值。

![image-20220101153000599](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220101153000599.png)

### balance equation的频率解释

之前我们得到的用于求某个state 稳态概率的的balance equation，我们可以从频率角度解释。

- 停留在j上的频率

假设有一个观察者一直记录着state停留在j上的次数，那么当n足够大的时候，$\pi_j$就可以解释为停在j上的次数除以总次数n，也就是频率；

- 从1到j的转换频率

仔细考虑这个过程：从1到j的转换，意味着初始状态需要为1，然后在初始状态为1的可能性里，需要转换到j，那么这个概率就分两段：$\pi_1$是初始状态为1的概率，$p_{1j}$是状态从1到j的概率；并且按照频率角度解释，也可以转换成频率，即：从状态1转换到状态j的频率为$\pi_1p_{1j}$；

- 转移到j的频率

也就是求从所有state转移的j的频率的和，假如一共有m个状态，则从任意一状态转移到j的频率为$\sum_{k=i}^m(\pi_kp_{kj})$。

![image-20220101160841458](https://gitee.com/joy_thestraydog/typora1.0/raw/master/image-20220101160841458.png)

## 3. birth-death processes

