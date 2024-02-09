Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of five pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 5 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 5 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 5 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 5 times.)
2. Calculate the median age from that sample of pennies.
3. Repeat

Do this a few times with your actual physical pennies, and then once you get the idea, you can make a vector in R of your penny ages and use R to speed up the sampling. Don't forget to sample with replacement.

Gather a collection of 100 median estimates, each one calculated using a different bootstrapped dataset. Calculate the Bias and the Variance of the estimator for the Median.

We now want to take this one step further and estimate the confidence intervals for the median age of a penny in circulation. We actually have two primary mechanisms for generating confidence intervals for the statistic.

**Method #1**: Simply take the quantiles directly from the distribution of $\hat{\theta}^{*}$:

$$
\theta_{LL} = \mbox{2.5th percentile of } \hat{\theta}^{*}
$$
$$
\theta_{UL} = \mbox{97.5th percentile of } \hat{\theta}^{*}
$$

Notice that (by construction) 95$%$ of the $\hat{\theta}^{*}$ values fall in the interval $(\theta_{LL},\theta_{UL})$. <span style="color: orangered;">This is the very definition of the 95th percentile confidence interval.</span>

**OR** 

**Method #2**: We can use the Normal approximation:

We have a second method that won't make 100\% sense until next week, but it turns out that if we assume the bootstrapped estimates follow a Normal distribution, 

$$
\hat{\theta^{*}} \sim N(\hat{\theta},\hat{se}^{2})
$$

we can use the fact that the 95th percentile confidence interval is approximately given by:

$$
\hat{\theta}_{LL}=\hat{\theta}-1.96*\hat{se}
$$
$$
\hat{\theta}_{UL}=\hat{\theta}+1.96*\hat{se}
$$

It turns out that 95$\%$ of the probability for a Standard Normal distribution lies between (-1.96$\sigma$,+1.96$\sigma$). (We will show this more formally next week.) 

NB: If you are going to go through the trouble of doing the bootstrap sampling, I don’t know why you would make a Normal approximation at the very end to construct the CIs. I recommend Method #1.

**<span style="color: green;">Checkpoint #1: Use your penny data to calculate the 95th percentile confidence interval using Method #1 and Method #2. What did you get?</span>**

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #2: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #3: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #4: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

