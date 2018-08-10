---
layout:     post
title:      机器学习面试
subtitle:   项目经历
date:       2018-08-10
author:     SunJackson
category:   机器学习
catalog: true
tags:
    - 机器学习
    - word2vec
    - TextRank
    - 数据预处理
    - 特征工程
---

# 主题模型

## 1. 词向量

何为词向量？即对词典 D 中任意词 w，指定一个固定长度的实值向量 V(w)属于 R^m 。则 V(w) 即称为w的词向量，m是词向量的长度。

词向量有两种表现形式：

- One-hot Representation：用维度为字典长度的向量表示一个词，仅一个分量为1，其余为0。缺点是容易收到维度灾难的困扰，而且不能很好的刻画词与词之间的关系。
- Distributed Representation：每个词映射为固定长度的短向量。通过刻画两个向量之间的距离来刻画两个向量之间的相似度。

### 1）word2vec 原理、推导.
word2vec是Google于2013年推出的开源的获取词向量word2vec的工具包。它包括了一组用于word embedding的模型，这些模型通常都是用浅层（两层）神经网络训练词向量。

Word2vec的模型以大规模语料库作为输入，然后生成一个向量空间（通常为几百维）。词典中的每个词都对应了向量空间中的一个独一的向量，而且语料库中拥有共同上下文的词映射到向量空间中的距离会更近。

用一个一层的神经网络(CBOW的本质)把one-hot形式的词向量映射为分布式形式的词向量，通过这种方法，我们可以快速地计算两个词汇的相似程度。

word2vec作为神经概率语言模型的输入，其本身其实是神经概率模型的副产品，是为了通过神经网络学习某个语言模型而产生的中间结果。
- “某个语言模型”指的是：
    - “CBOW”
    - “Skip-gram”。
- 具体学习过程会用到两个降低复杂度的近似方法
    - Hierarchical Softmax
    - Negative Sampling。
    
两个模型乘以两种方法，一共有四种实现。
word2vec 是一种基于神经网络的分布表示方法。与分布表示相对的是符号表示。分布表示还可以通过矩阵分解来计算，比如SVD等，主题模型也可以计算词的压缩表示。word2vec 是对神经概率语言模型等的改进，

### 2）hierarchical softmax 与 negative sampling.

- Hierarchical Softmax
    - 一种对输出层进行优化的策略，输出层从原始模型的利用softmax计算概率值改为了利用Huffman树计算概率值。
    - 本质是把 N 分类问题变成 log(N)次二分类

- Negative Sampling（简写NEG，负采样）
    - Noise-Contrastive Estimation（简写NCE，噪声对比估计）的简化版本：把语料中的一个词串的中心词替换为别的词，构造语料 D 中不存在的词串作为负样本。因此在这种策略下，优化目标变为了：最大化正样本的概率，同时最小化负样本的概率。
    - 本质是预测总体类别的一个子集
    
    
noise contrastive estimatio（NCE，噪声对比估计）是一种对观测数据的概率密度进行估计的方法，通过构造一些分布已知的 noise data，将概率密度的估计问题转化为一个 logistic regression 问题.

### 3）word2vec 与 TFIDF 比较 与 LDA 比较.

- word2vec
    - 用一个一层的神经网络(CBOW的本质)把one-hot形式的词向量映射为分布式形式的词向量，通过这种方法，我们可以快速地计算两个词汇的相似程度。
    
- TFIDF
    - 是一种用于资讯检索与资讯探勘的常用加权技术。TF-IDF是一种统计方法，用以评估一字词对于一个文件集或一个语料库中的其中一份文件的重要程度。字词的重要性随着它在文件中出现的次数成正比增加，但同时会随着它在语料库中出现的频率成反比下降。
- LDA
    - 一种主题模型，它可以将文档集中每篇文档的主题以概率分布的形式给出，从而通过分析一些文档抽取出它们的主题（分布）出来后，便可以根据主题（分布）进行主题聚类或文本分类。同时，它是一种典型的词袋模型，即一篇文档是由一组词构成，词与词之间没有先后顺序的关系。

