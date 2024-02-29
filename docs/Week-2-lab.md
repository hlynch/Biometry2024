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
## 0 2 4 5 6 8 
## 1 3 2 2 1 1
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
## [1] -0.0019
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
## [1] 2.738316
```

```r
UL.boot
```

```
## [1] 6.257884
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.2025
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
##    [1] 5.4 3.1 3.1 6.1 6.3 5.3 5.6 5.8 3.3 2.6 4.8 6.0 5.0 4.9 4.4 4.6 4.7 5.5
##   [19] 4.9 5.8 4.7 3.9 3.6 3.5 4.1 3.6 4.1 3.9 4.2 3.6 4.8 4.3 5.0 4.3 2.5 3.7
##   [37] 5.0 3.0 4.2 4.2 4.9 5.0 5.6 4.3 3.2 5.6 4.4 4.8 4.5 4.7 5.7 4.9 5.4 5.1
##   [55] 3.1 5.8 5.0 4.6 5.2 3.7 5.3 3.3 3.4 3.7 3.6 4.7 4.1 5.1 3.6 2.7 4.8 4.2
##   [73] 4.9 2.3 5.4 3.6 5.3 3.4 5.7 3.4 3.8 3.9 4.6 4.2 6.0 4.2 2.9 5.1 5.6 4.8
##   [91] 3.5 6.2 4.1 4.1 4.2 4.7 5.8 5.2 5.7 5.3 4.8 3.5 6.1 6.1 4.1 4.2 4.4 4.2
##  [109] 4.3 4.7 4.5 7.1 3.3 5.0 4.9 1.7 3.8 5.7 4.1 2.9 4.9 4.1 5.4 5.2 5.6 3.9
##  [127] 4.6 4.8 5.2 3.4 5.3 4.9 5.6 5.2 6.3 4.2 3.8 4.5 4.6 6.3 4.4 4.3 4.4 5.4
##  [145] 6.2 4.4 3.4 4.0 5.8 5.0 5.0 5.4 6.4 3.8 4.6 4.4 4.5 5.1 3.6 4.8 4.3 4.1
##  [163] 3.9 2.6 3.3 4.9 4.3 2.6 3.3 2.5 3.8 3.8 4.0 5.4 4.6 3.6 6.2 4.2 4.2 4.8
##  [181] 5.4 4.2 4.2 4.6 4.2 5.3 3.4 5.9 5.6 5.0 4.3 4.2 5.3 5.6 4.3 3.3 4.7 3.2
##  [199] 4.5 4.1 4.5 5.7 4.4 3.7 4.0 5.4 4.7 4.5 5.2 4.4 5.0 3.7 5.2 4.3 4.2 3.3
##  [217] 4.5 5.5 4.6 3.3 4.4 4.2 4.4 3.5 5.2 5.8 3.0 5.2 5.2 6.2 4.3 4.0 3.6 4.6
##  [235] 3.9 5.0 4.5 3.1 4.8 4.6 5.7 4.3 4.3 4.8 5.1 4.0 3.8 5.7 4.8 2.7 4.2 4.7
##  [253] 3.4 3.8 5.6 5.1 4.5 3.9 3.4 5.1 5.2 3.9 3.5 3.6 4.5 4.1 3.8 4.1 4.1 3.8
##  [271] 5.2 5.5 5.2 4.4 5.5 5.9 3.5 3.4 4.6 3.2 5.1 4.9 4.7 3.8 4.7 3.6 4.0 5.4
##  [289] 5.8 4.6 4.1 5.8 4.5 6.1 3.5 3.4 3.6 3.7 4.1 4.7 5.7 4.8 4.8 3.3 4.8 4.5
##  [307] 4.9 5.6 2.7 4.4 6.0 6.3 5.8 4.6 2.9 4.0 3.3 4.0 3.7 5.2 4.8 5.4 5.9 5.2
##  [325] 3.4 3.9 3.6 5.5 4.3 2.8 4.4 4.0 3.8 6.1 5.2 4.8 4.9 3.4 4.9 4.8 4.6 4.4
##  [343] 4.5 3.3 4.4 4.7 5.7 3.7 5.7 5.0 4.1 4.3 2.9 6.0 2.4 4.1 3.2 3.2 5.1 4.6
##  [361] 5.4 6.3 4.6 3.8 3.5 4.2 4.5 3.8 4.8 2.6 4.5 4.0 3.9 4.1 3.8 4.9 4.9 3.5
##  [379] 5.1 4.6 5.9 3.0 4.5 3.8 3.9 5.0 3.8 6.5 3.2 4.3 6.0 5.1 3.4 4.8 3.4 5.2
##  [397] 5.2 4.7 3.4 3.9 5.3 4.6 4.6 5.3 3.1 6.0 3.7 4.8 5.7 6.1 5.7 2.0 4.0 3.0
##  [415] 3.2 5.3 4.8 4.2 4.4 4.3 4.6 5.6 6.1 5.6 6.3 5.3 3.9 4.0 4.0 3.9 3.9 3.8
##  [433] 4.6 4.9 4.7 6.2 3.8 4.4 6.1 4.0 3.8 3.9 4.6 4.4 4.3 4.4 4.0 3.7 2.9 3.9
##  [451] 4.3 5.0 2.9 4.6 5.0 5.7 6.9 4.7 3.5 4.7 5.4 3.8 4.4 3.2 4.8 6.2 5.3 6.2
##  [469] 5.0 3.7 5.8 4.3 4.9 4.5 4.8 6.4 4.6 5.3 4.1 3.8 4.0 4.2 4.2 4.3 5.4 4.6
##  [487] 5.1 3.4 4.2 2.5 3.9 3.2 5.4 3.5 4.5 4.8 5.7 4.5 4.3 4.3 4.9 4.5 4.0 4.6
##  [505] 3.9 5.7 5.0 4.7 2.1 2.9 2.8 5.4 4.6 3.0 4.5 4.4 4.4 2.9 5.0 4.2 4.7 6.2
##  [523] 6.1 5.1 4.6 5.7 5.3 5.5 4.6 3.7 4.9 5.1 2.9 4.1 4.8 4.0 5.6 4.1 5.5 4.5
##  [541] 5.2 3.6 5.4 3.5 3.3 4.0 5.2 3.6 5.1 4.1 4.6 5.1 3.9 5.9 5.9 4.5 2.8 5.0
##  [559] 3.2 4.0 5.4 3.2 5.0 4.1 5.7 6.9 3.7 6.6 4.6 5.1 5.3 5.1 3.7 4.8 4.8 5.4
##  [577] 4.7 5.4 4.6 2.6 4.2 4.8 3.9 5.6 3.7 4.2 4.5 4.6 5.1 5.1 3.7 5.0 4.9 5.9
##  [595] 4.3 4.8 5.6 3.6 3.6 5.1 2.5 4.1 5.2 4.2 4.0 4.6 5.9 6.2 4.0 4.6 5.0 7.1
##  [613] 3.4 4.5 2.7 2.6 5.1 3.8 2.8 3.3 3.8 4.3 2.7 5.9 3.4 5.0 5.8 5.2 4.8 4.8
##  [631] 3.8 5.1 5.4 4.1 5.1 5.2 4.4 5.1 4.8 3.5 4.1 3.5 4.2 5.7 5.4 4.4 2.9 3.5
##  [649] 5.1 3.2 5.5 3.4 5.0 3.6 4.7 4.9 5.5 5.7 5.7 5.0 4.5 4.8 4.4 5.8 6.5 4.0
##  [667] 5.4 3.9 4.6 4.2 4.5 4.7 4.9 3.7 5.9 4.3 4.8 3.8 4.5 3.4 5.8 5.0 3.0 3.9
##  [685] 4.8 3.6 3.4 4.5 6.0 4.3 4.4 3.5 4.2 5.3 4.1 4.8 4.8 6.3 3.4 4.3 4.0 5.2
##  [703] 5.0 6.3 2.7 3.5 4.5 5.5 4.5 4.5 4.8 4.4 3.8 3.5 3.6 2.4 4.3 4.8 4.8 3.7
##  [721] 2.6 4.1 4.8 4.5 4.8 5.6 3.8 5.4 6.2 5.1 3.8 4.1 4.7 5.0 5.7 4.8 4.3 4.6
##  [739] 5.3 5.5 4.8 4.3 3.6 6.1 5.1 5.1 5.6 3.8 5.6 3.8 5.0 4.1 2.3 4.8 4.2 5.3
##  [757] 4.9 5.0 5.2 5.0 5.3 3.6 4.5 4.5 3.8 4.7 5.0 3.7 4.0 3.4 5.2 3.4 4.5 3.9
##  [775] 5.5 6.7 4.8 3.5 4.8 5.7 6.1 6.0 4.8 5.0 6.3 3.0 3.5 5.8 5.8 5.2 4.2 5.6
##  [793] 3.8 4.0 4.4 5.5 5.0 5.7 3.5 3.1 4.3 4.1 4.1 5.1 5.5 6.2 5.1 2.8 3.8 3.5
##  [811] 5.5 4.7 3.6 6.8 2.4 5.7 2.8 2.2 6.1 4.2 4.3 3.6 4.6 3.9 6.0 5.8 5.4 4.5
##  [829] 5.0 4.7 5.8 4.7 5.0 4.0 4.8 4.5 4.8 5.1 4.4 4.4 4.4 3.6 4.1 3.3 3.3 4.0
##  [847] 5.8 3.3 4.8 4.1 4.3 4.6 3.9 4.6 5.2 4.4 3.0 3.7 3.4 5.3 5.2 5.4 4.1 4.4
##  [865] 4.0 3.1 5.7 2.8 6.3 5.0 2.4 3.4 5.2 5.1 5.9 5.0 2.4 3.1 4.9 4.3 4.2 3.9
##  [883] 5.6 4.3 4.6 5.0 4.3 2.6 4.5 3.2 4.1 4.3 4.2 4.8 4.4 4.6 5.1 4.7 4.7 4.2
##  [901] 3.9 4.6 3.3 4.9 4.5 4.8 3.7 4.8 4.8 4.0 4.4 5.2 4.2 4.8 3.6 5.4 4.7 5.8
##  [919] 5.0 4.7 4.7 4.5 4.3 5.7 4.4 3.1 5.2 4.6 4.4 3.2 5.0 3.6 4.4 4.5 4.9 5.3
##  [937] 4.7 4.1 4.5 6.2 4.0 4.3 4.0 5.6 3.9 4.6 3.7 5.8 3.1 3.6 4.2 4.4 5.7 4.7
##  [955] 6.3 4.7 4.0 4.7 5.3 3.8 4.0 5.0 5.0 3.8 4.1 4.9 5.2 4.5 5.6 6.2 3.4 4.4
##  [973] 4.2 4.3 5.1 4.4 4.2 5.0 3.6 4.7 3.4 3.2 4.2 3.8 4.0 5.5 4.7 3.5 4.4 5.4
##  [991] 5.0 4.2 4.4 5.0 3.4 4.6 4.0 6.1 4.5 4.5
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
##   2.7   6.2
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
##    [1] 6.2 4.3 4.4 4.4 2.5 5.2 6.4 6.9 5.0 3.1 4.6 3.2 3.5 3.0 5.6 5.8 4.3 4.4
##   [19] 4.4 3.5 4.6 4.5 4.8 3.5 3.4 4.6 5.5 5.6 4.2 6.5 5.9 3.7 5.1 4.1 5.7 4.2
##   [37] 4.2 4.2 2.9 4.1 4.3 4.7 5.6 5.5 4.7 3.5 4.5 4.5 5.3 5.1 4.3 4.5 4.5 6.4
##   [55] 3.7 4.7 4.6 4.6 5.4 5.7 3.8 4.0 3.2 4.1 4.8 3.8 6.0 5.6 4.5 4.2 3.2 5.0
##   [73] 3.9 3.2 4.1 4.2 3.3 4.2 4.8 4.6 3.7 5.5 6.9 5.7 4.0 6.8 5.4 5.4 4.2 4.4
##   [91] 4.8 3.2 5.0 4.6 3.7 4.4 6.1 5.9 3.0 4.3 3.3 6.3 4.1 3.9 4.7 4.3 5.6 3.9
##  [109] 5.7 4.9 4.1 5.3 6.2 4.4 5.4 3.7 5.9 5.5 4.8 6.1 4.4 5.4 5.2 4.6 3.2 5.6
##  [127] 4.8 5.4 4.6 5.1 3.2 5.0 3.2 4.1 4.3 5.1 4.1 2.7 5.9 5.9 3.1 4.5 5.8 3.9
##  [145] 4.9 3.7 6.8 3.8 5.0 6.3 5.6 5.5 4.6 4.7 4.8 4.7 3.5 4.3 3.8 2.3 5.5 4.5
##  [163] 4.6 3.5 5.2 4.5 4.2 5.5 4.8 4.6 5.9 4.1 4.7 3.9 3.3 4.7 4.5 3.8 5.7 5.4
##  [181] 4.3 4.9 3.7 3.6 4.4 4.2 4.7 3.7 5.5 5.9 3.5 4.5 5.1 4.2 3.2 5.6 4.6 4.0
##  [199] 3.2 5.2 4.3 6.0 3.0 5.6 6.1 3.2 3.5 5.4 5.7 4.7 2.9 4.5 4.1 3.8 4.6 4.8
##  [217] 4.2 4.9 6.3 2.9 4.7 3.6 3.3 5.5 2.9 4.5 3.7 5.1 4.2 3.7 4.9 5.0 4.7 2.3
##  [235] 3.7 5.1 5.0 4.9 3.5 6.3 3.8 3.3 6.0 3.7 3.7 5.0 3.5 4.6 4.4 3.9 5.5 3.1
##  [253] 3.9 3.9 4.9 4.6 4.3 5.5 4.9 5.1 5.8 5.5 3.0 5.6 3.8 5.9 4.0 3.8 4.9 5.9
##  [271] 4.0 3.6 5.0 4.6 6.0 4.9 4.0 4.2 5.1 3.3 5.6 4.5 6.5 4.6 3.6 4.4 5.7 3.9
##  [289] 4.6 4.8 4.2 5.7 3.6 4.2 3.4 4.4 4.8 5.0 5.0 5.4 4.5 3.3 4.3 5.1 5.3 5.7
##  [307] 2.4 4.7 4.6 5.3 4.6 5.4 3.7 4.6 5.0 3.5 5.4 3.8 6.2 3.7 5.2 3.2 4.8 3.4
##  [325] 4.2 4.9 4.2 5.1 2.1 3.6 4.8 5.3 5.0 4.3 3.9 5.4 4.1 4.2 4.1 4.3 4.3 5.1
##  [343] 5.7 3.7 4.8 5.0 3.6 6.5 4.7 6.0 7.3 4.1 6.0 4.6 4.1 4.6 4.7 4.8 4.9 3.8
##  [361] 2.9 5.9 3.2 5.4 4.8 3.9 4.8 6.0 5.7 4.5 4.6 3.3 5.0 3.1 3.5 4.1 3.8 3.2
##  [379] 5.2 5.3 5.3 5.0 4.0 5.4 3.7 5.9 5.4 3.2 5.2 3.7 5.5 4.7 3.3 5.4 6.7 3.2
##  [397] 5.2 4.4 5.8 6.3 4.8 4.1 4.4 5.0 5.3 5.1 2.8 4.3 4.4 6.5 4.8 3.5 4.6 3.1
##  [415] 5.8 4.1 5.4 3.0 4.4 5.0 6.0 4.6 4.0 5.7 5.6 4.4 4.8 3.4 4.7 4.2 6.5 2.6
##  [433] 5.2 4.2 5.7 4.7 5.2 3.5 3.7 4.8 4.7 5.0 5.2 6.2 5.1 3.7 3.3 6.5 6.4 3.8
##  [451] 6.8 3.4 4.5 5.0 5.3 3.5 2.4 5.5 2.5 3.2 4.1 3.2 5.5 4.7 3.5 3.7 3.4 4.3
##  [469] 5.2 4.1 5.6 3.2 5.6 5.8 4.7 4.0 5.8 5.4 5.2 5.0 4.7 4.2 3.0 3.3 5.3 5.4
##  [487] 5.1 3.9 6.3 5.6 6.0 3.6 3.2 4.8 4.4 3.8 3.1 3.5 3.5 4.0 5.2 5.0 5.5 3.3
##  [505] 4.9 4.7 5.4 3.5 4.0 3.9 5.2 2.2 4.8 3.8 4.3 4.9 4.3 4.1 5.3 4.4 4.6 5.4
##  [523] 4.2 3.9 2.8 4.1 4.3 2.3 4.0 4.9 5.7 6.2 5.3 4.3 4.6 3.4 4.9 5.1 4.0 3.3
##  [541] 4.3 6.5 3.6 3.3 4.3 5.6 4.1 4.2 3.6 5.7 3.8 3.2 5.7 5.9 3.8 4.3 5.4 2.5
##  [559] 4.8 4.9 4.1 4.2 4.7 3.8 4.6 4.4 4.5 4.4 5.4 5.5 4.4 4.2 3.8 3.9 6.8 3.2
##  [577] 4.7 5.5 3.0 4.1 5.5 3.9 4.3 2.6 3.8 3.7 3.2 4.8 3.7 3.7 4.0 4.0 5.7 5.4
##  [595] 5.5 5.7 4.5 5.7 4.9 5.9 5.0 4.5 5.3 3.4 3.5 5.9 3.6 4.8 4.3 5.2 3.8 4.2
##  [613] 4.2 3.1 3.1 5.9 4.9 5.2 5.3 5.3 4.1 5.5 3.6 4.3 3.9 4.9 4.6 3.3 5.7 5.3
##  [631] 5.0 4.3 4.9 5.6 5.6 3.2 3.1 6.4 4.2 3.7 3.2 3.1 4.2 4.6 3.6 6.3 4.6 5.6
##  [649] 5.0 3.8 5.2 6.2 3.9 5.1 4.4 3.6 4.5 4.7 4.8 6.5 3.8 3.3 4.9 6.6 2.9 4.1
##  [667] 5.1 4.2 3.6 4.8 5.9 4.5 3.9 4.1 5.1 4.1 5.2 3.8 4.2 6.3 3.9 3.1 3.3 5.0
##  [685] 4.2 4.3 4.9 2.9 5.8 3.1 5.4 3.8 4.9 5.4 4.2 4.7 3.7 3.9 4.5 3.8 5.5 4.0
##  [703] 3.4 4.5 4.2 4.9 4.7 3.5 4.3 5.0 3.9 6.2 3.6 6.1 4.3 4.2 4.0 3.9 3.5 4.4
##  [721] 3.9 4.0 3.2 7.3 3.7 3.3 5.1 4.4 5.0 5.1 4.3 4.8 4.1 4.7 4.5 5.8 3.6 4.7
##  [739] 6.1 4.6 4.1 2.1 4.0 3.3 6.2 6.6 5.1 6.2 3.5 5.2 5.1 4.2 3.7 4.9 4.4 2.5
##  [757] 4.6 4.8 5.2 4.8 4.1 6.7 5.3 4.9 4.4 4.4 4.7 5.0 5.3 3.8 5.0 4.8 5.5 4.3
##  [775] 4.2 3.3 5.1 6.3 3.4 5.0 2.9 5.2 5.9 5.1 4.9 4.6 4.2 4.5 4.4 5.7 4.5 3.1
##  [793] 3.2 4.5 4.6 3.6 4.2 3.8 4.9 5.5 5.4 4.9 5.8 5.9 3.4 5.8 5.5 4.7 3.6 4.3
##  [811] 4.9 4.3 5.8 4.6 5.9 5.0 4.4 6.1 4.3 3.8 4.5 5.3 4.8 4.8 4.0 3.6 3.8 6.9
##  [829] 5.0 4.8 4.8 5.8 3.3 4.7 4.0 5.4 4.5 5.3 3.7 5.5 4.5 4.1 4.9 5.3 3.6 3.0
##  [847] 3.7 4.5 3.4 3.6 4.8 3.5 4.1 5.0 3.6 4.8 4.1 3.9 3.3 4.1 4.0 4.3 4.5 3.3
##  [865] 4.2 4.8 6.1 4.4 5.5 3.4 5.6 4.6 4.5 3.2 4.1 4.0 4.2 4.9 3.2 4.9 4.3 4.1
##  [883] 5.4 5.3 4.0 5.3 5.0 4.9 3.6 6.6 4.7 4.8 4.2 5.6 3.1 5.1 2.9 6.2 4.5 4.6
##  [901] 5.3 5.4 3.4 4.6 2.5 5.0 3.5 4.6 5.4 4.9 4.9 3.6 3.8 4.3 4.1 4.4 4.5 4.2
##  [919] 4.6 2.9 3.6 4.4 3.6 5.9 3.8 4.2 4.5 5.5 4.5 3.8 4.3 4.7 3.3 6.5 4.3 2.4
##  [937] 4.7 4.3 2.7 3.9 3.7 5.0 5.1 4.7 5.8 4.4 5.2 7.4 4.0 3.6 3.8 2.4 3.9 5.6
##  [955] 5.3 2.9 4.7 2.4 5.4 5.6 3.1 4.5 4.7 5.4 3.5 2.7 4.2 5.4 3.8 4.2 5.7 4.1
##  [973] 2.9 4.3 4.6 5.9 5.6 3.7 4.6 5.1 5.1 3.7 4.9 4.9 4.1 3.3 4.8 4.0 4.2 3.4
##  [991] 4.0 5.4 5.0 3.2 5.0 4.3 2.8 5.5 4.0 4.6
## 
## $func.thetastar
## [1] 0.0322
## 
## $jack.boot.val
##  [1]  0.550720461  0.491168831  0.324258760  0.166388889  0.120370370
##  [6] -0.005172414 -0.191142857 -0.261470588 -0.329714286 -0.484045584
## 
## $jack.boot.se
## [1] 1.006345
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
##    [1] 3.2 3.8 4.4 4.6 5.8 4.1 3.5 4.3 6.4 4.4 5.8 5.3 4.5 5.2 4.8 6.7 5.4 5.0
##   [19] 3.8 5.2 4.4 4.6 4.0 4.8 4.2 4.6 5.2 3.3 2.9 4.8 5.0 5.6 3.4 1.6 4.9 4.7
##   [37] 5.5 5.4 3.7 3.6 3.4 4.5 4.5 4.8 4.3 4.4 4.9 4.2 4.3 5.1 4.8 2.9 5.2 5.2
##   [55] 4.7 3.7 5.5 5.0 5.4 3.4 3.1 4.9 4.8 3.6 4.9 3.4 3.8 4.0 4.1 3.8 3.9 5.1
##   [73] 5.2 5.0 4.2 3.6 6.3 4.8 2.5 6.1 6.3 4.6 4.8 4.4 4.4 4.5 5.0 5.7 4.1 5.0
##   [91] 4.5 4.3 4.0 3.5 4.3 4.8 4.2 2.8 4.9 5.0 3.3 3.1 5.5 4.9 3.9 4.8 4.8 4.3
##  [109] 4.6 4.7 4.7 6.3 4.4 5.4 4.5 2.1 5.3 5.1 4.9 2.5 4.3 3.7 4.0 4.9 4.5 3.5
##  [127] 4.3 3.9 4.8 3.6 4.6 3.7 4.0 4.7 4.9 5.9 3.6 3.4 5.1 5.4 4.8 5.6 3.4 5.4
##  [145] 4.3 5.3 5.1 4.3 3.8 4.3 4.5 5.0 4.5 3.6 6.0 3.0 3.8 4.3 4.5 5.9 3.6 3.6
##  [163] 5.3 5.0 5.1 4.0 4.2 5.8 4.8 4.3 2.1 4.7 5.0 3.4 2.9 3.4 5.2 3.8 4.0 4.9
##  [181] 5.1 5.9 5.0 5.4 5.5 5.0 4.0 5.7 2.5 2.6 1.8 4.7 5.7 4.0 1.9 5.1 4.5 5.0
##  [199] 4.2 6.0 3.7 4.8 1.8 4.8 4.7 5.1 4.1 4.1 4.4 5.4 4.7 3.9 3.7 4.5 3.8 4.5
##  [217] 3.0 4.1 5.1 5.0 3.4 3.6 3.7 5.5 5.4 5.1 3.8 3.6 4.5 3.8 4.8 5.1 5.4 4.4
##  [235] 3.5 3.7 5.6 5.6 5.9 4.4 3.6 3.8 2.7 3.3 5.3 5.4 5.2 5.0 3.1 4.2 4.6 4.2
##  [253] 4.1 5.2 3.4 3.3 3.9 5.5 6.3 3.4 3.0 4.5 3.8 4.9 5.5 5.4 3.6 4.2 5.2 3.1
##  [271] 5.5 5.4 4.8 5.3 5.1 4.8 3.6 4.1 3.3 4.9 4.5 4.2 5.1 3.8 6.3 4.7 3.4 2.8
##  [289] 4.2 5.4 6.0 4.8 4.1 4.3 5.6 5.1 5.0 3.7 5.0 3.7 4.0 5.8 3.7 3.8 5.0 2.2
##  [307] 5.1 2.9 5.6 4.3 4.4 5.3 5.0 2.6 5.6 4.3 4.0 3.7 4.0 4.3 2.5 5.0 4.7 5.3
##  [325] 4.0 4.5 3.5 5.5 3.8 5.2 5.8 3.8 6.0 3.5 4.0 5.5 5.4 5.9 3.5 5.6 4.1 5.1
##  [343] 3.6 5.2 4.6 4.9 5.1 3.9 4.4 5.2 2.9 5.1 3.0 4.3 4.3 4.1 2.7 4.2 5.0 5.3
##  [361] 5.5 4.9 2.5 7.4 5.0 2.6 4.7 5.9 3.3 4.2 3.5 4.3 4.2 4.3 3.9 4.7 5.6 5.7
##  [379] 3.0 5.3 2.9 4.6 3.8 3.4 4.1 3.4 4.3 4.9 3.0 5.1 4.9 5.3 4.2 5.7 5.6 5.5
##  [397] 5.0 4.6 2.9 3.4 5.1 5.3 5.2 3.6 4.0 5.1 3.7 5.7 3.9 4.0 4.7 5.2 5.4 4.1
##  [415] 3.8 4.9 5.0 2.9 4.5 4.9 6.0 3.2 6.1 4.2 4.0 5.5 3.6 5.6 5.4 3.3 5.1 3.3
##  [433] 5.5 3.7 3.8 5.3 5.1 3.4 4.5 5.1 4.8 5.4 5.0 5.2 5.3 5.1 4.7 3.9 4.1 4.6
##  [451] 3.8 5.6 4.2 4.4 3.2 6.7 4.3 4.1 4.3 4.2 5.0 4.6 5.4 3.4 3.7 5.6 3.4 5.5
##  [469] 5.0 6.1 4.8 5.5 4.3 4.5 4.5 5.9 6.9 4.0 4.0 4.9 6.7 2.9 3.0 4.9 6.7 3.6
##  [487] 5.8 4.9 4.6 5.4 5.0 4.1 4.7 5.5 4.4 4.3 4.2 5.3 4.3 5.1 4.4 4.7 5.4 2.8
##  [505] 3.9 4.6 5.6 4.3 4.0 5.1 3.5 3.9 5.6 3.8 4.8 5.5 4.3 5.8 3.7 6.4 4.3 5.6
##  [523] 3.9 5.1 4.0 4.4 4.6 3.8 3.5 4.6 4.7 3.5 5.3 3.0 4.1 4.4 4.4 5.3 3.7 4.6
##  [541] 4.3 4.2 2.8 4.4 4.2 3.5 5.2 4.5 4.3 4.8 4.1 5.0 6.0 4.6 1.7 5.4 4.4 6.6
##  [559] 4.7 4.5 4.1 3.3 5.4 3.9 2.9 5.1 5.2 4.5 3.9 3.5 5.1 4.3 4.0 6.0 3.0 4.2
##  [577] 4.8 4.7 4.6 3.1 4.8 4.8 5.7 5.1 3.7 4.7 5.6 5.5 3.0 6.2 3.5 4.4 4.6 4.4
##  [595] 4.1 4.8 4.2 4.7 3.5 3.8 5.7 5.3 3.7 3.7 4.8 5.2 5.5 3.7 4.6 3.0 5.6 5.5
##  [613] 4.7 5.0 5.0 3.7 5.8 6.2 3.6 4.5 3.6 4.0 4.6 6.2 5.0 5.4 4.2 3.8 5.5 5.7
##  [631] 4.7 4.3 4.6 3.0 4.2 3.9 4.6 5.4 4.6 5.4 4.8 4.6 3.8 3.4 2.7 3.7 5.3 3.3
##  [649] 3.6 5.0 4.7 6.4 5.3 3.3 3.7 4.2 3.3 4.4 4.8 5.3 4.3 3.6 4.6 4.1 5.6 3.6
##  [667] 4.6 5.6 5.0 3.1 5.0 4.7 3.5 3.8 3.1 3.4 4.4 4.9 3.4 5.4 4.5 4.5 4.6 3.0
##  [685] 5.1 4.2 4.7 3.4 5.8 7.1 4.3 5.0 4.0 6.8 4.9 3.4 4.1 3.3 4.5 4.6 6.4 5.2
##  [703] 3.9 5.2 4.3 3.6 3.4 4.3 4.0 4.4 5.0 6.0 5.4 6.5 4.5 6.2 4.4 3.7 4.3 3.4
##  [721] 5.7 2.3 4.0 3.9 5.9 4.1 4.3 3.5 4.3 5.4 4.3 5.2 3.6 5.5 2.6 4.6 4.2 4.0
##  [739] 4.1 4.5 3.8 5.6 5.4 3.8 4.2 6.1 3.5 4.2 4.1 2.2 4.2 5.9 4.3 4.8 5.7 4.7
##  [757] 4.9 3.7 4.9 3.3 4.1 3.1 6.3 5.0 5.1 5.5 4.2 4.3 3.1 4.9 4.4 4.5 3.1 3.8
##  [775] 5.0 5.3 4.9 4.8 4.7 4.5 3.1 5.1 3.5 4.0 2.3 4.8 3.8 3.9 4.1 3.5 3.2 4.4
##  [793] 5.3 4.4 4.3 5.4 6.2 4.9 4.3 3.0 5.3 4.5 4.7 3.9 3.3 3.7 3.9 5.3 5.2 4.3
##  [811] 4.6 3.9 3.2 5.1 4.2 6.1 5.6 4.7 3.6 5.5 4.4 5.1 4.4 3.0 5.5 5.0 5.0 5.8
##  [829] 5.8 4.9 5.7 5.1 5.6 4.5 4.7 5.8 4.6 6.9 5.1 4.1 3.1 4.2 3.9 5.1 4.6 5.3
##  [847] 5.8 3.0 5.3 4.2 4.6 4.5 2.9 3.4 3.7 3.6 3.7 4.2 4.4 4.8 5.9 4.1 4.2 5.3
##  [865] 4.4 3.9 2.9 4.4 4.3 3.0 3.7 4.5 4.3 4.3 6.6 5.6 6.5 5.0 3.3 4.7 5.7 4.8
##  [883] 5.3 3.7 3.6 4.9 4.3 3.9 6.0 4.0 6.3 5.0 5.7 3.7 4.4 3.2 5.2 3.9 3.7 4.8
##  [901] 3.4 5.0 3.4 2.9 5.9 5.1 3.8 3.5 5.5 6.3 5.1 5.6 4.0 2.2 5.1 3.8 5.0 4.4
##  [919] 4.2 4.4 3.4 4.5 3.0 4.4 4.5 3.7 5.1 4.1 4.6 5.2 3.6 4.0 5.4 3.0 5.0 4.6
##  [937] 3.2 4.4 6.2 3.7 6.2 4.5 3.0 5.1 6.2 4.6 4.1 4.0 5.4 5.2 4.7 4.8 4.0 3.8
##  [955] 5.0 5.2 5.7 4.2 4.9 4.3 4.5 4.7 5.4 5.0 3.9 5.4 4.5 5.9 4.3 5.9 5.3 3.1
##  [973] 2.3 4.4 4.1 4.1 5.5 5.0 4.8 5.0 5.6 4.3 5.4 4.8 6.0 4.5 4.7 3.7 4.5 2.9
##  [991] 3.1 4.0 4.0 4.4 3.2 5.9 5.6 5.5 4.5 4.6
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.3 5.2 5.1 5.0 4.9 4.5 4.5
## 
## $jack.boot.se
## [1] 0.96
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
## [1] 0.842351
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
##   1.7310628   3.0436095 
##  (0.7120937) (1.4500707)
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
## [1] -0.05567777  1.18474109  0.63568399  1.14268093  2.32911047  0.05113341
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
##    [1]  1.274415538  0.281557607  0.028838042  0.903439832  0.577125086
##    [6]  0.594875039  0.295371377  0.558148481  0.797218779  1.025827765
##   [11]  1.799580259  0.203723785  1.646542629  1.013998037 -0.405151218
##   [16]  0.558148481  0.204022184  0.539279157  1.755327444  0.790574744
##   [21]  0.774804929  0.140930554  0.984927812  0.646439586  0.793160346
##   [26]  1.545007232  0.301817541  1.139825957  1.270323202 -0.300279505
##   [31] -0.025938157 -0.179644444  0.734355979  1.819119168  0.416895289
##   [36]  0.853890348  0.801398291  1.326495753  0.227781906  0.849212780
##   [41] -0.365495641  1.353182297  1.389668913  1.628032483  0.201690211
##   [46]  0.424039603  0.656139431  1.071519941  0.432892954  1.761981430
##   [51]  0.483799199  0.639941182  0.903117569  0.607017901  1.156353385
##   [56]  0.851684601  0.704471320  0.676677250  0.674185013  1.659721786
##   [61]  0.791397970  1.139054699  1.007585837  0.023242669  1.055668517
##   [66]  1.247437004  0.148829949  0.842270260  0.452266030  1.468084905
##   [71]  1.133651575  0.073729570  0.660149389  0.477754603  1.099502133
##   [76]  1.134540030  1.241774427  1.363209001  2.515927138  1.254193782
##   [81]  0.562438404  0.134261727 -0.626328754  1.022124981  0.736060612
##   [86]  0.630707303  1.268743498  0.173048495  0.439399738  0.744637944
##   [91]  1.492377813  0.016853241  0.440301424  1.171296389  0.997871233
##   [96]  0.859591597  0.640639670  0.422234465  1.531476666  0.125901452
##  [101]  1.271913851  1.262122761  0.724274270  1.030455947  0.570417398
##  [106]  1.468904189  0.803703746  0.446283212  1.344569148  0.434091504
##  [111]  1.022001403  1.121646427  1.055569275  0.815326801 -0.292518296
##  [116]  0.666505725  0.765496793  0.753745757  0.903276405  0.872116159
##  [121]  1.613243144  1.174565869  0.182726361  1.440664462  1.237350859
##  [126]  0.725816584 -0.168183489  0.023980115  0.621118594  1.182016108
##  [131]  0.419160556  1.038870401  2.354749978  0.152500985  0.620588189
##  [136]  1.190404670  0.948802231  0.789555868  0.980738917  0.212455695
##  [141]  0.066214721  0.520539028  1.524800364  0.887064191  1.809380321
##  [146]  0.478819559  0.216700475 -0.182386198  0.275389527  0.130918042
##  [151]  1.147667061  0.851730518  1.010515500  1.138748092  0.712486867
##  [156]  0.210584112  1.275755304  0.396352089  0.026338248  0.814763658
##  [161]  0.444780583  1.741988001  0.101340942  0.886517027  1.408041570
##  [166]  0.460152154  0.481946379  0.359087181  0.538709260  0.383196388
##  [171]  1.012071700  1.624474229  0.724228851  0.400684032  0.173822372
##  [176]  0.734362458  0.633475032  1.142151976  1.645446664  2.047237554
##  [181]  0.584816528  1.648552858  0.739867125  1.812938869  0.293409784
##  [186] -0.412275789  1.546343322  1.299942295  0.564907254  0.106439665
##  [191]  0.213954368  0.759196344  0.573982202  0.669890472  1.150991885
##  [196]  0.034117765  1.152319047  0.276040899  0.706851456  0.235141856
##  [201]  1.345433582  0.847777936  0.921618211  1.068075282  0.246652022
##  [206]  0.563103770 -0.398595779  1.214612080  0.794137170  1.483385979
##  [211]  0.098617011  0.629804239  0.581330923  1.962152456  1.790952243
##  [216]  0.478763632  1.407215322  1.529443847  0.869916341  0.961308349
##  [221]  1.147630754  0.671189365  0.949842461  0.523206166 -0.306763344
##  [226] -0.192208113  0.413566805  0.873502042  0.558379486  0.254048852
##  [231]  1.610948702  0.444099505  0.070892178 -0.334540735  1.753686159
##  [236]  0.431540854  1.143013413  0.838332476 -0.354212342  0.792536310
##  [241] -0.373165039  0.553540074 -0.140938418  1.315513751 -0.291413524
##  [246]  1.798589621  0.976505659  1.016927242 -0.152730788  0.793090959
##  [251]  0.894076973 -0.046580383  1.413856468  1.252696704  0.716004840
##  [256]  0.824220043  1.195103127  0.810672028  0.057582262  1.244422135
##  [261]  0.946111863  1.511276633  0.521747393  1.250674088 -0.443508342
##  [266]  0.089792619  1.622603376  0.392310733  0.124825597  0.164620533
##  [271]  0.833334775  0.148163153  1.504248200  2.116408178  0.043856162
##  [276]  1.812177312  0.845128454  0.555251033  0.095109239  0.652336837
##  [281]  0.726941908 -0.112722469  1.055201081 -0.073543979 -0.066695565
##  [286]  0.307938844  2.121356825  0.553569804  0.236723923  1.516002392
##  [291]  1.036326220  0.369377348  1.026237303  0.776533484  2.113009683
##  [296]  0.935460694  0.545426892 -0.135536855  0.450248484  0.842233726
##  [301]  0.778776772 -0.352952143  1.096577935  1.387889854  0.517101024
##  [306]  1.380935326  0.465277493  1.412401134  0.365544998  1.504778551
##  [311]  0.758673414  1.084850106  1.307757045  1.164837790  0.567577145
##  [316]  1.647827774  1.490546129  1.342625727  0.295016102  0.279390741
##  [321]  0.299005437  1.430781822  0.677893750  0.766094293  0.022707449
##  [326]  1.055761164  0.638595588  0.841739004  0.812726164  1.183417653
##  [331]  0.842350999  0.472710051  0.606855947  0.101363123  0.151513603
##  [336]  0.050054368  0.247370820  1.721825177  0.199627715  0.637786648
##  [341] -0.093911230  0.475410351  0.243174068  0.466047390  1.374860386
##  [346]  0.935439754  0.454708879  0.498760828  0.848138146  1.746097019
##  [351] -0.008842659  0.087003582  1.556361676  1.399050203  0.781551684
##  [356]  1.109201925  0.825007183  0.451133913  0.889325007  0.205945856
##  [361]  0.504396905  0.479436664  0.846498767  1.085488690  1.284734668
##  [366]  0.277751915  0.746289404  0.155313475  0.779239496  0.548927049
##  [371] -0.144391580  0.745785862  0.063876031  1.308632541  1.042961224
##  [376] -0.291435965  0.216601224 -0.020917347  0.753911506  0.807589535
##  [381]  0.450700301  1.674331027  1.182785453  0.708306168  1.237079544
##  [386]  1.381076121 -0.004384196  0.781925819  0.618536940  0.777706723
##  [391]  1.701174960  0.724413246  0.759281620  0.571379469  0.560887834
##  [396]  0.996845434 -0.074703447 -0.307765076  0.404774777  0.290797590
##  [401]  1.025782341  0.204683483  0.961420883  0.429502085  0.720995458
##  [406] -0.002337742  0.789799917  0.641247418  1.536675970  0.993145011
##  [411]  1.752753578  1.255864983  0.699536659  0.645853771  0.931471507
##  [416]  0.189347461  0.701834706  0.219610641  1.767512019  1.262319754
##  [421]  0.700718239  0.706975268  0.679689996  0.479673639  0.133454439
##  [426]  0.561187803  0.514447145  0.279442933  0.103504612  1.203556479
##  [431]  0.913679567  0.858520696  1.649316250  0.253852340  0.305370469
##  [436]  0.024784388  0.938381720  0.229281019 -0.194739417  0.697305404
##  [441]  0.221398729  0.109502263  0.643322473  0.471032947  1.794195686
##  [446]  0.724476986  0.867608641  0.938459663  1.364765042  0.237569256
##  [451]  0.840063046  0.164603045  1.416653900  0.921841478  0.647939255
##  [456]  0.878288477 -0.199073207  0.712627599  1.517017314  0.932580573
##  [461]  1.281386242  0.691732460  0.139445424  1.726795014  1.703944116
##  [466]  1.239254865  0.208882279  1.181811845  0.685896107  0.851389491
##  [471]  1.200414439  0.748725696  0.053623703 -0.099838219  0.232109983
##  [476]  2.354000802  0.614050840  0.636447199  1.298469414  0.472453668
##  [481]  0.752388353  0.424132895  0.501474886  2.020684547  0.959340827
##  [486]  1.209866992 -0.158020411  1.055566537  1.058690636  1.298771231
##  [491] -0.024620783  0.005116583  0.863809216  0.612654087  0.662088251
##  [496]  0.840063046  0.843144026  0.878184315  0.648984589  0.858066346
##  [501]  0.507166835 -0.152790841  0.631406651  1.126869275 -0.240357853
##  [506]  0.528030316  0.305769933  0.386917396  0.710407200  1.961180927
##  [511]  0.464879294  0.644748550  0.626797777  0.707438977  1.526670536
##  [516]  0.397222138  1.214494557  0.406480779  1.412483129  0.211487031
##  [521]  0.657971937  0.059511221  0.407176473 -0.309343351  1.055652019
##  [526]  0.219875810  0.607207992  0.916741401  1.643148414  0.359533259
##  [531]  0.197602394  1.174410392  1.904040701  1.233176104  0.892755786
##  [536]  1.672725155 -0.076951120  0.284394732  1.187977735  1.037648370
##  [541]  1.199957444  0.693260641  0.237456874  2.095663203  1.176096629
##  [546]  0.456891380  0.188421948  0.026338248  0.490838517  0.616031595
##  [551]  0.484004360  0.946925681  0.739253975  1.643583951  1.147008822
##  [556]  1.009325553  0.489380527  1.209545092  1.201673404  0.803091689
##  [561]  0.823402856  1.131030734  0.547672868  0.501930028  0.801940244
##  [566]  1.539590279  0.655844703  0.448207040  0.289264526  0.364542062
##  [571]  0.383467091  0.592528133  1.575665229  0.329366592  1.406625712
##  [576]  0.457645595  0.628159286  1.411374596 -0.336513922  1.293429467
##  [581]  0.777669414  0.448985873  1.939353712 -0.626015148  0.461385120
##  [586]  1.302373538  0.760840122  1.413481586  0.772368045  0.376769514
##  [591]  1.140879123  1.534173189  1.294189782  0.040862303  0.854331928
##  [596]  0.512317055  1.389835708 -0.095319925  0.636447199  0.812853853
##  [601]  0.103947596  0.572738731  0.439232232  0.363602355  0.446597477
##  [606]  0.617241351  0.405011253  0.276655817  1.155464752  0.861483659
##  [611]  1.144445663  0.994634527  1.082037779  1.650165987  0.789999280
##  [616]  1.115461634  1.013589790 -0.209352809  0.653251380  0.794010486
##  [621]  0.654244850  0.556111575 -0.071796806  1.315021535  0.623336699
##  [626]  0.370983523  0.865015685  0.511841019  1.923432404 -0.129604651
##  [631]  1.022744625  0.659532834  0.737337012  0.280444380  1.063777832
##  [636]  0.815133457 -0.304623754  0.213288122  0.727806854  0.985266819
##  [641]  1.021207204  0.272112382  1.293697313  0.421456926  0.858193534
##  [646]  0.351290825  0.520558075  0.212759235  1.472642429  0.616164516
##  [651]  1.393569232  0.808410118  0.337535903  0.580411369  0.280766267
##  [656]  0.299648581  1.472265139  1.492924050  1.249158285  1.554174458
##  [661]  1.252070488  0.865235021  0.048628164  1.093698833  1.468084905
##  [666]  1.396155476  0.302889581  1.068378613  0.536950556  2.626329302
##  [671]  0.842786034  0.356057173  0.841020202  1.446383034  1.068639081
##  [676] -0.094133932  0.114936167 -0.488929265  1.013695186  1.135895566
##  [681]  0.598142337  0.016564632  0.822924143  0.699422287  0.188758099
##  [686]  0.515371240  0.694799115  1.330079551  0.358108562  2.083374491
##  [691]  0.826464847  0.719200731  1.345053462  2.644491389  0.161039632
##  [696]  1.203564570  1.350990874  0.339806029  0.042399897  1.313547436
##  [701]  1.198950318  1.598368177  0.987715454  0.125563696  0.902034832
##  [706]  0.869234791  1.251452130  0.790903492 -0.132380961  0.298389636
##  [711]  1.285518620  0.783121232  0.182836095  1.350990874  0.220410953
##  [716]  0.718896174  1.062766574  0.073520406  0.694258537 -0.193159478
##  [721] -0.160122334  0.421369671  0.344070669  0.223061909 -0.352183641
##  [726]  0.940039872  1.390404362  1.994751964  1.545271521  0.633053902
##  [731]  0.484421727  0.214447752  0.028885861  0.088951284  0.338514753
##  [736]  0.417562758  0.195068271  0.341418941  0.541167664  0.632617202
##  [741]  0.265234604  1.627327964  0.001903751  1.957607102  0.817043446
##  [746]  0.270195842  1.280741439  0.322830454 -0.177138871  1.267808563
##  [751]  0.726053709  0.262610218  1.272086062  0.201683465  0.635320642
##  [756]  0.532470482  0.514954547  1.483704142 -0.334623312  1.014149378
##  [761]  0.601645897 -0.278220038  1.695553036  0.761759424  0.600839123
##  [766]  0.656632299  0.125419398  1.126691111 -0.016098790  0.729436046
##  [771]  0.600652452  0.217555710  0.417438175  0.985092538  0.423026393
##  [776]  0.697326994  1.754389796  2.236316892  0.441018403  0.433816894
##  [781]  0.423884737  0.981152536  0.139157453  0.163215736  0.456858780
##  [786] -0.222146244  1.277164784  0.669474505  0.789236043  0.498028541
##  [791]  1.442145807  0.218713517  0.499272333  0.520452017  1.179239065
##  [796]  0.097198289  1.500952443  0.780644941  0.500888810  1.268121575
##  [801]  1.919974845 -0.055240268  0.538537845  0.460356537  0.640639670
##  [806]  1.905850115  1.207135184  0.699518291  1.540313690  0.993528109
##  [811]  0.281115423  0.363734414  0.257765111  0.922722067  0.635579890
##  [816]  0.449791396  0.285969519  0.548153716  0.642596601  0.123548368
##  [821]  0.123709627  0.641805839  0.013090434  0.219631293  0.945384416
##  [826] -0.172433232  0.261132647  0.432854678  0.847918047  0.421178917
##  [831]  1.481917768  0.406990665  1.306913500 -0.607187049  0.441340120
##  [836] -0.125954964  0.569733302  0.795058299 -0.019445060 -0.016542954
##  [841]  0.572651762  0.561070198  0.711698707  1.875377276  1.111698343
##  [846]  1.324509259  1.259329939  1.130558870  1.297023385  0.544875249
##  [851]  0.349517682  0.747525295  0.732341155  0.700718239  0.645816254
##  [856]  0.423432727 -0.055073653  0.472943539  1.128277399  0.563837396
##  [861]  1.234170233  0.380998518  0.508763540  0.598055599 -0.078566953
##  [866]  1.183803931  0.714740781  1.482134964  0.317848336  0.856526779
##  [871]  0.655218078  0.581244782 -0.100698267  1.241259477 -0.028019340
##  [876]  1.190919431  1.261399125  0.463310549  1.549233763  0.363356454
##  [881]  0.558470450  0.004136538  0.580892643  0.779973653  1.451960917
##  [886]  1.291483201  0.460503538  0.565307947  1.433571440 -0.056185755
##  [891]  0.840912221 -0.101313721  1.048338668  0.828000750  0.221467663
##  [896]  1.075994447  1.208399637  0.384725001  0.417835124  1.317243911
##  [901]  1.345433582  0.415354866  1.158824175  0.487233693  0.370461904
##  [906] -0.098755355  0.443240932  0.153267899  0.275403356  0.666291428
##  [911] -0.702405468  0.239477651  0.908428459  0.852500572  1.255177311
##  [916]  0.656395894  1.229958684  1.009675684  0.736081026  1.939422645
##  [921]  1.206429621  0.462190378  0.846849031  0.070316613  0.931795806
##  [926]  1.273133437  0.267014981  0.646259226  0.092187119  0.765496793
##  [931]  0.634188062  1.020689487  0.555337626  1.536084125  0.778091283
##  [936]  0.238679759  0.398192131  0.243708875  0.472710051  0.622048036
##  [941]  1.327533814  0.675918514  0.754992909  0.468546601  1.065337313
##  [946]  0.156411097  0.627291235  0.855354843  1.988563477  1.551234081
##  [951]  0.419545630  1.187359679  0.420340181  1.786113848  1.479456244
##  [956]  1.091661280  1.906283107  1.156874360  0.281204101  0.587863909
##  [961]  0.246650796  0.452559669  0.207801861  0.689571632  1.210061936
##  [966]  0.885352172  0.834179742  0.245405171  1.212676549  0.865400496
##  [971]  1.149790213  0.142216824  0.629355243  0.336512506  1.486279212
##  [976]  1.393419265  0.476694868  1.158338239  0.516885062  0.406012841
##  [981]  0.494344740  0.404755931  0.765496793  1.142358452  0.914545479
##  [986]  0.542006077  0.516861465  0.792755065  0.545400644  0.708384967
##  [991]  1.792350026  1.806234954  1.302192284  0.330630618  0.417835124
##  [996]  0.029057300  0.855458586  0.397222138  1.449760193  1.153106621
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
```

```r
fit2
```

```
##       mean          sd    
##   0.56873726   0.44395658 
##  (0.14039140) (0.09926997)
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
## [1] -0.15617111 -0.54442072 -0.07305945  0.15169233  0.03013862  0.58422700
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
## [1] 0.0619
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9139659
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
## t1*      4.5 0.05235235   0.9139489
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 4 5 6 7 8 9 
## 1 1 2 1 2 1 2
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
## [1] -0.0053
```

```r
se.boot
```

```
## [1] 0.8975341
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