<div class="figure" style="text-align: center">
<img src="Table3categories.png" alt="Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)" width="50%" />
<p class="caption">(\#fig:unnamed-chunk-2)Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Basics of bootstrap and jackknife
------------------------------------

To get started with bootstrap and jackknife techniques, we start by working through a very simple example. First we simulate some data


```r
x<-seq(0,9,by=1)
```

This will constutute our "data". Let's print the result of sampling with replacement to get a sense for it...


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 4 6 7 9 
## 1 1 1 1 3 3
```

Now we will write a little script to take bootstrap samples and calculate the means of each of these bootstrap samples


```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
```

The actual number of bootstrapped samples is arbitrary *at this point* but there are ways of characterizing the precision of the bootstrap (jackknife-after-bootstrap) which might inform the number of bootstrap samples needed. *In practice*, people tend to pick some arbitrary but large number of bootstrap samples because computers are so fast that it is often easy to draw far more samples than are actually needed. When calculation of the statistic is slow (as might be the case if you are using the samples to construct a phylogeny, for example), then you would need to be more concerned with the number of bootstrap samples. 

First, lets just look at a histogram of the bootstrapped means and plot the actual sample mean on the histogram for comparison



```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Calculating bias and standard error
-----------------------------------

From these we can calculate the bias and standard deviation for the mean (which is the "statistic"):

$$
\widehat{Bias_{boot}} = \left(\frac{1}{k}\sum^{k}_{i=1}\theta^{*}_{i}\right)-\hat{\theta}
$$


```r
bias.boot<-mean(xmeans)-mean(x)
bias.boot
```

```
## [1] -0.0031
```

```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=5,col="black")
abline(v=mean(xmeans),lwd=2,col="yellow")
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-7-1.png" width="672" />

$$
\widehat{s.e._{boot}} = \sqrt{\frac{1}{k-1}\sum^{k}_{i=1}(\theta^{*}_{i}-\bar{\theta^{*}})^{2}}
$$


```r
se.boot<-sd(xmeans)
```

We can find the confidence intervals in two ways:

Method #1: Assume the bootstrap statistics are normally distributed


```r
LL.boot<-mean(xmeans)-1.96*se.boot #where did 1.96 come from?
UL.boot<-mean(xmeans)+1.96*se.boot
LL.boot
```

```
## [1] 2.757254
```

```r
UL.boot
```

```
## [1] 6.236546
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
```

Let's compare this to what we would have gotten if we had used normal distribution theory. First we have to calculate the standard error:


```r
se.normal<-sqrt(var(x)/length(x))
LL.normal<-mean(x)-qt(0.975,length(x)-1)*se.normal
UL.normal<-mean(x)+qt(0.975,length(x)-1)*se.normal
LL.normal
```

```
## [1] 2.334149
```

```r
UL.normal
```

```
## [1] 6.665851
```

In this case, the confidence intervals we got from the normal distribution theory are too wide.

**<span style="color: green;">Checkpoint #6: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

There are two packages that provide functions for bootstrapping, 'boot' and 'boostrap'. We will start by using the 'bootstrap' package, which was originally designed for Efron and Tibshirani's monograph on the bootstrap. 

To test the main functionality of the 'bootstrap' package, we will use the data we already have. The 'bootstrap' function requires the input of a user-defined function to calculate the statistic of interest. Here I will write a function that calculates the mean of the input values.


```r
library(bootstrap)
theta<-function(x)
  {
    mean(x)
  }
results<-bootstrap(x=x,nboot=1000,theta=theta)
results
```

```
## $thetastar
##    [1] 4.3 4.4 3.9 5.3 5.1 4.2 5.1 5.5 3.4 5.6 4.8 5.0 4.2 6.7 4.2 6.0 5.1 4.6
##   [19] 4.3 4.4 4.4 3.9 4.2 4.3 3.3 4.0 3.8 6.3 5.3 5.5 4.5 4.3 6.3 5.0 4.2 3.0
##   [37] 3.4 6.3 5.6 4.8 4.7 4.8 4.6 4.8 4.2 4.7 3.7 4.3 4.5 3.0 2.2 3.2 4.4 4.1
##   [55] 4.1 4.8 3.6 4.4 5.0 4.7 5.4 5.6 4.3 3.0 5.7 4.4 3.7 5.5 4.1 5.2 4.8 3.4
##   [73] 4.7 3.2 5.0 5.8 6.2 6.5 3.5 4.8 5.6 4.1 5.5 4.0 4.1 4.0 4.1 5.0 4.7 5.5
##   [91] 5.2 3.8 4.4 5.3 5.0 3.4 5.4 3.9 4.9 5.3 4.3 4.1 4.5 2.1 5.5 3.5 4.0 3.8
##  [109] 4.1 5.1 4.5 6.2 3.2 5.2 4.4 3.4 3.0 5.2 5.6 4.9 5.5 3.4 4.4 4.3 4.3 4.7
##  [127] 4.3 5.9 5.3 5.3 4.9 3.7 4.1 3.4 4.4 5.8 4.9 3.4 6.3 2.8 5.8 3.2 5.0 4.1
##  [145] 4.7 4.9 4.1 3.8 4.3 3.5 2.9 5.6 2.8 3.8 4.4 4.2 3.2 5.3 6.6 5.4 3.4 4.1
##  [163] 4.4 4.5 4.1 4.8 3.4 3.0 4.4 5.2 3.4 4.2 3.2 4.4 3.9 4.0 4.2 4.8 4.3 4.3
##  [181] 4.9 4.9 3.3 4.5 4.9 5.1 4.5 5.3 5.3 4.0 4.9 4.6 5.5 4.8 2.6 4.9 4.0 3.5
##  [199] 3.2 4.9 6.1 4.3 4.3 5.3 5.5 5.2 3.7 3.5 7.0 4.3 5.0 6.6 4.3 3.2 4.5 4.5
##  [217] 3.7 5.4 5.8 3.5 6.2 3.5 4.6 5.8 5.5 4.8 5.6 4.1 4.8 2.8 3.9 4.1 4.4 6.7
##  [235] 3.1 4.8 5.5 4.5 3.8 6.0 3.8 4.8 4.9 5.6 3.2 3.6 5.3 4.3 5.4 3.9 5.5 5.1
##  [253] 5.0 5.6 3.8 4.0 4.4 4.7 5.0 4.0 5.0 3.3 3.6 4.6 4.7 2.5 6.2 5.2 5.8 4.7
##  [271] 3.2 4.7 4.4 5.7 3.6 4.8 4.1 5.2 4.3 5.2 2.9 4.6 4.4 5.0 4.8 4.4 3.8 5.9
##  [289] 5.2 4.7 5.0 3.5 3.6 2.8 6.0 3.7 5.6 4.5 3.8 5.4 4.1 4.7 5.5 3.7 4.7 3.7
##  [307] 5.2 4.5 5.0 4.2 2.9 5.2 2.7 3.3 5.6 6.2 3.6 3.5 3.8 4.1 5.2 4.5 3.9 4.3
##  [325] 4.7 4.7 2.4 5.0 4.4 5.3 3.1 3.0 2.9 4.7 3.1 4.1 4.0 4.3 5.4 5.0 5.0 4.3
##  [343] 4.3 5.0 4.3 4.7 4.6 3.2 5.0 4.1 6.7 4.4 4.6 3.4 4.9 2.8 6.6 3.6 4.8 4.1
##  [361] 4.3 4.5 4.0 5.3 2.5 3.8 4.2 5.8 4.3 6.3 4.3 3.6 4.2 3.7 4.4 6.1 5.6 2.2
##  [379] 4.8 4.8 4.5 3.3 4.4 4.3 6.5 5.7 5.7 4.6 4.5 4.8 3.7 3.7 4.7 4.7 4.6 6.4
##  [397] 3.1 3.5 4.5 3.8 4.8 4.4 5.6 4.2 5.1 3.0 3.0 5.3 5.0 4.8 4.8 5.2 4.5 5.7
##  [415] 3.4 4.3 3.8 4.3 3.8 4.1 3.0 4.5 4.2 3.6 4.5 4.1 3.6 5.7 4.2 5.2 3.4 4.8
##  [433] 3.4 4.7 3.7 4.5 3.7 5.1 3.5 5.7 4.2 4.9 3.1 2.9 5.3 5.1 4.2 3.6 4.9 4.0
##  [451] 5.2 5.9 5.3 4.7 4.6 3.4 5.1 6.1 3.7 4.5 5.9 4.0 4.6 5.0 3.6 4.7 5.3 4.5
##  [469] 5.1 3.0 2.9 3.9 3.7 4.3 5.6 4.5 4.1 5.2 5.5 4.1 2.3 2.6 4.5 4.5 2.9 5.7
##  [487] 3.6 3.9 3.7 4.6 4.2 4.8 4.3 4.6 5.4 4.8 6.1 5.1 3.9 4.3 4.4 3.6 4.7 1.8
##  [505] 5.1 5.1 4.3 4.3 4.5 5.7 3.8 4.8 4.2 4.8 4.7 5.8 5.6 4.9 4.3 5.0 4.2 4.1
##  [523] 3.4 5.2 5.2 5.4 5.4 4.0 4.8 4.6 5.4 5.9 3.3 5.1 5.8 3.0 3.5 2.8 5.8 5.0
##  [541] 3.5 4.7 3.0 4.6 4.4 3.3 5.2 4.3 3.6 4.6 4.8 5.9 5.1 5.3 5.4 4.8 5.9 2.9
##  [559] 3.6 5.0 4.3 4.6 5.0 4.8 4.9 5.8 4.4 3.1 3.6 4.7 4.4 4.1 4.7 3.6 3.4 4.2
##  [577] 4.4 4.1 5.9 4.3 3.5 3.3 3.0 6.4 4.0 5.2 5.4 5.4 5.3 3.8 5.2 3.1 4.0 4.5
##  [595] 4.3 4.7 4.5 4.1 5.2 5.6 5.7 6.0 2.8 4.2 4.3 3.9 4.9 5.9 4.9 5.1 4.4 5.9
##  [613] 4.2 5.3 4.3 4.8 3.4 4.2 5.9 6.0 5.4 5.5 3.6 4.8 4.7 3.7 6.0 4.7 5.7 3.4
##  [631] 3.4 4.5 4.9 4.9 5.5 3.9 4.0 4.4 3.5 3.9 5.3 5.4 5.0 2.9 4.4 3.7 4.8 4.1
##  [649] 3.8 6.4 4.6 5.5 5.4 3.4 5.3 4.5 4.3 5.4 4.8 3.8 5.2 5.4 3.6 5.4 4.2 4.4
##  [667] 6.3 5.2 4.8 4.4 4.0 4.5 4.6 4.6 4.8 5.0 5.5 5.5 3.1 3.2 4.8 4.7 5.9 5.0
##  [685] 4.5 3.6 6.8 2.5 4.3 4.1 5.1 5.1 5.9 5.0 4.5 4.1 5.6 2.9 1.4 5.4 5.9 4.4
##  [703] 3.9 4.1 5.9 4.5 3.9 6.7 3.6 4.4 4.7 3.6 3.9 3.4 4.6 4.9 4.6 4.9 2.9 4.6
##  [721] 5.3 4.2 5.5 3.2 5.5 4.1 4.8 2.8 2.5 4.0 5.8 5.2 5.9 4.6 3.8 5.6 3.8 2.9
##  [739] 3.7 4.2 4.7 4.1 4.1 5.4 4.4 4.0 3.9 5.5 4.9 6.6 5.1 5.3 5.3 5.1 4.2 3.9
##  [757] 4.7 5.2 5.9 3.2 4.2 5.3 4.4 3.3 3.7 5.0 3.2 4.9 6.2 3.5 7.5 5.6 5.4 4.8
##  [775] 4.9 2.3 5.1 2.7 3.7 5.1 4.3 5.6 5.4 4.0 5.8 3.5 2.8 5.7 4.3 2.9 5.2 3.9
##  [793] 4.8 5.0 2.6 4.3 4.7 3.8 4.2 4.6 3.4 4.9 4.4 3.9 5.6 3.8 6.1 4.4 3.6 4.9
##  [811] 3.4 5.2 5.7 5.1 4.5 4.3 5.2 4.7 3.6 5.9 3.1 5.3 4.4 4.6 4.6 4.2 4.6 4.7
##  [829] 6.1 3.4 4.4 4.0 4.0 5.3 5.0 3.9 3.5 6.0 3.6 4.6 3.9 4.4 4.3 5.3 4.5 3.5
##  [847] 4.9 4.7 5.9 4.6 2.7 5.0 4.0 3.6 3.1 6.9 4.1 5.3 5.5 3.6 4.8 4.5 3.3 4.7
##  [865] 5.1 4.8 4.6 4.3 3.9 5.3 4.0 4.7 4.2 4.5 3.6 3.4 4.6 4.6 4.8 4.8 6.8 4.7
##  [883] 6.4 3.4 5.7 4.0 6.3 5.0 4.0 3.7 5.2 3.9 3.9 3.1 5.5 4.1 4.7 6.5 5.9 4.3
##  [901] 5.6 3.8 4.1 5.6 3.4 5.6 3.6 4.2 5.5 4.1 4.0 3.5 4.8 4.8 5.0 3.8 3.0 4.7
##  [919] 6.0 5.0 4.0 5.1 4.1 4.4 3.9 4.2 3.5 4.6 3.8 3.8 3.3 4.6 3.9 4.4 4.1 4.9
##  [937] 4.0 3.6 3.0 5.0 2.9 4.2 4.9 4.6 3.7 4.8 3.1 4.2 4.3 5.1 5.5 4.5 3.7 4.5
##  [955] 5.6 6.4 4.1 3.0 5.1 4.1 3.8 3.1 5.5 4.8 3.2 5.0 2.8 4.6 5.3 4.4 3.7 4.1
##  [973] 3.1 4.3 3.3 4.9 3.4 3.3 5.2 4.7 4.6 4.5 3.8 4.9 4.4 3.5 5.0 5.2 4.6 2.9
##  [991] 5.0 6.9 4.3 4.6 3.7 6.7 5.3 3.7 4.8 5.0
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta)
```

```r
quantile(results$thetastar,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.3
```

Notice that we get exactly what we got last time. This illustrates an important point, which is that the bootstrap functions are often no easier to use than something you could write yourself.

You can also define a function of the bootstrapped statistics (we have been calling this theta) to pull out immediately any summary statistics you are interested in from the bootstrapped thetas.

Here I will write a function that calculates the bias of my estimate of the mean (which is 4.5 [i.e. the mean of the number 0,1,2,3,4,5,6,7,8,9])


```r
bias<-function(x)
  {
  mean(x)-4.5
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=bias)
results
```

```
## $thetastar
##    [1] 4.5 3.4 4.2 6.1 3.8 4.3 5.7 3.9 4.1 4.6 5.5 5.0 5.6 3.3 5.1 3.2 3.9 6.1
##   [19] 3.8 5.1 4.8 4.2 4.7 3.8 1.7 4.4 4.2 3.5 2.8 5.3 3.3 6.5 5.2 4.3 3.9 3.2
##   [37] 5.1 5.5 3.8 3.9 3.7 5.0 5.5 6.1 4.8 4.1 4.0 4.6 5.8 4.3 4.2 5.4 3.2 3.9
##   [55] 5.1 5.6 6.1 6.2 5.5 4.5 4.3 3.5 3.0 4.4 5.2 3.8 4.2 3.7 3.5 3.5 6.0 3.8
##   [73] 4.5 4.8 3.8 4.7 3.1 4.2 4.6 5.9 4.1 5.5 3.5 4.1 6.9 6.0 4.5 5.9 5.2 4.9
##   [91] 4.5 4.6 4.4 2.9 5.3 4.1 5.5 5.4 2.4 4.2 3.4 2.8 4.5 5.3 4.2 4.0 4.1 4.8
##  [109] 4.4 4.7 3.4 4.0 5.4 5.4 5.0 5.3 4.5 4.3 5.8 4.4 3.5 2.7 5.4 2.3 3.8 3.9
##  [127] 4.7 4.1 5.1 5.6 5.5 4.0 4.5 4.6 3.6 6.0 4.7 3.8 4.4 3.7 3.5 4.8 3.5 4.7
##  [145] 4.1 4.6 3.6 5.0 4.6 4.7 4.7 5.2 4.8 4.0 5.2 3.8 4.5 4.4 4.7 4.3 4.5 3.3
##  [163] 4.8 4.7 4.1 4.2 4.6 5.1 4.0 3.9 4.5 4.2 3.7 2.9 4.2 6.8 4.7 6.5 5.4 4.8
##  [181] 2.8 2.6 3.5 2.9 4.6 4.2 4.3 4.5 4.5 5.1 4.6 5.5 5.1 3.6 4.8 4.3 4.0 5.7
##  [199] 4.8 4.3 4.0 4.4 3.2 6.9 3.7 4.2 5.5 4.0 5.5 3.1 5.1 4.6 4.6 4.1 3.9 5.0
##  [217] 3.6 5.4 4.2 3.9 1.8 5.8 4.7 2.9 5.1 4.0 4.1 5.5 4.2 2.5 4.4 5.3 3.5 4.8
##  [235] 5.9 5.6 5.1 5.6 5.0 5.4 5.2 4.1 6.7 5.1 4.0 3.2 4.7 3.9 5.0 4.7 5.0 4.2
##  [253] 5.2 5.0 3.1 4.8 3.5 5.6 4.6 4.5 4.4 4.5 3.4 3.8 4.4 5.1 5.0 3.4 6.3 2.7
##  [271] 4.6 3.9 3.8 5.0 2.2 5.1 5.7 3.3 3.3 4.0 4.7 5.2 4.4 3.4 4.0 5.2 4.7 3.4
##  [289] 5.6 5.4 4.4 4.0 3.5 4.3 4.7 5.7 4.5 3.9 4.4 3.5 4.8 4.0 4.9 5.0 5.8 4.7
##  [307] 4.3 3.7 3.9 4.8 3.6 3.3 3.2 4.9 3.8 4.1 5.1 4.9 4.4 2.8 3.9 3.5 4.3 3.4
##  [325] 5.5 6.4 3.9 4.5 5.1 3.2 4.3 4.5 6.4 4.2 4.2 4.5 5.0 4.6 2.8 3.7 5.1 4.1
##  [343] 4.6 4.9 5.9 5.0 4.2 3.9 4.0 4.8 4.8 4.3 3.7 4.9 4.1 5.8 4.2 2.9 3.4 3.8
##  [361] 5.5 3.4 4.6 2.8 1.9 4.1 4.8 3.2 5.6 5.3 5.0 5.0 2.2 2.9 5.7 5.4 4.4 5.0
##  [379] 5.9 5.2 4.0 6.2 4.1 5.6 4.5 3.3 3.9 6.2 3.9 4.6 4.4 4.9 5.7 3.2 5.7 4.1
##  [397] 5.2 5.0 4.2 4.1 5.9 4.3 2.3 4.3 5.4 4.9 3.5 5.6 5.2 5.2 4.8 5.2 3.5 6.1
##  [415] 4.8 4.6 4.6 4.8 2.6 3.8 3.9 5.5 6.4 4.3 4.7 4.8 3.1 5.4 5.0 4.4 4.2 3.4
##  [433] 4.6 4.1 4.1 3.2 4.7 4.8 5.7 4.9 6.6 5.6 3.2 3.1 5.2 4.9 6.3 4.9 3.1 4.8
##  [451] 3.2 4.7 3.4 3.0 3.8 6.1 3.6 5.0 3.1 5.0 4.8 5.9 4.9 4.9 4.7 6.3 4.1 5.1
##  [469] 4.1 4.8 5.3 4.6 3.3 4.8 4.8 4.6 3.5 3.0 5.4 4.0 4.1 5.6 4.4 3.8 5.7 2.9
##  [487] 3.0 5.6 3.3 3.5 4.6 4.2 5.8 5.0 4.1 4.3 5.6 4.7 4.9 5.0 4.6 3.3 5.2 3.6
##  [505] 5.0 4.9 5.4 2.9 3.2 5.6 5.4 5.5 3.7 5.8 5.4 6.0 2.8 5.4 3.0 5.2 5.5 3.4
##  [523] 5.1 5.4 5.6 4.2 4.9 4.7 5.1 4.0 4.7 3.7 4.1 6.0 5.4 4.3 2.7 5.9 4.0 6.2
##  [541] 3.7 3.1 4.8 4.3 4.0 3.6 5.1 4.3 4.9 5.7 5.5 3.6 2.9 5.4 2.7 4.9 3.9 4.8
##  [559] 4.7 4.6 4.0 4.1 6.5 4.0 5.3 3.5 5.0 4.6 5.4 4.9 4.2 4.4 2.7 4.4 4.8 4.3
##  [577] 4.9 3.4 4.7 4.7 4.2 5.9 5.3 4.1 5.3 5.9 5.0 4.7 5.2 5.1 2.5 4.3 5.6 3.0
##  [595] 3.5 5.4 4.2 3.1 6.0 3.5 5.2 4.5 6.2 3.7 6.1 4.6 4.6 3.7 6.1 5.5 1.6 4.1
##  [613] 4.0 5.5 4.5 4.6 4.3 5.4 3.5 5.0 5.0 5.6 5.0 4.5 3.9 3.3 3.2 4.6 5.1 4.5
##  [631] 4.3 5.5 5.0 4.4 4.1 5.4 5.1 6.3 3.7 5.5 5.3 4.5 3.5 5.4 4.9 3.3 4.3 5.2
##  [649] 4.8 4.2 3.9 5.2 5.2 3.9 4.8 3.7 4.3 5.3 3.4 2.3 4.3 5.1 4.4 3.9 5.3 3.2
##  [667] 6.0 4.6 4.6 4.8 4.6 4.2 3.9 3.5 5.3 5.1 4.4 5.9 2.8 4.1 3.7 4.2 4.7 3.3
##  [685] 4.5 4.6 5.5 5.9 3.7 4.4 4.1 4.0 3.8 4.2 2.9 3.6 4.9 4.8 3.8 3.4 4.3 6.0
##  [703] 6.0 4.9 2.0 5.8 4.4 3.7 4.6 3.5 2.9 4.1 4.1 3.1 2.1 4.3 3.8 5.3 4.0 5.1
##  [721] 5.6 3.6 4.4 4.5 3.3 4.7 3.3 5.4 4.9 6.5 5.3 4.6 4.3 4.5 5.4 4.7 5.7 4.2
##  [739] 3.9 4.7 3.4 5.6 4.5 5.7 4.3 3.5 4.3 6.2 3.1 5.2 4.9 4.6 4.2 3.4 6.0 4.8
##  [757] 3.9 4.4 3.4 2.1 4.2 3.2 3.3 4.1 5.6 4.2 6.4 4.2 3.7 3.7 4.8 4.5 6.0 5.7
##  [775] 5.4 4.1 4.7 4.4 6.1 4.9 3.5 4.4 4.5 5.6 4.4 3.9 3.7 3.8 4.4 6.6 5.7 3.8
##  [793] 3.9 3.8 4.5 4.4 4.5 5.4 4.2 4.8 4.4 4.2 3.1 4.6 4.3 3.2 3.8 4.4 4.7 5.3
##  [811] 3.5 3.8 4.7 4.3 4.5 4.2 3.5 4.8 2.8 5.1 5.1 4.3 5.1 3.3 3.9 5.7 3.2 3.9
##  [829] 4.6 3.4 4.9 4.8 4.6 4.4 5.7 5.3 6.0 4.5 3.6 4.7 3.5 5.5 4.1 3.7 3.1 4.1
##  [847] 6.0 3.5 6.1 4.8 6.1 3.4 4.3 3.3 5.4 2.5 3.5 4.4 5.1 3.3 7.1 5.4 4.9 4.1
##  [865] 4.5 4.6 6.3 4.7 4.5 5.4 2.5 4.4 4.4 4.3 2.9 4.2 3.7 4.5 4.7 4.1 4.5 4.5
##  [883] 4.2 4.1 4.2 5.1 5.7 4.5 2.9 2.8 5.4 3.4 6.5 3.4 4.1 3.9 5.6 5.2 6.0 6.4
##  [901] 3.9 3.0 5.4 5.2 5.3 5.1 4.3 5.6 4.0 4.9 3.7 4.7 4.5 5.9 5.6 5.0 3.8 4.2
##  [919] 5.9 5.3 4.0 5.6 4.2 4.8 5.4 2.1 5.1 5.2 4.5 3.0 5.1 6.3 6.3 4.7 4.5 4.9
##  [937] 5.2 4.1 4.3 3.5 4.1 4.3 5.8 4.2 5.8 6.0 2.8 5.4 4.1 2.7 5.1 4.4 5.5 6.2
##  [955] 5.0 3.1 3.7 4.6 3.9 5.0 6.1 3.5 4.0 3.7 3.6 5.8 5.3 5.0 3.7 3.8 4.0 3.5
##  [973] 4.0 4.2 5.0 6.1 5.0 4.3 4.9 4.0 4.6 4.9 5.5 5.1 3.3 3.9 4.4 3.3 4.1 2.7
##  [991] 3.7 2.2 3.1 5.2 6.1 3.7 4.7 3.5 4.5 5.9
## 
## $func.thetastar
## [1] -0.0174
## 
## $jack.boot.val
##  [1]  0.50026385  0.40553846  0.28184438  0.08885714  0.11876833 -0.12513966
##  [7] -0.15800000 -0.37093750 -0.43472222 -0.52756598
## 
## $jack.boot.se
## [1] 1.020354
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = bias)
```

Compare this to 'bias.boot' (our result from above). Why might it not be the same? Try running the same section of code several times. See how the value of the bias ($func.thetastar) jumps around? We should not be surprised by this because we can look at the jackknife-after-bootstrap estimate of the standard error of the function (in this case, that function is the bias) and we can see that it is not so small that we wouldn't expect some variation in these values.

Remember, everything we have discussed today are estimates. The statistic as applied to your data will change with new data, as will the standard error, the confidence intervals - everything! All of these values have sampling distributions and are subject to change if you repeated the procedure with new data.

Note that we can calculate any function of $\theta^{*}$. A simple example would be the 72nd percentile:


```r
perc72<-function(x)
  {
  quantile(x,probs=c(0.72))
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=perc72)
results
```

```
## $thetastar
##    [1] 3.3 3.8 3.5 4.1 4.7 4.8 4.5 3.1 5.7 4.7 5.1 3.3 3.6 4.2 4.1 5.9 5.7 5.1
##   [19] 3.9 5.1 4.8 4.2 3.9 4.5 4.3 3.4 4.5 3.2 2.9 3.1 5.5 4.1 5.3 3.5 6.8 7.0
##   [37] 4.3 4.5 4.8 4.3 4.6 5.9 4.2 4.6 2.9 3.5 4.4 4.7 4.5 4.7 5.5 5.7 5.5 4.9
##   [55] 4.7 2.7 4.1 3.8 5.1 3.9 3.1 4.1 4.7 4.2 5.3 5.5 2.3 4.8 4.7 3.6 3.2 4.8
##   [73] 3.8 3.8 5.2 3.7 5.2 6.1 4.7 4.2 4.7 5.1 3.4 5.9 5.2 4.5 3.3 4.9 3.7 3.8
##   [91] 5.7 3.2 4.8 4.5 3.4 5.2 5.1 4.5 4.9 4.2 3.9 5.1 5.1 5.2 4.7 5.8 5.1 5.0
##  [109] 4.8 5.5 5.6 4.2 4.4 3.7 5.0 5.1 4.8 5.1 4.5 5.1 3.8 5.1 4.8 5.1 5.6 1.9
##  [127] 4.5 5.1 3.2 5.2 4.8 4.3 4.0 4.5 4.7 5.5 4.7 3.8 4.6 3.0 2.6 5.9 5.5 4.4
##  [145] 4.8 4.3 5.0 3.8 4.7 5.4 3.3 4.8 4.5 4.1 3.1 5.2 4.1 3.5 4.7 5.1 4.9 4.6
##  [163] 4.8 4.4 3.8 4.3 5.6 4.3 6.2 3.0 3.1 3.2 5.6 5.1 4.1 4.9 3.8 4.3 5.8 3.8
##  [181] 3.3 4.5 4.4 5.1 3.5 7.1 5.3 4.2 3.9 5.8 3.1 3.0 3.7 5.3 3.8 5.0 4.6 5.4
##  [199] 4.8 5.0 5.1 5.2 5.5 3.1 4.7 4.0 5.1 5.4 4.3 2.7 5.4 3.9 4.7 3.4 4.8 4.0
##  [217] 2.9 3.5 2.5 5.0 5.5 4.5 5.3 4.8 6.9 3.4 5.2 4.7 5.1 4.2 3.9 3.8 5.3 3.8
##  [235] 3.7 3.8 3.4 4.4 4.7 5.6 2.9 4.9 5.4 4.9 4.8 4.8 5.3 4.3 4.1 5.4 5.2 4.0
##  [253] 3.3 4.9 4.4 6.4 5.4 4.7 4.0 6.4 4.1 3.9 6.6 4.7 4.1 3.4 6.2 3.7 4.9 4.1
##  [271] 5.5 3.8 4.7 4.4 5.4 4.1 4.9 3.4 6.4 4.3 4.7 5.1 4.2 6.4 4.6 6.4 4.8 5.5
##  [289] 4.2 3.7 5.2 5.4 4.5 5.3 4.7 4.3 4.0 4.3 5.8 5.1 4.1 6.4 4.5 5.7 4.5 5.5
##  [307] 5.0 4.2 4.9 3.5 5.2 4.6 5.2 4.5 4.2 3.6 5.2 5.3 3.9 3.7 5.1 4.0 3.7 4.8
##  [325] 5.1 5.8 4.9 3.5 4.2 5.1 4.9 3.6 5.0 2.9 1.6 4.5 4.8 3.3 3.7 4.4 5.1 3.8
##  [343] 3.5 2.3 4.4 5.9 5.4 5.4 3.9 3.3 4.8 4.7 5.7 5.5 3.6 4.7 4.8 4.3 5.3 3.8
##  [361] 3.1 3.6 3.9 3.3 3.8 5.3 6.0 4.8 3.8 4.7 4.8 5.7 4.3 5.1 4.3 5.4 5.0 5.3
##  [379] 5.5 4.6 5.4 5.2 4.1 3.5 3.7 4.0 5.3 6.1 5.7 4.5 5.6 5.9 6.0 6.3 4.2 3.0
##  [397] 4.7 4.2 3.2 6.0 5.1 4.9 2.7 4.5 3.4 6.1 3.6 3.6 4.8 4.3 4.6 3.0 3.7 4.0
##  [415] 4.1 5.2 3.2 3.8 5.0 5.4 4.4 3.9 4.5 4.4 4.4 5.6 4.9 4.3 5.3 4.2 4.9 6.5
##  [433] 4.3 3.5 3.6 3.4 3.6 4.6 3.8 4.9 5.0 3.5 5.3 4.5 5.4 5.2 4.1 5.2 4.8 3.3
##  [451] 4.7 5.0 6.3 2.6 4.8 3.0 5.3 4.1 5.5 3.6 3.6 5.8 4.4 5.3 4.2 5.2 5.4 6.2
##  [469] 4.7 3.9 4.8 6.1 5.1 4.0 6.4 2.8 3.2 4.4 4.6 4.9 3.2 5.8 6.6 4.0 3.5 3.8
##  [487] 6.8 5.8 4.7 3.7 2.6 4.7 3.7 4.5 3.5 6.0 6.1 2.9 5.2 2.9 4.7 3.8 4.4 3.6
##  [505] 4.2 3.3 5.4 5.4 5.4 5.5 5.5 5.2 4.4 4.4 3.5 5.4 4.9 3.6 4.9 4.8 5.5 5.0
##  [523] 4.3 5.5 4.0 5.1 3.4 5.0 3.1 3.3 5.4 3.3 5.1 3.9 4.4 6.5 4.5 5.4 5.0 4.6
##  [541] 4.9 3.7 5.9 3.7 4.7 4.5 4.5 4.4 5.1 4.8 3.7 5.0 4.7 4.4 4.2 5.0 2.6 4.2
##  [559] 4.0 6.5 4.0 3.6 4.8 3.9 5.7 3.8 4.1 3.5 4.2 4.0 4.4 4.5 3.0 3.0 4.8 5.0
##  [577] 2.7 5.4 5.6 5.5 4.1 3.5 3.0 1.5 4.4 6.6 5.6 3.7 4.8 5.1 4.3 6.1 5.4 5.0
##  [595] 6.2 4.2 4.8 5.2 4.7 4.5 4.9 3.7 5.0 3.9 6.2 5.2 5.4 4.5 4.7 3.5 4.6 5.2
##  [613] 5.1 3.6 5.1 3.7 5.9 3.8 3.6 5.4 5.5 4.2 5.0 7.0 3.5 4.6 5.6 3.7 3.4 3.2
##  [631] 3.3 5.0 3.6 4.4 4.7 4.0 5.7 4.5 3.9 5.1 4.1 4.8 3.2 4.4 4.3 4.2 5.4 4.4
##  [649] 4.0 4.1 3.3 4.6 4.9 4.2 5.8 5.1 4.2 4.3 3.3 4.2 4.4 6.2 5.6 5.2 5.0 4.2
##  [667] 4.2 5.1 5.2 4.6 3.9 4.0 4.3 5.9 4.0 4.2 5.6 4.4 4.4 3.3 3.6 5.3 4.8 4.9
##  [685] 3.3 4.3 4.2 5.0 5.5 3.7 4.8 3.3 5.7 3.8 3.2 4.5 4.6 4.9 4.9 3.6 3.0 3.1
##  [703] 5.4 3.2 4.5 3.0 5.5 5.3 3.9 5.0 3.8 3.0 5.3 5.6 6.2 4.6 3.8 5.2 6.4 4.6
##  [721] 5.2 3.5 5.0 5.6 5.6 4.2 4.5 5.7 3.5 6.9 4.4 6.6 3.5 4.3 3.9 4.8 5.6 4.2
##  [739] 5.6 3.9 3.5 4.3 5.8 5.0 5.2 5.7 3.9 6.0 4.3 3.5 4.9 4.7 5.1 4.6 7.0 3.6
##  [757] 4.9 5.5 5.1 2.7 3.9 4.3 3.2 3.8 4.0 4.0 5.2 3.2 4.1 5.6 4.3 3.6 4.3 4.2
##  [775] 4.4 2.6 3.4 4.7 3.5 3.5 3.4 4.6 2.8 4.4 4.6 6.0 4.7 3.9 3.0 5.0 6.7 4.1
##  [793] 5.1 3.6 4.4 5.4 3.4 2.5 4.3 4.8 4.9 5.6 4.2 5.3 3.4 3.7 4.8 5.1 4.3 5.1
##  [811] 3.8 4.5 3.9 3.7 5.1 4.6 3.4 5.0 3.9 4.4 4.5 6.6 3.5 3.6 4.6 3.5 4.7 5.5
##  [829] 3.4 3.5 4.3 5.1 4.2 5.3 4.1 4.2 4.6 4.6 5.9 3.8 4.6 4.6 5.2 4.5 5.0 4.3
##  [847] 4.4 4.8 4.1 4.6 2.1 5.4 3.5 4.1 3.7 5.7 3.9 6.5 5.4 4.1 3.5 5.6 5.1 4.5
##  [865] 3.5 4.1 5.1 4.1 4.0 3.2 4.5 5.8 5.6 3.5 3.2 3.8 5.3 4.4 4.9 5.5 5.1 4.0
##  [883] 5.9 5.9 4.4 3.2 3.9 2.8 3.7 6.1 4.8 4.9 5.2 3.7 5.6 5.0 4.4 2.5 4.9 4.8
##  [901] 5.3 5.3 3.2 5.9 2.9 3.8 4.5 4.9 4.2 4.6 4.0 4.0 4.9 5.3 4.7 6.0 4.0 5.3
##  [919] 4.3 4.4 4.6 5.4 3.5 4.1 4.6 4.0 5.4 2.3 4.9 4.7 5.9 5.4 4.6 4.5 4.9 5.3
##  [937] 5.2 3.4 5.8 5.7 2.6 5.7 3.3 5.5 6.2 4.8 3.2 4.2 2.2 5.6 4.1 3.8 5.7 6.6
##  [955] 3.4 4.3 3.2 3.2 4.1 4.3 3.6 4.1 5.6 5.2 4.7 3.4 4.4 3.6 4.1 5.5 3.4 5.9
##  [973] 5.3 3.2 4.8 5.1 5.5 6.1 5.3 3.6 5.0 3.8 4.4 4.7 5.4 5.3 5.9 3.4 6.3 6.3
##  [991] 4.6 4.6 3.4 4.5 3.4 4.5 5.4 4.2 4.5 4.7
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.300 5.300 5.200 5.100 4.948 4.800 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9724414
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = perc72)
```

On Tuesday we went over an example in which we bootstrapped the correlation coefficient between LSAT scores and GPA. To do that, we sampled pairs of (LSAT,GPA) data with replacement. Here is a little script that would do something like that using (X,Y) data that are independently drawn from the normal distribution


```r
xdata<-matrix(rnorm(30),ncol=2)
```

Everyone's data is going to be different. With such a small sample size, it would be easy to get a positive or negative correlation by random change, but on average across everyone's datasets, there should be zero correlation because the two columns are drawn independently.


```r
n<-15
theta<-function(x,xdata)
  {
  cor(xdata[x,1],xdata[x,2])
  }
