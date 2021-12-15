![image-20211215100813307](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215100813307.png)

# Problem types

概率分析是使用概率方法对真实世界进行预测或决策。

但是真实世界是否适合这些方法呢？这就要依靠推理和统计对数据进行（特定情境下的）建模。

### 建模和求解未知参数

- 情境：

  有原始信号S，经过某种介质传播，其衰减系数为a，并且传输过程中收到信号干扰W，最后接受到的信号为X。

1. 建立模型问题：发射一个已知的信号S，接受其观测信号X，已知W遵循某种分布：推理a。

2. 参数估计问题：已知系数a，观测结果X，噪音分布W，推理S。

![image-20211215103029956](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215103029956.png)

### 假设检验和估计

1. 假设检验问题：有几种备选的模型可以放在某个特定情境中，我们想知道到底是哪个模型。我们希望借助这个模型能够做出正确的决定，或者能让做出错误决定的概率降低。例如雷达接收到信号，到底是有一只鸟还是一架飞机——实际上是在离散的数据或有限选项中选择。

2. 估计问题：求解数值型问题，目的是估计某个未知量的数值，可以是离散或者连续值。使估计值尽量接近真实值。

![image-20211215115240339](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215115240339.png)

# Bayesian Inference

## 1. the general framework

### 1.1 Variables

- 先验未知量$\theta$

贝叶斯推理与其他方式不同点在于，它将未知量作为一个随机变量，而不是一个未知的常数。

这个未知随机变量$\theta$有一个先验分布$p\theta 或f_{X|\theta}$。

- 观测X

对观测值，即随机变量X进行建模，这个模型为在$\theta$发生条件下的条件概率模型$p_{X|\theta}$。

我们将会得到一个确切的观测值x作为随机变量X的取值，此时我们可以根据贝叶斯定理对条件概率分布$p_{\theta|X}$求解。

![image-20211215121012072](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215121012072.png)

### 1.2. the Output

贝叶斯推理的结果其实就是根据先验分布求后验分布。

但有时还需要对结果有进一步处理。

![image-20211215123229560](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215123229560.png)

### 1.3. the Point Estimates： MAP/LMS

在得到后验概率分布后，有时我们会对这个分布进行一个点估计，也就是给出一个$\theta$的预估值。这个预估值可以代表着$\theta$​的最大概率取值(MAP,最大后验概率)，也可以代表着平均取值（LMS,最小均方估计）。

需要注意的是，$\theta$的具体取值背后，代表着我们对已知参数的一种处理方法，也就是一种函数的结果。我们用小写$\theta$代表某一个具体的估计值，大写$\Theta$代表一个随着X的不同取值而发生变化的随机变量。

![image-20211215125442990](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215125442990.png)

## 2.Point Estimates

### 2.1 MAP Rule：Discrete $\Theta$, discrete $X$

假如$\Theta \& X$都是离散变量。在进行贝叶斯推理以后我们可以得到$\Theta$的后验概率分布。如下图。

在3个备择假设中，$\theta=2$时的概率最大，为0.6。我们选取这个值，就是选择了最大后验概率假设。

MAP能够使conditional probability of error最小。

另外，由于MAP能够使每一个x条件下的conditional probability of error最小，利用全概率公式我们可以得知，MAP也能够使overall probability of error 最小。

也就是说，MAP规则是在假设检验背景下进行估计，得到最小错误率的最优方法。 

![image-20211215133452001](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215133452001.png)

### 2.2 MAP Rule ：Discrete $\Theta$, Continuous $X$

例子：发出标准信号$\Theta${1,2,3}，经过一些连续的噪音干扰W正态分布，得到信号$X$。

由于$\Theta$是离散的，$X W$是连续的，而且已知$X=\Theta+W$。我们可以想象X的PDF是通过把W的pdf移动了$\Theta$个单位得到。

在得到$\Theta$的后验概率分布后，我们可以发现此时运用MAP规则与之前并无区别。依然$\theta=2$时的概率最大。

唯一的区别是在从条件错误概率求总体错误概率时，对每个x的加和变成了积分，因为X变成了连续变量。

![image-20211215134753617](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215134753617.png)

### 2.3 MAP Rule ：Continuous $\Theta$, Continuous $X$

- linear normal models

例如信号是连续的，噪音也是连续的情况。

- estimating the parameter of a uniform distribution

X是一个在一定范围内均匀分布的随机变量，但是这个范围本身是随机和未知的。根据对X的观察，估计范围$\Theta$的取值。

- 估算方法的性能

真实值和估算值之间的差的平方，取平均值：条件下的性能与总体性能。

 ![image-20211215162655748](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215162655748.png)

# 3. Example: biased coin 

## 3.1  β distribution

- 一枚有偏倚的硬币：

  偏倚度$\Theta$未知；把$\Theta$视为随机变量，假设其先验分布为$f_\Theta()$;

  抛掷n次，其中K次正面向上，n是常数，K是离散随机变量；

  推理$\Theta$。

- 假设先验分布$f_\Theta()$是在[0,1]上的均一分布（也就是我们完全不知道硬币的偏倚度）

  由于我们处理的$\theta$是连续随机变量，所以在利用贝叶斯公式的时候，为了确保等式左边积分结果为1，右边分母部分必须等于分子部分的积分结果。

将这个贝叶斯公式化简，得到的就是$\beta$分布。

![image-20211215171530307](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215171530307.png)

β分布的一个很好的性质是，当先验分布是β分布时，求出的后验分布也会是β分布，这样就可以对它进行递归计算。

## 3.2 Point estimates

得到后验概率后，可以对$\theta$进行一个点估计。

首先求MAP，即求概率最大时的$\theta$。此处可以先对左右取对数再求导，得到结果k/n。这是一个符合直觉的结果。

然后求LMS，也就是$\theta$的期望/平均值。这时需要求解d(n,k)，利用期望共识以及蓝框中的公式，最终求出期望值为$\frac{k+1}{n+2}$。

这意味着在n较小时，平均值和最大概率值是不同的；而n越大这两者越相等。

![image-20211215173430725](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215173430725.png)





# SUMMARY

贝叶斯推理和后验分布；

点估计的两种方法：MAP LMS

评价预估性能的方式：错误的条件/全概率；误差平方的条件/全期望



![image-20211215174219001](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211215174219001.png)