### 4）word2vec 怎么进行增量式训练.

gensim 可以进行增量式训练，C 版本的好像不行，但都无法改变词表的大小.

### 5）word2vec 目标函数？为什么没加正则

- 对于统计语言模型来说，我们通常构造的目标函数是『最大似然函数』，实际上由于连乘可能导致概率极小，所以经常采用的是『最大对数似然』，
- 加正则的本质是减少数据中的误差对模型的影响。word2vec中输入数据是one hot encoding没有误差所以不用加。

### 6）word2vec 考虑词语的顺序？

不考虑词语顺序

### 7）CBOW 和 skip-gram 对比？为什么 skip-gram + negative sampling 效果好？

- CBOW 和 skip-gram 对比

    - CBOW 模型 
    最大化概率 p(w|context(w))，用 context(w) 去预测 w
    - Skip-gram 模型 
    最大化概率 p(context(w)|w)，用 w 去预测 context(w)
    
### 8）TextRank 原理

TextRank 根据 PageRank 改编而来的算法

- PageRank

    <a><img src="https://latex.codecogs.com/png.latex?$$S(V_i)=(1-d)+d\cdot\sum_{j\in In(V_i)}\frac{1}{|Out(V_j)|}S(V_j)$$" title="PageRank" /></a>

    - <img src="https://latex.codecogs.com/png.latex?$d$"> 是阻尼系数，一般设置为 0.85。
    - <img src="https://latex.codecogs.com/png.latex?$In(V_i)$">  是存在指向网页 
    - <img src="https://latex.codecogs.com/png.latex?$i$"> 的链接的网页集合。
    - <img src="https://latex.codecogs.com/png.latex?$$Out(V_j)$">  是网页 <img src="https://latex.codecogs.com/png.latex?$j$"> 中的链接指向的网页的集合。
    - <img src="https://latex.codecogs.com/png.latex?$$|Out(V_j)|$">  是集合中元素的个数。
    
    PageRank 需要使用上面的公式多次迭代才能得到结果。初始时，可以设置每个网页的重要性为 1。
    
- TextRank

    <a><img src="https://latex.codecogs.com/png.latex?$$WS(V_i)=(1-d)+d\cdot\sum_{V_j\in In(Vi)}\frac{w_{ji}}{\sum_{V_k\in Out(V_j)}w_{jk}}WS(V_j)$$" title="TextRank" /></a>

    - <img src="https://latex.codecogs.com/png.latex?$w_{ij}$"> 就是是为图中节点 <img src="https://latex.codecogs.com/png.latex?$V_i$"> 到 <img src="https://latex.codecogs.com/png.latex?$V_j$"> 的边的权值 。
    - <img src="https://latex.codecogs.com/png.latex?$d$"> 依然为阻尼系数，代表从图中某一节点指向其他任意节点的概率，一般取值为0.85。
    - <img src="https://latex.codecogs.com/png.latex?$In(V_i)$"> 和 <img src="https://latex.codecogs.com/png.latex?$Out(V_i)$"> 也和 PageRank 类似，分别为指向节点 <img src="https://latex.codecogs.com/png.latex?$V_i$"> 的节点集合和从节点 <img src="https://latex.codecogs.com/png.latex?$V_i$">出发的边指向的节点集合。
    
在 TextRank 构建的图中，默认节点就是句子，权值 <img src="https://latex.codecogs.com/png.latex?$w_{ij}$"> 就是两个句子 <img src="https://latex.codecogs.com/png.latex?$S_i$"> 和 <img src="https://latex.codecogs.com/png.latex?$S_j$"> 的相似程度。两个句子的相似度使用下面的公式来计算：

<img src="https://latex.codecogs.com/png.latex?$$Similarity(S_i,S_j)=\frac{|{w_k| w_k \in S_i \& w_j \in S_j}|}{\log(|S_i|)+\log(|S_j|)}$$">

