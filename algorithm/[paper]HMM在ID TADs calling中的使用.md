### 碎碎念
昨天的操作流程里有一个问题没明白（好吧其实有很多都没明白，只是目前更关心ML算法相关的东西。）HMM算法究竟是怎么在DI的TADs calling里面应用的？

我从bioconductor里找到了用来call TADs的包，阅读了官方说明文档后找到当时发明这种方法的论文，是2012年发表在NATURE上面的一片论文，目前全文可以在pubmed上面免费阅读。以下是我从补充材料里截取的对Boundary Calling算法实现的具体描述章节。放在这里以备后续学习参考。（没错我一时半会儿还看不懂。。人菜瘾大说的就是我）
### Directionality Index, Domain and Boundary Calling
We noted that the regions at the periphery of the topological domains are highly 
biased in their interaction frequencies. In other words, the most upstream portion of a 
topological domain is highly biased towards interacting downstream, and the downstream 
portion of a topological domain is highly biased towards interacting upstream. We 
reasoned that by identifying such biases in interaction frequency in the genome, we 
would be able to identify the locations of topological domains and boundaries in the 
genome.  

To determine the directional bias at any given bin in the genome, we developed a 
Directionality Index (DI) to quantify the degree of upstream or downstream bias of a 
given bin. The directionality index is calculated in equation 1, where A is the number of 
reads that map from a given 40kb bin to the upstream 2Mb, B is the number of reads that 
map from the same 40kb bin to the downstream 2Mb, and E, the expected number of 
reads under the null hypothesis, is equal to (A + B)/2.

Eq. 1 

![](https://files.mdnice.com/user/20439/590f35b7-d275-4f4a-aab5-df1ad49f6a4c.png)


The directionality index is based on the chi-squared test statistic, where the null 
hypothesis is that each bin is equally likely to interact with the regions upstream and 
downstream of it. Bins that show a directional bias have a directionality index 
proportional to the degree of bias, with more biased bins having a higher magnitude of 
directionality index. We use a 40kb bin size and a 2Mb because these parameters 
maximize the reproducibility of the DI and the domain calls while retaining a sufficiently 
high resolution to identify domains and boundary regions. 

To generate a random directionality index, we randomized the direction either 
upstream or downstream of every read pair that mapped to a given bin and calculated the 
directionality index with the randomized directions. Bins with large random 
directionality indexes are virtually absent by chance, with less than 1% of the absolute 
value of random DI being greater than 6.57.

We consider the directionality index as an observation and believe that the “true” 
hidden directionality bias (DB) can be determined using a hidden Markov model (HMM). 
The HMM assumes that the directionality index observations are following a mixture of 
Gaussians and then predicts the states as “Upstream Bias”, “Downstream Bias” or “No 
Bias” (See **Supplementary Figure 28** for a mathematical representation of our Hidden 
Markov Model).

![](https://files.mdnice.com/user/20439/ce9cf894-5bd2-4abc-bae0-de82d234fdd6.png)

Describing the observed directionality index as Y’s [Y1,Y2..Yn], the hidden true 
directionality biases as Q’s [Q1,Q2..Qn] and the mixtures as M’s [M1,M2..Mn]. The 
probability P(Yt|Qt = i,Mt = m) is represented using a mixture of Gaussians for each state i. The Conditional probability distribution [CPDs] of Yt and Mt nodes are,

![](https://files.mdnice.com/user/20439/c2310d70-92cf-4680-988c-3d1894177a1e.png)

 

We used Baum-Welch algorithm [EM] to compute maximum likelihood estimates 
and the parameter estimates of transition and emission (characterized by mean, 
covariance and weights). The posterior marginals were then estimated using the Forward￾backward algorithm.

For each chromosome, we allowed 1 to 20 mixtures and chose the mixture with 
best goodness of fit using the AIC criterion, AIC = 2k – 2ln(L), k is the number of
parameters in the model and L being the maximum likelihood estimate. Matlab was used 
to perform the HMM. 

As a post-processing step, we estimated the median posterior probability of a 
region, defined as a stretch of same state, and believed only in regions having a median 
posterior marginal probabilities ≥ 0.99 or a region that is at least 80kb long.

Domains and boundaries are then inferred from the results of the HMM state calls 
throughout the genome. A domain is initiated at the beginning of a single downstream 
biased HMM state. The domain is continuous throughout any consecutive downstream 
biased states. The domain will then end when the last in a series of upstream biased 
states are reached, with the domain ending at the end of the last HMM upstream biased 
state. We term the regions in between the topological domains as either “topological 
boundaries” or “unorganized chromatin.” We defined unorganized chromatin to be these 
regions that are > 400kb, and the topological boundaries to be less than 400kb. We 
would note that the topological boundaries, though defined as regions less than 400kb, 
are mostly quite small, with 76.33% being less than 50kb in size (mESC data).

---
阅读文章原文：https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3356448/  
补充文件下载地址：https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3356448/bin/NIHMS366885-supplement-1.pdf
