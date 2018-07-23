---
layout:     post
title:      Estimating Discrete Entropy, Part 1
subtitle:   转载自：http://www.nowozin.net/sebastian/blog/estimating-discrete-entropy-part-1.html
date:       2015-02-07
author:     Sebastian Nowozin
header-img: img/background2.jpg
catalog: true
tags:
    - estimation
    - estimates
    - estimators
    - p_k
    - entropy
---

Estimation of the
[entropy](http://en.wikipedia.org/wiki/Entropy_%28information_theory%29) of a
random variable is an important problem that has many applications.
If you can estimate entropy accurately, you can also estimate mutual
information, which allows
you to find dependent random variables in large data sets.
There are numerous
applications.

The setting of discrete entropy estimation with a finite number of outcomes is
as follows.
There is an unknown categorical distribution over \(K \geq 2\) different
outcomes, defined by means of a probability vector
\(\mathbb{p} = (p_1,p_2,\dots,p_K)\), such that \(p_k \geq 0\) and
\(\sum_k p_k = 1\).
We are interested in the quantity

\begin{equation}
H(\mathbb{p}) = -\sum_{k=1}^K p_k \log p_k,\label{eqn:Hdiscrete}
\end{equation}

where \(0 \log 0 = 0\) by convention.

Because the probability vector is unknown to us we cannot directly use
\((\ref{eqn:Hdiscrete})\).
Instead we assume that we observe \(n\) samples \(X_i\), \(i=1,\dots,n\), from the
categorical distribution in order to estimate \(H(\mathbb{p})\).

### Naive Plugin Estimator of the Discrete Entropy

The naive plugin estimator uses the frequency estimates of the categorical
probabilities in the expression for the true entropy, that is,

\begin{equation}
\hat{H}_N = - \sum_{k=1}^K \hat{p}_k \log \hat{p}_k,\label{Hplugin1}
\end{equation}

where \(\hat{p}_k = h_k / n\) are the maximum likelihood estimates of each
probability \(p_k\), and \(h_k = \sum_{i=1}^n 1_{\{X_i = k\}}\) is simply the
histogram over outcomes.
The form \((\ref{Hplugin1})\) is equivalent to the simpler form

$$\hat{H}_N = \log n - \frac{1}{n} \sum_{k=1}^K h_k \log h_k.$$

### Problems of the Naive Plugin Estimator

It has long been known, due to (Basharin,
1959) and (Harris,
1975) that the estimator
\((\ref{Hplugin1})\) underestimates the true entropy \((\ref{eqn:Hdiscrete})\).
In fact, we have for any distribution specified by \(\mathbb{p}\) that

$$H(\mathbb{p}) - \mathbb{E}[\hat{H}_N] =
 \frac{K-1}{2n}
 - \frac{1}{12 n^2} \left(1-\sum_k^{K} \frac{1}{p_k}\right)
 + O(n^{-3}) \geq 0,$$

so that most often the true entropy is at least as large as what \(\hat{H}_N\)
claims it is.
Why is this the case? There is a simple explanation illustrated by
the following figure and description.

![](http://www.nowozin.net/sebastian/blog/images/entropy-estimation-1-bias.svg)


Let us only consider a single bin \(k\) with true probability \(p_k\).
If we would know \(p_k\) exactly, the contribution this bin makes to the true
entropy of the distribution is \(-p_k \log p_k\).
We do not know \(p_k\) and instead estimate it using its frequency estimate
\(\hat{p}_k = h_k / n\). The marginal distribution of \(\hat{p}_k\) is a
[Binomial distribution](http://en.wikipedia.org/wiki/Binomial_distribution).

I have shown an empirical histogram of 50,000 samples from a
\(\textrm{Binomial}(1000,p_k)\) distribution in red, where \(p_k=0.27\) in this
case. As you can see, there is significant sampling variance about the true
\(p_k\), despite having seen 1,000 samples. It is however exactly centered at
\(p_k\) because \(\hat{p}_k\) is an *unbiased* estimate of \(p_k\), that is we have
\(\mathbb{E} \hat{p}_k = p_k\). It also is approximately
normally
distributed,
as can be clearly seen in the Gaussian shape of the red histogram.

When we now evaluate the function \(f(x) = -x \log x\) we evaluate it at the
slightly wrong place \(\hat{p}_k\) instead of the true place \(p_k\).
Because \(f\) is concave in this case, the famous Jensen's
inequality tells us that

$$H = \sum_k f(p_k) = \sum_k f(\mathbb{E} \hat{p}_k) \geq \sum_k \mathbb{E}
f(\hat{p}_k) = \mathbb{E} \sum_k f(\hat{p}_k) = \mathbb{E} H_N,$$

so that for each \(p_k\) the contribution to the entropy is underestimated on
average. (This does not imply that each particular finite sample estimate is
below the true entropy however.)

In the next part we will take a look at some improved estimators of the
discrete entropy.

*Acknowledgements*. I thank [Il Memming Park](http://www.memming.com/) and
[Jonas Peters](http://ei.is.tuebingen.mpg.de/person/jpeters)
for reading a draft version of the article and providing feedback.