分子是在两个句子中都出现的单词的数量，<img src="https://latex.codecogs.com/png.latex?$$|S_i|$$"> 是句子 i 中的单词数。

使用 TextRank 算法计算图中各节点的得分时，同样需要给图中的节点指定任意的初值，通常都设为1。然后递归计算直到收敛，即图中任意一点的误差率小于给定的极限值时就可以达到收敛，一般该极限值取 0.0001。

#### 使用 TextRank 提取关键词

现在是要提取关键词，如果把单词视作图中的节点（即把单词看成句子），那么所有边的权值都为 0（两个单词没有相似性），所以通常简单地把所有的权值都设为 1。此时算法退化为 PageRank，因而把关键字提取算法称为 PageRank 也不为过。





参考资料

1）来斯惟博士论文：基于神经网络的词和文档语义向量表示方法研究.

2）来斯惟博客

3）[word2vec 中的数学原理详解](http://blog.csdn.net/itplus/article/details/37969519)

4）[noise contrastive estimation 文献](https://yinwenpeng.wordpress.com/2013/09/25/noise-contrastive-estimation/)

5）[噪声对比估计加速词向量训练](http://models.paddlepaddle.org/2017/04/21/nce-cost-README.html)

6）[TFIDF 概率解释](http://www.cnblogs.com/weidagang2046/archive/2012/10/22/tf-idf-from-probabilistic-view.html)


### 2 随机采样方法

参考资料：

1）[从随机过程到马尔可夫链蒙特卡罗方法](http://www.cnblogs.com/daniel-D/p/3388724.html)


### 数据预处理

- 缺失值
    
    - 缺失量少，均值，中位数等填充；
    
    - 缺失量多，离散化；
    
    - 拟合模型填充.

- 异常值

    - 基于统计的方法：3 sigma
    
    - 聚类方法
    
    - isolation forest 等算法.

##### 特征工程

- 连续特征；

- 离散特征；

- 特征组合；

##### 特征选择与特征降维
- 特征选择

过滤式、包裹式、嵌入式

- 特征降维

##### 模型
LR、RF、GBDT、XGBoost

- 为什么 LR 需要归一化或者取对数；为什么 LR 把特征离散化后效果更好？
    - 离散特征的增加和减少都很容易，易于模型的快速迭代；
    - 稀疏向量内积乘法运算速度快，计算结果方便存储，容易扩展
    - 离散化后的特征对异常数据有很强的鲁棒性：比如一个特征是年龄>30是1，否则0。如果特征没有离散化，一个异常数据“年龄300岁”会给模型造成很大的干扰
    - 逻辑回归属于广义线性模型，表达能力受限；单变量离散化为N个后，每个变量有单独的权重，相当于为模型引入了非线性，能够提升模型表达能力，加大拟合
    - 离散化后可以进行特征交叉，由M+N个变量变为M*N个变量，进一步引入非线性，提升表达能力
    - 特征离散化后，模型会更稳定，比如如果对用户年龄离散化，20-30作为一个区间，不会因为一个用户年龄长了一岁就变成一个完全不同的人。当然处于区间相邻处的样本会刚好相反，所以怎么划分区间是门学问
    - 特征离散化以后，起到了简化了逻辑回归模型的作用，降低了模型过拟合的风险。

参考：[连续特征的离散化：在什么情况下将连续的特征离散化之后可以获得更好的效果？](https://www.zhihu.com/question/31989952)

- RF 和 GBDT 的区别
    
    - 组成随机森林的树可以是分类树，也可以是回归树；而GBDT只由回归树组成 
    - 组成随机森林的树可以并行生成；而GBDT只能是串行生成 
    - 对于最终的输出结果而言，随机森林采用多数投票等；而GBDT则是将所有结果累加起来，或者加权累加起来 
    - 随机森林对异常值不敏感，GBDT对异常值非常敏感 
    - 随机森林对训练集一视同仁，GBDT是基于权值的弱分类器的集成 
    - 随机森林是通过减少模型方差提高性能，GBDT是通过减少模型偏差提高性能

- 随机森林的优点： 
    - 容易理解和解释，树可以被可视化。 
    - 不需要太多的数据预处理工作，即不需要进行数据归一化，创造哑变量等操作。 
    - 隐含地创造了多个联合特征，并能够解决非线性问题。 
    - 和决策树模型，GBDT模型相比，随机森林模型不容易过拟合。 
    - 自带out-of-bag (oob)错误评估功能。 
    - 易于并行化。

- 随机森林的缺点： 
    - 不适合小样本，只适合大样本。 
    - 大多数情况下，RF模型的精度略低于GBDT模型的精度。 
    - 适合决策边界是矩形的，不适合对角线型的。

3）GBDT 和 XGBoost 的区别

- XGBoost相对于普通GBDT的实现，可能具有以下的一些优势：

    - 显式地将树模型的复杂度作为正则项加在优化目标
    - 公式推导里用到了二阶导数信息，而普通的GBDT只用到一阶
    - 允许使用column(feature) sampling来防止过拟合，借鉴了Random Forest的思想，sklearn里的gbm好像也有类似实现。
    - 实现了一种分裂节点寻找的近似算法，用于加速和减小内存消耗。
    - 节点分裂算法能自动利用特征的稀疏性。
    - data事先排好序并以block的形式存储，利于并行计算
    - cache-aware, out-of-core computation，这个我不太懂。
    - 支持分布式计算可以运行在MPI，YARN上，得益于底层支持容错的分布式通信框架rabit。

- GBDT和XGBoost区别
    - 传统的GBDT以CART树作为基学习器，XGBoost还支持线性分类器，这个时候XGBoost相当于L1和L2正则化的逻辑斯蒂回归（分类）或者线性回归（回归）；
    - 传统的GBDT在优化的时候只用到一阶导数信息，XGBoost则对代价函数进行了二阶泰勒展开，得到一阶和二阶导数；
    - XGBoost在代价函数中加入了正则项，用于控制模型的复杂度。从权衡方差偏差来看，它降低了模型的方差，使学习出来的模型更加简单，防止过拟合，这也是XGBoost优于传统GBDT的一个特性；
    - shrinkage（缩减），相当于学习速率（XGBoost中的eta）。XGBoost在进行完一次迭代时，会将叶子节点的权值乘上该系数，主要是为了削弱每棵树的影响，让后面有更大的学习空间。（GBDT也有学习速率）；
    - 列抽样。XGBoost借鉴了随机森林的做法，支持列抽样，不仅防止过 拟合，还能减少计算；
    - 对缺失值的处理。对于特征的值有缺失的样本，XGBoost还可以自动 学习出它的分裂方向；
    - XGBoost工具支持并行。Boosting不是一种串行的结构吗?怎么并行 的？注意XGBoost的并行不是tree粒度的并行，XGBoost也是一次迭代完才能进行下一次迭代的（第t次迭代的代价函数里包含了前面t-1次迭代的预测值）。XGBoost的并行是在特征粒度上的。我们知道，决策树的学习最耗时的一个步骤就是对特征的值进行排序（因为要确定最佳分割点），XGBoost在训练之前，预先对数据进行了排序，然后保存为block结构，后面的迭代 中重复地使用这个结构，大大减小计算量。这个block结构也使得并行成为了可能，在进行节点的分裂时，需要计算每个特征的增益，最终选增益最大的那个特征去做分裂，那么各个特征的增益计算就可以开多线程进行。


参考：
1）[陈天奇：XGBoost 与 Boosted Tree](http://www.52cs.org/?p=429)

2）[机器学习算法中 GBDT 和 XGBoost 的区别有哪些](https://www.zhihu.com/question/41354392/answer/128008021?group_id=773629156532445184)

##### 模型组合
平均、bagging、stacking

##### 评测指标
AUC：AUC 的定义和本质，有哪些计算方法.