results<-bootstrap(x=1:n,nboot=50,theta=theta,xdata=xdata) 
#NB: xdata is passed to the theta function, not needed for bootstrap function itself
```

Notice the parameters that get passed to the 'bootstrap' function are: (1) the indexes which will be sampled with replacement. This is different that the raw data but the end result is the same because both the indices and the raw data get passed to the function 'theta' (2) the number of bootrapped samples (in this case 50) (3) the function to calculate the statistic (4) the raw data.

Lets look at a histogram of the bootstrapped statistics $\theta^{*}$ and draw a vertical line for the statistic as applied to the original data.


```r
hist(results$thetastar,breaks=30,col="pink")
abline(v=cor(xdata[,1],xdata[,2]),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Parametric bootstrap
---------------------

Let's do one quick example of a parametric bootstrap. We haven't introduced distributions yet (except for the Gaussian, or Normal, distribution, which is the most familiar), so lets spend a few minutes exploring the Gamma distribution, just so we have it to work with for testing out parametric bootstrap. All we need to know is that the Gamma distribution is a continuous, non-negative distribution that takes two parameters, which we call "shape" and "rate". Lets plot a few examples just to see what a Gamma distribution looks like. (Note that the Gamma distribution can be parameterized by "shape" and "rate" OR by "shape" and "scale", where "scale" is just 1/"rate". R will allow you to use either (shape,rate) or (shape,scale) as long as you specify which you are providing.

<img src="Week-2-lab_files/figure-html/unnamed-chunk-18-1.png" width="672" />


Let's generate some fairly sparse data from a Gamma distribution


```r
original.data<-rgamma(10,3,5)
```

and calculate the skew of the data using the R function 'skewness' from the 'moments' package. 


```r
library(moments)
theta<-skewness(original.data)
head(theta)
```

```
## [1] 0.01306053
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##    6.733595   12.305590 
##  ( 2.939778) ( 5.577790)
```

Now lets sample with replacement from this new distribution and calculate the skewness at each step:


```r
results<-c()
for (i in 1:1000)
  {
  x.star<-rgamma(length(original.data),shape=fit$estimate[1],rate=fit$estimate[2])
  results<-c(results,skewness(x.star))
  }
head(results)
```

```
## [1] 1.2939105 0.9365332 0.4423664 0.1364160 0.8951711 0.6253627
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-22-1.png" width="672" />

Now we have the bootstrap distribution for skewness (the $\theta^{*}$ s), we can compare that to the equivalent non-parametric bootstrap:


```r
results2<-bootstrap(x=original.data,nboot=1000,theta=skewness)
results2
```

```
## $thetastar
##    [1] -0.0261732070 -0.0243718089  0.4176750121 -0.3410291786 -0.2024678486
##    [6]  0.3892211643 -0.3336514451  0.3005390763  0.1942910779 -0.2812657319
##   [11]  0.3675581906 -0.0327120604 -0.1883098439  0.0835800451 -0.5408680886
##   [16] -0.0832934267  0.4511982802  0.0508500250 -0.1376141252  0.2491852448
##   [21] -0.3415348650  0.0992278338  0.4872254846  0.0135162016  0.5956566882
##   [26]  0.3139464742  0.7332676396  0.3820620288 -0.3514662213 -0.0210124933
##   [31]  0.8016344544 -0.2840312383  0.0673655315 -0.0109234641  0.1325020487
##   [36]  0.3276373818 -0.0279691373  0.0326064670  0.0388381552  0.4851941030
##   [41]  0.2401830277  0.4586217358  0.5364261344 -0.6501689183 -0.2879134218
##   [46]  1.3342217253 -0.0686648182  0.4109407703 -0.9399287091 -0.1370099334
##   [51]  0.0026960987  0.4079203429 -0.0074234367 -0.2437572791  0.6841493842
##   [56] -0.0158960473 -0.6275994532 -0.1978524756 -0.3766695498 -0.4242547680
##   [61]  0.0580409095  0.4362910480 -0.3287114022  0.7206259434 -0.1587010118
##   [66]  0.0300141952  0.4170213309 -0.6023148337  0.1292788061 -0.4088696845
##   [71]  0.5514951513 -0.4331960761  0.0532251555  0.1468900709 -0.8147583143
##   [76]  0.5733274530  0.1944474071 -0.3126724753 -0.5112044572 -0.1316717045
##   [81] -0.0294888394 -0.1026907160  0.6872948919 -0.2145630700 -0.1946624123
##   [86] -0.0625854284 -0.1840037327 -0.2640424926 -0.6151801356  0.8952458212
##   [91] -0.9276920421 -0.0950312490 -0.3479214231  0.1223948461 -0.0615135748
##   [96]  0.3863408605  0.2894754683 -0.2548197595 -0.1381883114 -0.8632069563
##  [101]  0.3569785976 -0.2318796316  0.4104711429 -0.3719254359  0.2830511051
##  [106] -0.1866053431  0.9675482935 -0.0996990365 -0.1408456274 -0.3848067223
##  [111]  0.1971537925  0.9791272772 -0.3894279506 -0.4396616905 -0.7422672310
##  [116]  0.4065207808  0.1666646936 -0.1054786314 -0.0278486168  0.0173471781
##  [121]  0.2528402651 -1.5487954398 -0.1590138897 -0.0002142887  0.4583932807
##  [126] -0.8594235038  0.1402478118 -0.2122395419 -0.0192639886 -0.0273552235
##  [131]  0.0028287645  0.3656333497  0.4220604960 -0.2244115413  0.3071933256
##  [136] -0.4803207167  0.0152772482 -0.0508032216  0.7487530208 -0.7347281466
##  [141] -0.1633529408  0.0746030894  0.3706225138 -0.2994517293 -0.2829904502
##  [146]  0.4202919230 -0.0462640590  0.2279043406  0.2216810684 -0.0799914167
##  [151]  1.2248871408  0.1341892453 -0.1635396895 -0.0889028144 -0.3362846912
##  [156]  0.2491714832 -0.3651017251 -0.1829536774 -0.1764916325  0.5853534711
##  [161]  0.2159666763 -0.0702822103  0.0689970551 -0.9335348155 -0.7009192495
##  [166] -0.8334085596  0.2487321816 -0.0383697769  0.8889799050 -0.5529692899
##  [171] -0.2553472543  0.1661526889  0.7969620697  0.9050139588 -1.3494151325
##  [176]  0.4661347100 -0.3679950389  0.2886572694  0.0035861170 -0.3542156539
##  [181] -0.0573607812 -0.0785494572 -0.6827009890 -0.1098585966  1.1926551286
##  [186]  0.0575444441  0.3259620028  1.3650975256  0.6270869151  0.3337963807
##  [191]  0.0465364756 -0.1511488417 -0.1616771969 -0.5279974918  0.4344259041
##  [196] -0.2711166599 -0.2206105551  0.4397042572  0.1186871929  0.1316919744
##  [201] -0.1936520815 -0.3921591820 -0.0577525098 -0.6801760045 -0.8237928421
##  [206]  0.2915043625 -0.0395879206 -0.0241042307  0.2384985599  0.4940426257
##  [211] -0.1999985554  0.0938517245  0.4214907701  0.2470803872 -0.7300385290
##  [216] -0.0852465756 -0.1687890065  0.6002017923  0.1636995994 -0.3550618324
##  [221]  0.7302816786 -0.6868395580 -0.0295802045 -0.4725489058  0.1789439124
##  [226] -0.4744032083  0.5432112648 -0.8329402922 -0.2273434879  0.2107583827
##  [231] -0.0063218379 -0.5377992817 -0.0172296345 -1.2761305863 -0.1394480542
##  [236] -0.3688596504 -0.5208495440  0.1658935464  0.5456273051  0.7726283535
##  [241] -0.2118003116  0.4056945878 -0.4905762914  0.4053953624 -0.6639587580
##  [246] -0.3468861220 -0.4394833741 -0.8692937175  0.3577541974 -0.2674504968
##  [251] -0.0250565691 -0.2932307261  0.8134184661  0.1746434354 -0.0464995357
##  [256]  0.5296299402 -0.1832066644 -0.7148136640 -0.5927397220  0.5351377385
##  [261] -0.5424248336 -0.4310501087  0.3724700775 -0.1071186875  0.0941261215
##  [266] -0.2998726708 -0.1980661367  1.1650567304  0.2022711378  0.3335073105
##  [271]  0.1216044415 -0.9876554229 -0.0393062890  0.8829797726  0.0209160329
##  [276]  0.5260261954 -0.5157404853 -1.3719039684  0.8544371591 -0.0568340480
##  [281] -0.3047004507 -0.6996955403 -0.8011186015 -0.0965101875  0.2565453560
##  [286] -0.0813816905 -0.2601632817  0.1750524514  0.5977631443 -0.4327609579
##  [291]  0.8970192200 -0.4277796148 -0.2786921125  0.7839711221  0.0738714881
##  [296]  0.2184298237  0.3355400190 -0.0522796567 -0.2692516232  0.2793395821
##  [301]  0.1083690398  1.5076923983  0.5974808823  0.1705044774 -0.5076350897
##  [306] -0.5639918222 -0.5435638977 -0.5528390243  0.0866192473 -0.1235781312
##  [311]  0.5995101367 -0.3910590340  0.1932875530 -0.2377938104 -0.5253789562
##  [316]  0.3097431086 -0.5462880246 -1.3238349915 -0.0508032216  0.6070708722
##  [321]  1.0019662312 -0.2335282081 -0.3963865887 -0.0954448009  0.5940416677
##  [326] -0.0261732070  0.4374035672  0.5872691016 -0.0715124566  0.0557958159
##  [331] -0.4228722117  0.0274749523 -0.0196571312  0.2317900637  0.1699272123
##  [336] -0.5800555738  0.4201904167  0.8929857251 -0.4134362223 -0.1029534085
##  [341]  0.8224369854  0.0305545787  0.2246579265 -0.3602038343  0.5795262709
##  [346]  0.8093892978  0.3611459939 -0.3854181109  0.0691559972  0.3373505675
##  [351] -0.3418562160  0.0059655302  0.3866841852  0.3405858363 -1.1723366294
##  [356] -0.0569404124 -1.6167748395 -0.0544437500 -0.4633675256  1.1868741608
##  [361]  0.5952753812 -0.8406071752 -0.5611829186  0.0648749352 -0.3157239469
##  [366]  0.5869325007  0.5547540782  0.7482643855 -0.2051664241 -0.1519604421
##  [371]  0.1657519270 -0.2307577912 -0.2449720342 -0.1792978611  0.5712344811
##  [376] -0.7784503585 -0.2405343628 -0.1280834930  0.1217055011  0.2517289897
##  [381]  0.0462534797 -0.0496547289 -0.1878449810 -0.3521276408 -0.1978524756
##  [386] -0.1965478068  0.7158741660  0.3183048382  1.0431389699  0.5887530238
##  [391] -0.6408512913  0.0113121818 -0.0889018441 -0.0124213116  0.2867482452
##  [396]  0.5837689105 -0.2832086066 -0.7714434159  0.3841304456 -0.0577525098
##  [401]  0.2248506748  0.8795478609  0.2850065926 -0.4515447737 -0.5234222076
##  [406] -1.2519050291 -0.3083483566 -0.2208800047  1.6873696539  0.0531907478
##  [411] -0.2003278545  0.5452044672  0.9182177872 -0.3576260426 -0.1836655944
##  [416] -0.4226976957 -0.1993244015  0.5051876767  0.4387015166  0.2333889500
##  [421] -0.0666878018  0.4364076713 -0.3853961270 -0.3731922017  0.6927588949
##  [426]  0.8784834736  0.0407525729  0.5357448756 -0.0134561163 -0.3780810907
##  [431]  1.2573751591 -0.4585458868 -0.3293551644  0.1571026350  0.1524674079
##  [436] -0.3733595355 -0.2445366933  0.1070689112  0.5143061122  0.1463371030
##  [441] -0.5838814216  0.2938432326  0.4945278275 -0.2401471559 -0.5303075577
##  [446] -0.0826757198 -1.1061967968  0.0222572690  0.5769314913  0.3563004082
##  [451]  0.0132462639  0.2097025840 -0.5214576137 -0.2362469212  0.6867528629
##  [456]  0.0056973837 -0.1548045334 -0.1678125875  1.0321759811 -0.3190747664
##  [461]  0.0734683246  0.3597181013  0.7424300108  0.0477728116 -0.1503052734
##  [466]  0.4317843111  0.6519081518  0.2400505632 -0.1780013668  0.2892153854
##  [471] -0.7643826632 -0.2236301373  0.6608160517 -0.1826869888  0.2299588679
##  [476]  0.1057894216  0.0368358351 -0.0399065093 -0.2359048889  0.5221885963
##  [481]  0.1411213537  0.7060605385  0.0961481010  0.4328387756  0.4628988603
##  [486] -0.1720841398  1.0039994373 -0.0682224376  0.1059774753  0.5450367838
##  [491]  0.3365374052 -0.1999536113  0.1133732677 -0.4257627409  0.8955422469
##  [496]  0.2124459422  0.5509498811  0.2925406345 -0.8839874852  0.1390077888
##  [501]  0.4368112217 -0.9747940782 -0.1974933495  0.0901239988 -0.6900578032
##  [506]  0.6740894820 -0.2020507732  0.1808628288  0.3144551044 -0.1537664412
##  [511] -0.2393217349 -0.3090190402  0.1437590704 -0.0973305182  0.0402864944
##  [516] -0.3805697694  0.9574795933 -0.3316399531 -0.4324266730 -1.3772606108
##  [521] -0.6576690450 -0.0189802149  0.3156355653  0.0462310249  0.3680692233
##  [526] -1.0857397064  0.2789057076  0.5891782690 -0.0653795822 -0.0248671751
##  [531]  0.0519085822  0.2073615780  0.8544371591  0.6539332006 -0.3907859526
##  [536] -0.3385132906 -0.3990843378 -0.5594261730  0.3437861667  0.3894841051
##  [541]  0.2646642147  0.0174968512 -0.2796691847 -0.2201236319  0.4286054777
##  [546] -0.5778297498  0.1056890214  0.0132473752  0.1786678944 -0.7530275689
##  [551] -0.2810647185  0.0387033861  0.2272895088 -0.0813100681 -0.2336439133
##  [556]  0.2916480833  0.1440104397 -0.2941539989 -0.5852572200 -0.4151474984
##  [561]  0.2930844908 -0.1920035516  0.3563094082  0.5443345004  0.1318957422
##  [566] -0.1964071705  0.1460777503  0.5322397675 -0.3041397980 -0.1642740956
##  [571] -0.2805916697  0.5344127767 -0.8568574304  0.0533027393 -0.0840163125
##  [576] -0.0860681247 -0.3740537517  0.2533724388  0.1076449748  0.2806810742
##  [581]  1.1605106201  0.2934352807 -0.0457465229 -0.0497904196 -0.0278486168
##  [586]  0.0245815149 -0.1941035299  0.0925560162 -0.4332600519  0.2967506566
##  [591] -0.8064441018  0.3437223010 -0.9695209830  0.2898532607 -0.4598948424
##  [596]  0.3858671097  0.2438806839 -0.1337846070 -0.2964447357  1.1335305866
##  [601] -0.2982259418  0.6203790615 -0.0785494572  0.4003544383 -0.4384110331
##  [606] -0.1535936012 -0.0080396112  0.8259685436  0.0250544717  0.3472212188
##  [611] -0.1352513478 -0.1833432865 -0.6514175893  0.0282151528 -0.3158361200
##  [616]  0.2053901667 -0.2162377742  0.2479759055  0.3951847334 -0.5959067146
##  [621] -0.5880813928 -0.1759554665  0.1491405978  0.8216581999 -0.1013507830
##  [626]  0.1855874923 -0.4457187023 -0.8447697779  0.4480936053  0.0400639035
##  [631]  0.7597957987  0.5489895490  0.5550023004  0.0512804899 -0.2056759067
##  [636] -0.0723066472 -0.3046409445  0.1605676996  0.8562881901 -0.4198716041
##  [641] -0.7411301918  0.2329832001 -0.4644455071  0.1133732677  0.6472072465
##  [646] -0.2756877042  0.2759957548 -0.6066855475 -0.1128697915  0.3902400449
##  [651] -0.0362308025 -0.0060101237  0.1986998089  0.1041015042  0.1728589970
##  [656] -0.8812668331  0.0219535658  0.8646200264  0.2594002942  0.1605676996
##  [661]  0.0471113160 -0.1737644662  0.8774212238  0.4350704415 -0.0117482286
##  [666]  0.3410821434  0.1100632178  1.0916435801  0.5046611652  0.0631988219
##  [671]  0.1481417087 -0.1778246757  0.9680606164  0.0561425504 -0.0366512635
##  [676] -1.0613611052  0.1079069604 -0.2242728659 -0.0311860650  0.0645410819
##  [681]  0.4758265301 -0.2630361109  0.5457251357 -0.1348301177  0.1451867644
##  [686] -0.0394562168  0.2582044410  0.0523902920  0.1375070145 -0.3094877152
##  [691] -0.3423387817 -0.1768562257 -0.1905526885 -0.0168472275  0.5392916167
##  [696] -0.6498929236 -0.3105151876 -0.2844868172 -0.6282709176 -0.1637702069
##  [701]  0.4201361875 -0.7986091392 -0.3188126666 -0.3178115265 -0.3497900422
##  [706]  0.0896937042  0.1405243753  0.0374638490 -0.1996264487 -0.7298329414
##  [711] -1.2740450041 -0.3084287799 -0.3895108966 -0.8403055683 -0.1196078695
##  [716]  0.2363333668 -0.4686406455  0.1757555334  0.1397747084  0.1566009663
##  [721]  0.5355682549 -0.1460926154  0.2000794965  0.5410257138  0.0023976505
##  [726]  0.2704338908  0.1532813193 -0.1424823251  0.1881789433  0.5543790945
##  [731]  0.5150761487  1.4489557157 -0.5492557943 -0.1998759924  0.8276198844
##  [736]  0.4921429334  0.4951794648  0.3361926276  0.2658097711  0.4521440606
##  [741] -0.0609296957  0.0713376117 -0.8964865770 -1.1143932267 -0.5786107297
##  [746]  0.0164813335 -0.4356970488 -0.9668469819  0.1649520384 -0.3274649719
##  [751] -0.2394103339  0.5793450295  0.1316547054  0.3674553757  0.1262251131
##  [756]  0.1135544271  0.7082164963  0.1479588964 -0.2530782085 -0.4813035585
##  [761] -0.7346862064  0.3144942163 -1.2327910799  0.2028362984  1.4064584691
##  [766] -0.6476600431 -0.0642292367 -0.0172563842 -0.0395762936 -0.1011928860
##  [771] -0.1786176219 -0.2950646002 -1.0022628989  1.0339908266  0.0414672412
##  [776]  0.5067928626  0.2122791870 -0.1725046289  0.3242270090 -0.5676702502
##  [781] -0.2040487510 -0.0710613520 -0.2398391420 -0.8434044720 -0.0053773566
##  [786]  0.4776035153  0.6375623744  0.1235435461 -0.4924182882  0.0420237641
##  [791]  0.2985316978 -0.0120274864 -0.6021349947  1.1938258761 -0.5222172157
##  [796]  0.2982947245  0.0065426596  0.2675240431  0.8129640601 -0.6490670168
##  [801]  0.2455535469  0.6640503862  0.1044733678  0.0341090089 -0.3224049271
##  [806] -0.0189802149 -0.9129522034  0.2011644503  0.4511982802  0.0346304275
##  [811] -0.0261703304  0.0539253205  0.6970438271 -0.0915150293 -0.0971297907
##  [816] -0.5688201862 -0.1000035960  0.0559902357  0.1170949919 -0.5560985733
##  [821] -0.3605533930 -0.3099906076  0.1409235512  0.1433224840 -0.4070392548
##  [826]  1.2351722970 -0.3612889128 -0.0152391169  0.9839664127 -0.5018632579
##  [831] -0.2305555178  0.1607882769  0.7252451840 -0.5908435253 -1.0510641471
##  [836]  0.3951475712  0.7226857144  0.1311133183  0.1491976660  0.1728090845
##  [841] -0.3654461859  0.6915679177  0.2344758236 -1.1317981543  0.8027066320
##  [846]  0.6788113816 -0.7891389716  0.4349931553  0.2564220632  0.2049952313
##  [851]  0.2713305033 -0.2487573862  0.1481779774  1.3308043160  0.3102533068
##  [856] -0.2867763369  0.5492688090  0.1206957090 -0.2978387335  0.9993125560
##  [861]  0.2262051758 -0.1332901159  0.0819279553 -0.2760715024  0.1973955435
##  [866]  0.1301408312 -1.4036623973  0.2283190016 -0.1079554787 -0.0201878411
##  [871] -0.0642292367  0.5555856848 -0.2829841315 -0.1922596400 -0.2400766934
##  [876] -0.2178880548  0.7459076352 -0.7430545451 -1.5618545394  0.6338690892
##  [881]  0.1105182456 -0.2389585006 -0.8377461655 -0.0230210546 -0.1176339166
##  [886]  0.2564818430  0.3296224401  0.2949563251  0.9910327744  0.5045907075
##  [891] -0.1617615938  0.5113772742 -0.0324703105 -0.3728579242 -0.0522796567
##  [896]  0.0487651145 -0.2725202151 -0.3250907962  0.0006554523 -0.3088850073
##  [901] -0.3015481588  0.1748860304 -0.0617974015 -0.1826869888  0.3234783914
##  [906]  1.0963738815  0.7014121382  0.1824715368 -0.6582737685  0.4390281359
##  [911]  0.0133845657  0.7322845120  0.1387694421 -0.2101033232  0.1505127469
##  [916] -0.0847601301  0.3930787034  0.2132142359  0.5634091258 -0.0052191987
##  [921] -0.3137700065  0.6631331110  0.0180719756 -0.7770604664 -0.3325173410
##  [926] -0.9213728982 -0.2958318090 -0.4993867298 -0.7418370880  0.0450640186
##  [931] -0.3053548528 -0.7933195627 -0.2432759317  0.2677184573 -0.5771194150
##  [936]  0.4421500356 -0.3280663843 -0.2702701028 -0.5237460510  0.4908926417
##  [941] -0.4230140244 -0.3683584766 -0.8451504611 -0.1653324064  0.5147083624
##  [946]  0.2373159060 -0.0650342279  1.1359590701 -0.2785801502  0.1777360645
##  [951]  0.3577890953  1.0230602066 -0.0046467978  0.0444363925 -1.0574372633
##  [956] -0.1272284209 -0.3858093564  0.2977673771  0.0237278180  0.3553405257
##  [961]  0.4951794648 -1.2869957843  0.0926184274 -0.5285153111 -0.2073883641
##  [966] -0.1917237180  0.8631381545  0.9868804473  0.1595487778  0.0693860645
##  [971]  0.4124280279  0.6436676441 -0.3276239897  0.5046611652 -0.0177659650
##  [976] -0.4699963692 -0.2644485030  0.4267655686  0.1109477961  0.2265327023
##  [981] -0.2304736989  0.3714861218 -0.2178057512  0.6785276734  0.8347147705
##  [986] -2.5507556836  0.6941712384 -0.0132188879 -0.6163886640  0.4844275157
##  [991]  0.5362061443 -0.0816885523 -0.1943851804 -0.0442010280 -0.6578149233
##  [996]  0.3113394374 -0.6010758412  0.0766643443  0.5589383381 -0.0162948875
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = original.data, nboot = 1000, theta = skewness)
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-23-1.png" width="672" />

What would have happened if we would have fit a normal distribution instead of a gamma distribution?


```r
fit2<-fitdistr(original.data,dnorm,start=list(mean=1,sd=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.54719466   0.19815528 
##  (0.06266220) (0.04430337)
```

```r
results.norm<-c()
for (i in 1:1000)
  {
  x.star<-rnorm(length(original.data),mean=fit2$estimate[1],sd=fit2$estimate[2])
  results.norm<-c(results.norm,skewness(x.star))
  }
head(results.norm)
```

```
## [1] -0.311324131 -0.110404484 -0.103277465 -0.009884397 -0.055259264
## [6]  0.020259665
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results.norm,breaks=30,col="lightgreen",freq=F,add=T)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-24-1.png" width="672" />

All three methods (two parametric and one non-parametric) really do give different distributions for the bootstrapped statistic, so the choice of which method is best depends a lot on the situation, how much data you have, and what you might already know about the underlying distribution.

Jackknifing is just as easy at bootstrapping. Here we will do a trivial example for illustration. We will write a little function for the mean even though you could put the function in directly with 'jackknife(x,mean)'


```r
theta<-function(x)
  {
  mean(x)
  }
x<-seq(0,9,by=1)
results<-jackknife(x=x,theta=theta)
results
```

```
## $jack.se
## [1] 0.9574271
## 
## $jack.bias
## [1] 0
## 
## $jack.values
##  [1] 5.000000 4.888889 4.777778 4.666667 4.555556 4.444444 4.333333 4.222222
##  [9] 4.111111 4.000000
## 
## $call
## jackknife(x = x, theta = theta)
```

**<span style="color: green;">Checkpoint #7: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] 0.0101
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9262969
```


Everything we have done to this point used the R package 'bootstrap' - now lets compare that with the R package 'boot'. To avoid any confusion (a.k.a. masking) between the two packages, I recommend detaching the bootstrap package from the workspace with


```r
detach("package:bootstrap")
```


The 'boot' package is now recommended over the 'bootstrap' package, but they give the same answers and to some extent it is personal preference which one prefers to use.

We will still use the mean as the statistic of interest, but we will have to write a new function for it because the syntax of the 'boot' package is slightly different:


```r
library(boot)
theta<-function(x,index)
  {
  mean(x[index])
  }
boot(x,theta,R=999)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = x, statistic = theta, R = 999)
## 
## 
## Bootstrap Statistics :
##     original     bias    std. error
## t1*      4.5 0.01591592   0.9475509
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 6 7 8 9 
## 2 3 3 2
```

```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
mean(x)
```

```
## [1] 4.5
```

```r
bias<-mean(xmeans)-mean(x)
se.boot<-sd(xmeans)
bias
```

```
## [1] -0.0357
```

```r
se.boot
```

```
## [1] 0.9060665
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

