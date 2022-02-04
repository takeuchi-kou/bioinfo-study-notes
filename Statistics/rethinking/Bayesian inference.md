### 地球仪问题

我们要通过随机在地球仪上取点，判断地球上水和陆地的比例。

我们通过模拟取点得到一系列数据，其中6次取到了水，3次取到了陆地；

在这种情况下，我们推测有70%的区域被水覆盖，也就是 陆地面积/水面积=0.7；

我们猜对的可能性是多少？

### 解答

- 二项分布公式

一共取了9次，其中6次取到了水，这是典型的二项分布问题。

根据二项分布概率公式，以及我们做出的假设(0.7概率取到水)，我们可以得到具体的分布概率，如下图。

![image-20220128153233570](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220128153233570.png)

在R中可以用一行代码得到答案：`dbinom(W, W+L, P)`, d 意思是 density。

![image-20220128153444812](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220128153444812.png)

- 先验概率

为了使曲线下面积1，我们给出一个先验概率：常数1。其实这个常数并不重要，重要的是使之合乎逻辑，因为在我们得到数据后会归一化。

![image-20220128154304337](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220128154304337.png)

- 归一化

![image-20220128155102484](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20220128155102484.png)