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
## 0 1 2 3 4 5 8 9 
## 1 1 1 1 3 1 1 1
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
## [1] -0.0314
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
## [1] 2.660374
```

```r
UL.boot
```

```
## [1] 6.276826
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7000 6.3025
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
##    [1] 3.9 3.9 4.3 4.1 2.8 5.8 4.1 3.5 5.1 4.1 5.2 4.6 3.5 3.6 5.5 4.7 3.1 4.9
##   [19] 4.7 3.8 3.7 3.9 3.1 5.3 4.3 3.7 3.7 3.9 4.2 4.6 5.5 4.1 2.5 5.4 4.9 6.3
##   [37] 3.4 4.5 3.8 4.0 4.6 5.0 4.1 3.1 3.1 3.5 4.6 3.5 4.9 3.6 4.2 3.3 5.3 6.0
##   [55] 4.7 5.8 4.9 4.3 4.7 4.0 3.7 3.1 3.8 4.5 4.3 5.1 5.1 5.8 2.6 4.5 4.4 5.5
##   [73] 4.0 5.0 5.0 4.8 5.7 5.2 3.9 3.1 6.1 4.8 5.4 4.5 4.2 2.7 4.0 6.0 5.4 4.7
##   [91] 4.2 4.2 7.6 4.2 3.3 3.8 5.6 4.4 5.1 3.7 3.4 5.3 2.6 5.8 4.4 2.9 4.7 5.2
##  [109] 4.7 4.0 3.1 4.7 2.6 4.2 4.1 4.6 4.5 4.1 4.3 5.6 4.3 3.9 4.1 4.6 5.5 4.2
##  [127] 4.2 5.3 5.4 4.0 5.5 6.3 3.9 3.8 5.1 2.7 4.1 5.3 4.5 3.5 4.9 3.3 5.2 4.7
##  [145] 3.9 4.0 6.2 2.7 4.1 4.3 2.9 6.6 4.0 3.7 3.9 5.4 4.5 5.4 5.1 4.5 4.2 4.7
##  [163] 3.3 5.0 3.7 5.1 6.1 5.2 4.9 6.3 4.9 4.2 4.6 4.1 3.5 5.7 3.7 3.7 7.0 4.8
##  [181] 6.1 5.2 2.0 4.4 4.1 4.6 3.1 5.6 5.5 4.8 4.2 3.1 2.5 4.7 4.5 3.2 4.5 3.6
##  [199] 4.3 3.9 3.7 4.4 5.1 4.0 4.9 4.6 4.3 3.5 4.8 4.2 3.4 3.6 5.4 4.7 4.2 4.0
##  [217] 4.7 7.0 3.1 5.4 4.0 4.6 5.4 4.4 5.2 4.0 5.0 5.5 5.0 3.5 5.4 3.9 1.8 5.3
##  [235] 4.3 4.4 5.2 4.8 5.5 5.8 4.2 4.2 4.6 7.6 4.9 5.9 4.3 5.3 5.4 5.8 3.2 6.1
##  [253] 4.3 3.2 3.6 4.3 4.6 4.0 5.0 5.8 5.1 3.8 4.9 5.4 3.6 3.4 2.5 5.1 4.2 4.6
##  [271] 5.1 4.5 3.8 2.7 4.2 4.8 4.3 3.0 2.1 3.4 4.9 3.7 4.8 3.2 4.1 4.9 3.9 4.3
##  [289] 5.3 5.5 4.9 4.8 6.0 4.7 3.4 6.6 4.9 4.3 5.0 4.0 4.1 5.5 5.0 5.3 3.9 5.0
##  [307] 3.0 3.6 4.7 4.9 3.3 2.9 2.9 4.6 4.2 4.2 6.1 6.1 4.0 5.1 4.3 5.5 4.4 5.7
##  [325] 4.4 5.1 4.7 4.7 2.8 3.3 4.3 3.3 4.2 3.8 5.3 5.5 2.9 3.1 4.2 3.5 4.3 5.2
##  [343] 2.7 2.0 5.5 3.7 4.6 5.3 4.8 3.2 4.8 5.0 3.5 5.2 4.0 5.2 4.5 3.9 3.8 4.5
##  [361] 4.6 3.3 5.6 4.2 4.2 4.6 3.2 2.9 4.9 6.4 4.6 5.3 5.1 4.8 3.6 2.8 5.2 5.0
##  [379] 6.2 2.8 7.1 4.2 3.7 4.7 3.8 4.2 5.1 6.2 3.3 4.7 4.5 4.9 4.2 3.8 5.6 5.7
##  [397] 3.2 3.6 3.6 4.4 3.8 4.6 3.3 3.2 4.9 4.8 5.6 3.7 4.6 5.9 3.7 4.5 3.9 3.2
##  [415] 3.8 5.5 5.5 3.7 4.0 4.0 3.8 4.7 3.9 4.4 2.9 4.2 5.1 4.1 3.4 4.9 4.5 5.3
##  [433] 3.7 5.7 3.9 4.2 4.3 3.3 4.6 3.7 4.0 3.9 4.3 4.3 5.9 3.8 4.8 5.2 3.7 3.9
##  [451] 3.0 5.0 4.0 4.3 6.0 4.6 4.4 3.8 3.9 5.6 3.5 4.8 5.0 3.9 6.4 2.2 2.8 4.4
##  [469] 3.1 3.3 5.0 5.5 4.2 2.6 3.5 3.1 5.7 4.6 3.8 6.2 4.3 3.4 4.6 4.4 3.3 4.9
##  [487] 3.6 4.6 2.9 5.1 4.8 3.2 5.2 2.9 4.2 3.6 4.7 4.4 3.7 4.8 4.1 6.5 3.9 5.6
##  [505] 3.6 4.1 4.8 5.2 6.4 4.8 4.8 5.5 3.5 4.7 4.5 4.5 4.9 3.0 3.5 4.2 4.0 5.1
##  [523] 4.5 3.9 4.5 4.8 5.9 2.5 4.8 4.2 5.9 6.1 4.0 5.9 5.0 5.0 4.1 4.9 5.8 5.7
##  [541] 4.7 4.3 5.5 5.0 6.2 4.2 6.9 4.0 4.9 6.0 4.5 3.0 4.4 4.8 4.4 4.2 4.0 4.6
##  [559] 5.0 5.1 5.6 4.4 3.3 4.0 3.6 5.9 6.4 5.7 4.0 5.7 4.8 4.8 4.9 3.9 4.0 4.9
##  [577] 2.4 3.8 4.6 5.5 4.8 5.0 5.0 2.9 3.8 3.6 4.1 5.5 3.3 5.7 4.0 4.9 4.1 5.0
##  [595] 3.4 4.6 4.5 3.4 3.7 4.0 5.6 5.2 4.4 4.7 4.2 4.5 4.8 4.8 3.9 3.2 6.1 4.6
##  [613] 3.9 5.8 4.9 5.2 3.9 3.1 3.8 5.8 4.8 3.8 5.5 5.7 5.2 4.6 4.9 4.3 4.6 5.3
##  [631] 2.9 4.0 5.0 3.9 3.6 5.1 4.4 4.8 4.1 4.1 5.4 3.6 3.4 2.9 5.5 5.3 3.9 2.6
##  [649] 6.0 6.2 3.0 4.6 4.1 5.6 3.4 3.8 4.3 3.8 4.4 4.8 5.2 4.6 3.3 5.2 5.5 4.8
##  [667] 4.4 4.1 5.3 5.7 5.5 4.1 4.3 4.3 4.7 3.8 4.5 4.0 4.0 4.3 3.5 3.2 3.2 6.0
##  [685] 5.3 4.6 4.1 5.2 6.1 5.1 5.2 4.5 4.2 4.2 3.6 3.2 4.0 3.1 3.3 4.3 4.3 5.6
##  [703] 5.9 5.0 4.2 4.3 5.4 4.7 3.8 5.2 3.8 6.4 3.0 4.3 3.7 4.7 7.3 3.8 3.9 4.7
##  [721] 4.4 5.2 5.3 5.4 3.8 4.0 3.1 4.5 3.4 4.0 3.2 3.6 3.7 4.7 4.4 4.9 2.8 5.0
##  [739] 4.8 4.6 3.7 5.3 4.9 3.3 5.4 4.7 5.3 4.5 4.0 5.7 3.5 5.1 6.7 4.1 4.9 4.2
##  [757] 4.0 5.1 3.9 5.7 4.2 5.4 4.2 4.8 3.2 6.5 3.3 5.4 3.4 4.5 4.4 3.8 4.1 3.1
##  [775] 4.5 3.3 3.3 5.0 5.1 4.8 4.3 4.8 5.2 5.2 4.6 4.2 4.4 4.8 5.3 3.1 6.8 4.9
##  [793] 4.6 3.5 4.4 5.9 4.5 4.8 4.8 3.4 4.4 4.3 5.1 5.7 6.1 4.2 2.7 3.3 3.9 4.6
##  [811] 4.0 3.9 3.0 5.4 5.8 3.6 5.2 4.4 5.1 5.3 5.2 4.7 4.4 4.2 4.7 4.3 5.0 3.5
##  [829] 3.6 2.7 5.0 5.4 5.3 3.3 4.8 3.6 6.2 4.5 3.9 4.4 3.9 3.2 3.8 3.0 6.4 5.1
##  [847] 4.0 3.4 5.5 5.8 4.4 4.2 4.7 3.7 4.3 4.3 4.3 4.5 3.7 6.6 4.9 2.8 3.5 5.9
##  [865] 5.2 4.4 3.4 3.9 4.0 3.9 2.3 4.1 5.3 3.1 4.9 3.8 4.7 5.6 4.3 5.4 4.2 3.8
##  [883] 4.2 4.5 4.1 4.8 4.3 5.7 3.4 4.8 4.1 3.9 5.9 4.3 6.0 4.1 1.5 3.3 5.1 4.4
##  [901] 4.9 4.7 4.5 4.5 4.1 3.7 4.7 5.0 5.6 3.2 4.2 5.0 4.5 3.8 3.2 3.8 3.8 6.7
##  [919] 5.6 3.7 4.6 4.2 3.5 3.6 5.6 5.7 3.4 4.2 4.4 5.2 4.8 5.5 4.2 4.3 3.9 4.4
##  [937] 2.9 4.5 5.3 2.2 4.7 3.9 4.0 4.7 5.2 6.0 5.4 4.3 5.2 5.3 4.3 4.5 3.9 3.9
##  [955] 4.7 4.9 2.6 2.9 4.0 4.8 6.4 6.1 5.6 6.0 3.9 4.3 4.2 5.3 4.4 5.1 4.9 4.1
##  [973] 5.2 4.7 5.5 3.0 5.0 3.7 4.1 6.5 4.5 5.4 4.0 5.6 5.6 4.0 3.2 4.6 5.1 3.2
##  [991] 4.6 4.2 4.1 4.2 4.0 6.5 5.2 3.2 3.1 2.7
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
##   2.7   6.3
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
##    [1] 3.6 4.8 3.9 3.9 3.2 5.5 4.9 4.2 3.3 3.5 5.4 4.0 5.0 4.7 3.9 4.9 4.5 6.7
##   [19] 5.6 3.2 5.8 6.5 3.4 5.4 4.9 4.5 4.6 4.3 5.7 2.8 3.4 4.5 5.4 4.0 4.5 4.9
##   [37] 3.1 3.6 5.1 3.2 4.2 4.1 5.0 4.0 3.9 5.2 4.2 4.3 3.9 4.4 4.2 6.4 3.4 5.3
##   [55] 4.6 3.1 3.4 4.8 5.3 4.0 3.5 4.6 4.7 5.7 4.5 4.4 2.7 5.0 4.7 4.9 5.2 4.6
##   [73] 5.7 4.0 5.0 3.6 5.8 3.9 4.9 5.9 6.2 4.8 4.7 5.1 5.1 5.7 4.3 4.0 5.2 3.9
##   [91] 6.2 5.1 3.8 3.2 3.5 4.3 4.7 6.5 3.6 3.9 4.8 4.3 4.4 5.9 4.1 5.2 4.5 4.7
##  [109] 3.9 3.2 4.8 5.4 3.6 5.1 4.1 3.3 3.7 5.8 4.5 4.4 5.9 4.6 4.2 4.1 3.1 5.0
##  [127] 4.4 3.5 5.4 3.7 4.5 3.9 4.7 6.2 4.5 4.3 5.4 4.6 3.9 4.6 3.6 2.6 5.5 5.6
##  [145] 5.3 3.6 4.2 6.6 4.1 5.0 4.2 3.4 2.3 3.9 3.9 4.0 4.2 4.0 5.4 5.0 2.9 4.5
##  [163] 3.5 5.9 3.7 5.5 5.1 5.5 5.9 4.5 4.6 5.1 5.0 4.2 5.0 4.3 4.1 4.4 5.4 5.6
##  [181] 5.9 4.8 6.4 4.8 6.7 4.2 3.9 6.2 5.1 4.4 3.6 3.4 3.1 3.9 4.4 5.2 5.4 3.8
##  [199] 5.7 3.8 5.1 4.8 3.9 3.5 3.5 4.5 4.3 4.8 5.8 3.4 3.9 3.2 5.8 2.5 5.3 4.4
##  [217] 4.8 5.5 5.2 4.6 4.9 4.7 4.5 4.5 4.5 5.6 3.9 5.5 3.0 3.3 4.1 4.6 6.2 4.8
##  [235] 4.5 5.1 4.8 3.7 3.7 3.7 4.3 4.7 4.5 4.0 3.6 5.1 4.7 6.5 3.4 3.6 4.3 3.5
##  [253] 5.2 2.7 4.2 4.7 5.9 3.9 5.6 5.2 3.8 3.9 3.5 4.7 4.2 4.4 4.7 4.7 3.0 5.5
##  [271] 4.7 3.6 3.7 6.4 6.1 3.4 3.4 3.0 4.0 3.8 3.3 4.4 4.2 4.3 5.7 4.7 4.6 4.0
##  [289] 5.3 5.4 4.6 4.7 5.9 5.4 4.4 4.1 3.6 4.7 3.0 4.8 4.1 3.3 7.3 5.1 3.4 4.8
##  [307] 5.1 3.6 2.8 4.2 3.8 3.7 5.4 4.0 4.9 5.3 3.4 3.9 4.4 3.7 4.3 5.2 4.8 4.6
##  [325] 4.7 4.9 3.9 4.5 5.7 3.9 3.6 4.6 5.6 5.1 4.9 3.6 3.8 4.2 6.3 4.2 5.9 5.3
##  [343] 4.3 3.6 2.9 3.7 3.2 5.2 4.3 4.8 5.0 4.2 5.1 4.0 4.9 5.2 5.4 4.2 3.3 4.1
##  [361] 3.9 5.1 5.6 6.1 3.3 4.2 5.5 4.8 4.8 4.5 5.1 4.2 4.3 3.7 3.7 5.6 4.6 5.4
##  [379] 5.5 4.9 4.3 3.9 3.2 5.2 5.1 3.8 6.0 5.1 4.9 4.9 3.5 4.9 4.5 2.2 4.7 3.2
##  [397] 4.2 5.3 5.1 5.0 3.6 4.2 4.4 5.4 5.7 4.5 5.6 4.5 4.4 4.6 6.4 6.0 4.0 5.1
##  [415] 4.9 4.1 4.5 4.5 4.9 4.3 4.4 5.8 3.4 2.5 4.6 4.5 6.5 4.4 5.1 3.3 4.3 4.6
##  [433] 4.5 4.4 5.9 4.4 3.6 3.0 4.4 5.2 3.6 4.4 3.6 5.5 2.1 3.2 3.4 3.4 5.2 5.2
##  [451] 3.9 4.8 3.3 5.7 4.5 4.1 5.6 3.6 4.7 4.5 4.2 3.5 5.1 5.0 3.8 5.5 4.6 5.8
##  [469] 5.9 4.1 4.4 5.1 4.8 4.1 4.4 4.4 3.7 3.5 5.0 3.5 4.9 5.7 4.4 3.8 3.8 4.9
##  [487] 4.6 3.8 4.2 6.0 2.6 4.6 4.6 5.6 4.3 3.1 3.3 4.6 2.6 4.7 3.6 3.0 3.6 4.5
##  [505] 3.8 2.9 4.8 3.9 5.9 4.9 4.2 4.9 3.9 5.6 4.5 3.6 4.2 3.6 4.6 2.7 5.5 3.9
##  [523] 4.7 4.3 2.9 2.8 5.0 5.7 4.1 5.1 3.9 3.4 5.1 4.3 3.8 3.6 3.6 5.6 6.1 2.8
##  [541] 6.2 4.9 5.9 4.6 3.1 5.8 4.4 3.7 5.1 3.6 4.9 3.4 2.2 6.0 5.6 5.9 5.6 5.0
##  [559] 4.5 4.2 5.1 5.6 4.9 4.9 5.1 3.3 3.2 3.2 4.2 5.2 3.5 4.5 5.3 5.6 3.1 3.3
##  [577] 5.4 5.9 5.2 5.6 4.8 5.4 4.7 5.7 6.2 4.3 4.9 5.0 4.5 4.7 4.4 3.3 4.4 3.8
##  [595] 5.0 5.4 5.4 4.9 4.3 3.5 5.2 3.7 4.8 5.0 4.0 4.1 5.1 4.2 4.8 3.8 4.2 3.5
##  [613] 3.8 4.5 4.0 4.6 6.9 4.7 3.3 3.9 5.3 3.6 5.4 3.8 3.7 3.8 3.1 4.7 4.9 5.6
##  [631] 2.6 5.4 3.8 5.3 4.9 4.7 5.2 5.9 6.4 4.3 4.3 2.2 4.5 4.9 4.7 5.7 3.9 5.0
##  [649] 4.8 6.0 4.8 5.3 4.3 3.4 4.4 3.1 4.6 2.4 4.9 5.2 4.6 5.9 3.7 4.6 3.7 3.6
##  [667] 3.9 5.1 2.4 4.9 4.2 4.6 6.8 4.0 4.5 3.4 4.8 4.3 6.1 2.9 5.0 4.1 4.1 4.6
##  [685] 4.8 4.6 2.6 4.0 5.0 4.3 4.6 4.1 6.0 4.1 5.5 3.6 5.2 4.1 4.1 4.2 2.8 3.5
##  [703] 4.2 4.4 2.9 4.4 5.1 4.7 5.0 4.6 3.7 4.2 3.4 4.1 3.2 5.0 3.7 4.5 3.9 5.5
##  [721] 5.0 4.4 4.5 3.0 4.0 5.2 3.8 3.2 3.8 4.2 3.7 2.7 4.1 4.9 4.8 3.8 5.3 5.5
##  [739] 4.9 5.1 3.7 5.0 3.2 4.4 4.2 3.4 4.4 5.9 3.6 5.0 5.0 4.4 4.4 4.3 5.1 4.8
##  [757] 4.9 5.2 5.0 4.0 5.1 4.4 4.5 2.7 5.9 3.9 4.7 5.1 4.2 5.1 4.7 4.2 4.1 4.4
##  [775] 4.8 4.4 5.0 4.2 5.1 3.9 5.2 6.1 3.8 4.2 5.7 4.8 3.1 6.3 3.0 4.8 5.4 5.0
##  [793] 5.3 5.2 4.6 4.0 3.3 5.0 4.2 5.6 5.6 4.5 3.0 5.2 4.7 4.8 3.1 4.4 4.4 6.0
##  [811] 5.5 4.5 4.3 3.7 4.1 4.9 4.8 5.3 4.8 4.6 4.5 4.4 2.3 4.2 4.5 5.8 6.7 3.9
##  [829] 5.0 5.3 5.1 5.8 5.0 5.1 6.2 6.3 5.0 5.6 4.4 4.5 3.7 3.4 3.5 6.5 4.6 5.1
##  [847] 5.3 4.8 4.3 5.2 5.8 3.1 5.0 6.6 4.4 4.9 5.2 2.9 6.1 4.8 4.1 5.0 3.5 4.8
##  [865] 4.4 3.1 3.7 3.8 3.5 4.7 5.5 6.0 4.4 4.3 4.3 4.9 4.7 4.9 4.0 4.3 5.0 3.5
##  [883] 5.1 4.1 3.7 4.7 2.7 3.6 3.9 3.7 5.6 4.2 4.6 5.3 5.3 5.0 5.8 4.4 4.7 5.5
##  [901] 5.2 4.4 4.2 5.5 5.0 5.2 5.2 5.1 3.9 4.6 5.9 5.3 5.2 6.1 2.6 5.2 4.4 5.0
##  [919] 5.0 4.0 4.5 4.1 4.9 3.4 2.1 3.9 5.1 4.9 4.2 4.9 3.9 5.2 4.3 6.4 4.0 4.2
##  [937] 4.9 5.2 5.0 4.6 4.7 4.3 4.8 3.2 5.3 4.2 5.4 4.2 4.3 5.4 3.9 4.1 4.2 3.4
##  [955] 4.3 6.2 5.0 5.8 6.0 4.0 5.3 4.3 4.7 3.5 5.4 4.5 6.2 4.9 3.3 4.0 3.9 4.9
##  [973] 4.8 3.3 4.5 4.9 4.7 2.7 3.4 2.1 4.1 5.3 3.5 3.9 3.9 3.6 1.9 3.2 4.3 4.6
##  [991] 4.9 4.3 3.9 5.6 3.8 6.3 4.7 5.7 4.9 6.1
## 
## $func.thetastar
## [1] 0.0067
## 
## $jack.boot.val
##  [1]  0.48534483  0.32922636  0.23501484  0.12005208  0.06184971 -0.03854447
##  [7] -0.09093750 -0.28225352 -0.39371429 -0.43738872
## 
## $jack.boot.se
## [1] 0.8762613
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
##    [1] 4.1 4.7 4.1 3.8 2.6 4.9 5.8 5.2 4.5 4.4 4.1 4.7 3.8 3.5 5.8 3.6 3.2 5.5
##   [19] 4.6 4.8 5.5 6.4 5.2 3.9 4.5 3.2 6.0 5.1 3.7 5.5 5.7 4.0 3.9 4.6 4.1 3.4
##   [37] 6.4 4.6 3.3 2.8 2.3 4.8 5.1 3.1 3.8 3.7 4.2 5.0 4.3 3.5 4.6 2.9 3.6 4.6
##   [55] 2.8 4.3 3.7 4.9 5.6 4.8 3.9 4.4 4.1 4.2 5.9 3.6 5.3 4.3 4.2 5.0 5.1 5.3
##   [73] 3.7 4.6 4.2 6.4 5.2 5.4 4.4 4.0 4.6 4.5 6.3 4.3 4.3 5.1 4.1 5.2 5.2 5.0
##   [91] 5.4 5.4 4.1 4.8 1.9 4.0 4.4 3.7 6.6 2.2 3.8 4.7 3.8 6.4 6.8 5.3 2.8 3.3
##  [109] 3.4 3.0 4.6 3.4 4.9 3.7 3.2 4.2 3.7 4.0 2.9 5.1 3.8 4.0 3.0 4.3 6.0 5.3
##  [127] 4.0 4.4 6.4 2.8 4.7 5.6 4.4 5.8 3.3 5.9 4.4 3.8 5.2 3.3 3.8 3.9 3.5 4.3
##  [145] 5.3 4.6 3.5 2.9 4.7 5.8 3.5 5.0 4.8 3.6 5.0 4.3 5.2 6.2 5.4 4.8 4.6 5.1
##  [163] 3.8 4.2 3.4 5.0 5.0 3.7 6.6 3.2 4.0 4.7 2.8 5.3 3.1 3.7 3.1 4.5 6.0 3.7
##  [181] 4.9 4.3 5.2 3.3 4.6 5.0 4.9 4.9 4.0 4.8 4.5 4.1 4.2 4.2 4.2 2.8 5.4 3.8
##  [199] 3.8 3.6 4.5 5.1 3.5 4.3 4.0 3.6 6.2 4.7 5.0 5.1 5.3 3.6 2.8 5.1 5.0 3.0
##  [217] 5.9 2.9 3.9 5.4 2.3 4.7 4.6 4.5 4.1 4.0 4.4 4.5 3.6 4.3 4.9 3.9 4.2 5.4
##  [235] 5.4 3.9 5.7 3.2 5.1 5.8 4.3 4.7 5.1 5.0 4.1 3.8 4.5 4.8 4.6 5.8 3.7 5.2
##  [253] 4.4 4.9 4.9 3.3 6.3 6.7 4.9 5.2 5.0 4.1 4.6 4.6 4.1 4.3 4.2 6.0 4.2 4.6
##  [271] 6.1 2.9 3.0 4.8 4.9 4.7 4.6 4.6 3.3 5.0 5.2 3.5 3.1 4.9 6.0 3.4 5.1 5.0
##  [289] 4.7 6.5 5.5 4.9 4.5 3.6 7.2 3.5 6.5 2.5 4.2 4.3 4.8 5.9 5.4 5.4 4.2 5.0
##  [307] 5.2 4.5 6.2 4.6 4.7 4.6 4.1 4.2 4.3 6.1 4.7 4.4 3.9 4.2 4.4 5.8 4.2 4.8
##  [325] 6.0 5.2 4.5 4.7 3.9 3.8 5.5 4.8 3.6 3.7 5.1 5.1 5.1 5.2 3.3 5.9 4.4 3.9
##  [343] 4.1 3.9 5.1 3.4 5.0 3.4 3.5 5.3 6.4 4.6 3.1 4.7 5.3 3.3 5.2 3.5 4.9 3.0
##  [361] 4.3 4.9 5.3 3.6 4.5 5.9 2.7 6.2 2.8 3.9 5.7 1.6 5.8 4.3 5.1 5.5 5.3 3.7
##  [379] 5.4 4.9 3.4 4.6 4.5 4.2 4.4 4.7 3.4 5.0 4.8 3.2 6.1 3.5 3.4 4.5 5.1 4.7
##  [397] 3.9 4.3 4.6 3.3 4.3 4.2 5.3 5.3 5.4 6.0 5.2 4.5 5.3 4.3 3.8 4.2 3.8 5.0
##  [415] 2.6 5.3 4.8 4.1 4.0 4.3 3.9 4.5 5.5 4.7 4.2 5.0 3.7 4.4 4.9 5.3 4.2 4.2
##  [433] 5.1 3.7 5.0 4.0 3.6 5.6 5.9 6.1 3.1 2.5 4.6 6.1 3.5 4.7 3.8 5.3 5.7 6.2
##  [451] 4.3 1.7 3.3 4.8 4.6 5.4 4.8 3.0 4.2 6.3 3.7 5.6 3.7 5.2 3.9 5.9 5.7 2.6
##  [469] 6.1 3.8 3.5 5.0 2.8 4.6 3.9 5.0 3.3 4.0 5.3 4.2 4.0 3.7 5.0 3.2 4.9 3.8
##  [487] 3.7 5.0 2.8 3.4 4.9 4.2 2.1 4.2 4.0 4.9 5.5 5.2 4.6 5.2 4.9 5.2 5.0 4.6
##  [505] 3.6 3.8 4.5 3.7 5.0 4.0 4.2 3.9 4.3 3.6 5.0 6.7 5.0 4.9 2.4 5.2 3.5 3.0
##  [523] 5.3 5.3 4.4 4.8 4.2 4.0 4.2 2.5 4.7 5.5 4.9 3.2 3.2 4.9 4.0 4.8 4.0 5.7
##  [541] 4.1 4.8 3.6 5.6 4.5 4.2 5.5 3.8 7.2 3.9 4.8 3.5 6.1 4.4 4.5 3.5 3.3 4.0
##  [559] 3.6 3.5 4.0 6.2 4.6 4.8 5.4 5.4 5.0 5.5 3.9 4.2 5.0 6.7 5.2 4.4 6.6 3.6
##  [577] 4.1 6.0 4.3 4.8 5.3 3.9 5.2 4.3 3.9 4.5 4.5 3.4 5.2 4.2 5.6 3.8 5.4 5.6
##  [595] 4.6 4.0 5.1 3.9 5.8 4.7 4.4 4.1 3.6 4.8 3.7 4.3 4.2 6.1 5.7 5.0 5.3 4.0
##  [613] 3.4 4.6 3.7 3.8 4.3 3.5 5.3 4.8 3.9 3.3 3.5 3.9 5.4 5.6 3.4 5.2 5.1 5.3
##  [631] 4.3 4.5 5.1 5.2 3.9 4.2 5.3 5.5 3.1 5.5 4.2 4.8 5.2 3.8 4.2 5.1 5.2 4.5
##  [649] 5.6 5.5 5.4 4.8 3.5 4.7 1.8 4.4 4.6 5.6 4.4 4.2 3.9 3.6 5.4 3.6 4.8 3.7
##  [667] 4.8 4.4 2.4 4.0 4.5 3.9 3.6 5.3 3.3 3.4 4.1 2.3 5.2 5.1 3.4 5.2 4.7 5.0
##  [685] 3.7 5.6 4.8 5.7 5.5 5.9 3.8 4.8 2.6 3.9 4.8 2.6 4.8 4.4 4.3 3.5 4.9 4.7
##  [703] 5.7 5.2 4.5 4.5 5.6 5.2 5.1 4.5 3.6 4.9 5.5 4.5 5.7 3.6 4.1 5.6 3.7 4.6
##  [721] 6.2 5.2 3.2 5.7 3.8 4.9 3.9 3.7 6.0 5.1 4.0 3.6 5.5 3.1 5.3 5.7 3.9 5.2
##  [739] 6.4 4.4 5.1 4.5 5.1 5.0 4.4 3.6 3.6 4.5 3.6 4.3 4.6 4.8 3.3 2.8 4.3 4.1
##  [757] 4.6 5.0 4.8 4.7 4.9 4.5 4.1 3.3 4.5 4.7 3.2 4.1 4.7 5.8 5.9 5.3 4.0 3.5
##  [775] 6.6 5.2 4.2 6.4 4.6 4.1 5.4 5.4 6.1 3.5 6.5 3.7 5.1 6.4 4.2 3.4 5.1 5.0
##  [793] 5.7 4.7 4.0 4.3 4.3 4.9 4.8 4.1 2.9 4.3 3.8 4.1 4.7 4.1 4.9 3.0 4.5 5.9
##  [811] 4.4 3.4 5.9 6.2 5.1 5.2 5.6 5.3 6.3 4.1 3.9 5.5 4.2 5.7 3.7 5.2 4.9 5.7
##  [829] 4.0 3.8 4.9 4.0 3.8 4.9 3.6 3.9 4.4 6.0 3.6 6.3 4.6 4.1 3.4 4.5 5.4 5.7
##  [847] 5.4 4.8 4.4 5.8 3.5 4.5 4.8 2.9 5.8 4.7 3.1 4.6 4.9 4.6 4.9 4.3 6.2 5.0
##  [865] 3.9 5.3 3.8 5.2 3.8 4.8 5.4 4.6 5.0 3.1 5.3 5.5 5.8 5.0 6.0 2.0 3.5 5.0
##  [883] 4.5 4.1 4.4 5.6 4.0 5.5 3.7 6.9 5.0 4.2 4.4 2.3 3.6 3.7 3.3 3.8 5.2 4.0
##  [901] 5.3 3.5 5.6 5.3 4.7 4.2 3.3 6.3 4.9 4.2 3.4 5.5 3.8 4.5 3.4 5.3 3.6 4.0
##  [919] 4.7 5.3 3.7 6.2 4.5 5.4 3.7 3.7 4.8 5.0 3.5 4.6 4.3 5.9 3.9 4.8 3.9 5.7
##  [937] 4.6 3.8 4.5 4.1 4.6 3.4 4.5 3.7 3.2 5.4 5.3 4.1 4.3 4.5 3.9 5.1 5.3 4.7
##  [955] 3.8 4.6 5.6 4.5 4.7 5.3 5.1 5.4 2.5 4.4 5.7 3.7 5.1 2.8 6.5 5.6 2.1 4.6
##  [973] 5.1 3.7 3.3 3.5 5.2 4.2 4.3 5.5 5.0 3.3 4.6 4.5 5.3 5.7 3.9 2.4 4.8 5.0
##  [991] 3.1 3.9 2.9 6.0 4.5 4.0 5.4 6.1 4.8 4.9
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.308 5.200 5.200 4.900 5.000 4.800 4.600 4.500
## 
## $jack.boot.se
## [1] 0.961975
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
## [1] 0.04114946
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##    6.656619   10.895447 
##  ( 2.905390) ( 4.939441)
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
## [1]  0.1553965 -0.1666818  0.8353146  1.2848476  0.3346622  0.4606029
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
##    [1]  1.248644e-01  9.314880e-01  4.073348e-01  2.378928e-01  8.266331e-02
##    [6] -3.533511e-01  5.051411e-01 -3.571094e-01 -1.913844e-01 -3.294101e-01
##   [11]  2.202674e-01  8.974588e-02  1.414782e+00 -2.155514e-01  4.208976e-02
##   [16] -4.862939e-01  5.955147e-01  9.955894e-01 -1.115795e-01  7.085356e-01
##   [21]  3.205781e-01 -2.078646e-01 -4.814372e-01  2.794813e-02  2.045696e-02
##   [26] -1.905275e-01 -4.293409e-01  1.786431e-01  2.265448e-01  4.103153e-01
##   [31]  2.897949e-01 -7.382220e-02  2.394541e-01 -4.170754e-02 -1.193985e-01
##   [36]  1.824685e-01  5.579152e-01 -3.203382e-01 -1.353472e-01 -1.780656e-01
##   [41]  3.642300e-01  3.861753e-01  3.510501e-01  2.572288e-01  3.591180e-01
##   [46]  1.656825e-01  1.814247e-01  4.424430e-01  3.949627e-01  5.445965e-01
##   [51]  4.922059e-01 -2.742142e-01 -2.941932e-01  8.717411e-03 -2.738183e-01
##   [56] -1.928523e-01 -4.923623e-01 -2.167510e-01  6.256877e-01  3.438848e-01
##   [61]  8.774208e-01  1.175291e-01  1.654373e-01  3.992319e-01  1.709403e-01
##   [66]  4.135156e-01  1.888118e-02  2.459348e-01  1.312449e+00  3.579083e-01
##   [71]  8.250533e-01 -7.638472e-01  1.156150e-01  4.770586e-01  8.514371e-01
##   [76] -1.503285e-01 -2.538610e-01 -7.028305e-01 -3.250747e-01 -6.846366e-01
##   [81]  5.122879e-02 -4.065244e-02 -2.094236e-01  3.893420e-01  8.425690e-01
##   [86]  7.921210e-02 -8.269614e-01  4.121731e-01  1.574053e-01 -5.293748e-01
##   [91] -7.222991e-02 -4.306529e-01  4.635685e-01  3.472950e-01 -1.658945e-01
##   [96] -8.081375e-02 -2.309428e-01  1.581909e-02  1.082174e-01  5.780280e-01
##  [101]  5.038373e-02  4.170105e-02 -5.084781e-01  2.518883e-01  5.585445e-01
##  [106]  5.566665e-01 -1.521346e-02  2.413007e-02  1.452438e-01  1.964994e-01
##  [111]  3.732075e-01  3.145648e-01  2.961068e-02 -8.399738e-01  3.346222e-01
##  [116] -3.267885e-01  6.074581e-01  3.547913e-01 -5.936889e-01  2.040338e-01
##  [121]  1.052222e-01 -9.678901e-02 -4.091247e-01  4.609701e-01 -1.831913e-01
##  [126]  1.580043e-02  5.987812e-03 -2.775563e-01 -3.114722e-02  1.890425e-01
##  [131] -1.759072e-03  1.870425e-01 -3.787889e-01  5.541130e-01  1.162460e-01
##  [136] -6.887075e-01  2.559066e-01  4.763079e-01  3.162122e-02  5.294080e-01
##  [141]  3.166246e-01 -2.220326e-01 -4.521537e-01  3.450133e-01 -3.099922e-02
##  [146]  3.191262e-01 -1.230909e-01  1.140408e-01  4.269207e-01 -3.388864e-02
##  [151]  1.584520e-01 -2.364627e-01  3.495156e-02 -3.177141e-01 -2.106420e-01
##  [156] -2.839368e-01  1.309849e-01  3.088261e-01 -9.782363e-02 -9.718164e-01
##  [161] -4.030697e-01 -2.414182e-01  1.309157e-01 -2.237797e-01  5.263765e-02
##  [166] -1.497970e-01 -3.839083e-01  6.157136e-02  7.726468e-01  1.545159e-01
##  [171]  3.035914e-01 -6.401525e-01  1.086757e+00 -2.374413e-01 -5.022676e-01
##  [176]  9.660528e-02  2.879287e-01  4.264407e-01  8.432167e-01 -1.983929e-01
##  [181]  1.146590e-01  1.184714e-01 -1.843569e-01 -2.727618e-01  6.087651e-01
##  [186] -2.219773e-01  2.746503e-01  6.534690e-01 -2.934281e-01 -1.497970e-01
##  [191] -6.446975e-01 -1.109201e-01  7.141352e-01 -4.421196e-01  4.001989e-01
##  [196] -2.964118e-01  4.321816e-01  2.183214e-01 -3.723561e-01  9.581893e-01
##  [201]  1.010467e-01 -8.799866e-01 -2.971426e-01  1.508439e-01  6.475756e-01
##  [206]  6.727600e-01  2.695367e-01 -3.425819e-02 -2.062653e-01 -5.302281e-01
##  [211]  2.455836e-01  2.996791e-01  1.843802e-01 -5.263403e-02  6.247957e-02
##  [216]  7.491237e-02  6.020301e-01 -4.081955e-01  1.156506e-01 -5.275556e-01
##  [221] -2.447835e-01 -1.552401e-01  2.616693e-03 -2.320397e-01 -8.605298e-02
##  [226] -9.390373e-02 -4.065388e-01  3.062912e-01  2.834600e-01  2.364160e-01
##  [231]  9.518717e-03 -3.424963e-01 -1.645669e-01 -2.467611e-01  8.715084e-02
##  [236]  3.485269e-01 -1.152603e-01  1.326714e-01  4.983940e-01 -1.017600e+00
##  [241] -2.664269e-01  4.867535e-01  1.529716e-01  6.056231e-02 -5.716596e-01
##  [246] -1.859530e-01 -2.662146e-01  6.577878e-01 -1.913514e-01  3.151215e-01
##  [251] -6.441208e-02 -5.008276e-01  3.901033e-01  2.701210e-01 -4.511517e-01
##  [256]  8.369751e-01 -2.182860e-02 -1.098572e-01 -5.640160e-01 -3.692367e-01
##  [261] -1.083904e-01  4.918764e-01  5.657681e-01  1.422781e+00  2.059368e-01
##  [266]  2.464526e-01 -6.069979e-02  7.352476e-01  7.573062e-02 -1.837349e-01
##  [271]  2.717695e-01  3.973784e-01  5.156218e-01  6.669092e-01  1.308618e-01
##  [276]  8.057089e-02 -6.413022e-01  8.309105e-01 -3.657907e-02 -2.358106e-01
##  [281] -2.016347e-01 -4.248948e-02  2.843475e-01  1.496299e-01 -3.602500e-01
##  [286]  8.239766e-02  2.082902e-01 -2.188489e-01 -3.042078e-01 -1.431625e-01
##  [291] -1.434201e-01  3.088261e-01  5.629421e-01 -1.886604e-01  6.368222e-02
##  [296] -1.018080e+00  1.212746e-01  3.838362e-01  1.781905e-01 -1.170547e-01
##  [301]  2.554187e-01  3.625323e-02 -2.162986e-01 -1.701998e-01  3.374118e-01
##  [306] -1.790809e-01  9.155555e-01 -1.396869e-01  5.892840e-02 -4.453472e-01
##  [311]  1.309843e-01 -8.117614e-02  8.504970e-02  1.653034e-01  1.015547e-01
##  [316] -1.987246e-01 -5.693203e-03  6.754464e-02  8.239480e-01  9.548867e-01
##  [321]  3.240652e-01  1.797978e-01 -3.402353e-01 -6.066168e-02  6.450630e-01
##  [326]  1.163647e-01 -3.291981e-01  6.339764e-01 -4.838957e-01 -3.034210e-02
##  [331] -2.533818e-01  1.582971e-01  2.724611e-01  1.704497e+00  3.763495e-01
##  [336]  4.868161e-02  2.558870e-01 -1.206402e-02  3.897220e-01  2.721395e-02
##  [341]  2.683224e-01 -1.422803e-01 -2.415594e-01  9.443097e-01 -9.898072e-02
##  [346]  4.972773e-01  4.948946e-01  3.966577e-02 -1.187744e-01  8.077573e-02
##  [351]  6.710371e-01 -4.774707e-01  4.797046e-02 -1.876154e-01 -3.163537e-01
##  [356] -1.973102e-01  4.847050e-01  6.911744e-01 -3.986466e-01  3.740613e-02
##  [361]  5.637046e-01 -1.075719e-02 -1.152653e-01  4.842758e-01 -3.989131e-01
##  [366]  5.091892e-02 -8.621422e-03  5.364278e-01  5.144800e-01 -2.279375e-01
##  [371] -3.486371e-01 -1.422803e-01 -1.154849e-01  3.424102e-01  2.457238e-01
##  [376]  1.738421e-02 -4.035489e-01  2.506348e-01  7.464224e-01  3.194035e-01
##  [381]  1.467205e-01  6.735876e-01 -1.189970e-01 -7.443386e-02 -1.160767e-01
##  [386] -5.388494e-01  5.252815e-01 -4.056910e-01 -3.401969e-01  6.930169e-01
##  [391]  1.268772e-01  5.562805e-01  3.896700e-01  4.206781e-01 -4.199309e-01
##  [396] -8.103005e-01 -3.949297e-01  6.904120e-01 -6.390776e-02 -2.372341e-02
##  [401]  2.841102e-01  1.448630e-01 -4.420579e-01  2.721395e-02  6.227130e-03
##  [406] -2.035279e-01  6.710551e-01  2.030492e-01  2.578370e-01 -3.190975e-02
##  [411] -1.400007e-01  6.976915e-01  4.179956e-01 -1.559013e-01  5.961827e-01
##  [416] -1.419932e-01  3.887220e-01  9.473812e-02  3.414052e-01  1.703224e-01
##  [421] -1.097610e-01  3.858096e-01 -8.945620e-01  3.237908e-01 -7.696088e-01
##  [426] -4.281654e-01 -6.456887e-01 -4.443648e-01  1.797966e-01 -2.954959e-01
##  [431] -6.978314e-02 -1.451705e-01  4.970269e-01 -8.667838e-01 -1.527576e-01
##  [436] -2.604573e-01 -2.027552e-01  4.087747e-05 -1.363681e-01  3.181191e-01
##  [441]  6.016724e-01 -1.816238e-01  2.502152e-02 -2.483552e-01 -3.151872e-01
##  [446]  5.861081e-01  1.139262e+00  1.042715e+00  2.387030e-01 -2.732968e-02
##  [451]  7.241888e-01  7.048301e-01 -6.271520e-01  4.603385e-01  4.731864e-01
##  [456] -1.174834e-01  1.086313e-01 -2.966985e-01  2.119484e-01 -2.815533e-01
##  [461]  4.104239e-01 -1.281513e-02  1.153444e-01 -3.403658e-01 -2.751879e-01
##  [466] -4.979693e-01  1.017588e-01 -3.034210e-02 -6.090338e-01  5.617861e-01
##  [471]  3.924255e-01 -9.751015e-02  2.000377e-02  2.715883e-01  3.904577e-01
##  [476]  5.124111e-01  3.010978e-01 -3.018022e-02 -3.164824e-01  1.929243e-02
##  [481]  9.201919e-02  6.172151e-02  1.310875e-01 -3.011664e-01  7.213053e-01
##  [486] -3.856500e-01  8.285179e-01 -1.961384e-02 -2.336423e-01 -4.771875e-01
##  [491]  1.065887e-02 -5.892143e-01  3.352533e-01 -3.083062e-01  9.740582e-01
##  [496]  3.453765e-01  7.692003e-01  1.093766e+00  1.275486e+00  1.186484e-02
##  [501] -3.486705e-01 -5.628846e-01  3.180200e-02  4.265278e-01  4.574450e-01
##  [506]  1.043782e+00 -1.239138e-01  5.004795e-02  3.880515e-01  2.458727e-01
##  [511]  6.247957e-02 -7.659328e-01  2.417840e-02 -4.094927e-01  2.553775e-01
##  [516] -6.643443e-01 -5.200480e-01  9.598052e-03  1.067281e-01  1.075926e-01
##  [521]  2.363310e-01 -1.857210e-01 -1.062320e-01 -1.079995e+00  4.104206e-01
##  [526] -5.063296e-01 -2.882670e-01 -7.959798e-01  3.501653e-01 -1.886079e-01
##  [531]  4.056751e-02  7.303893e-01  1.857033e-02 -1.907467e-02  1.263335e-01
##  [536] -1.282509e-01 -4.413792e-01  9.457422e-02 -1.961340e-01  1.522869e-01
##  [541] -3.999287e-02 -1.153334e+00 -1.037723e-01 -5.437438e-01  3.777805e-03
##  [546] -1.618819e-01  3.440226e-01  1.838540e-01 -8.344381e-02 -4.030442e-02
##  [551]  1.998703e-02 -2.543803e-01 -1.908346e-01  1.501070e-01  4.482658e-01
##  [556]  3.562999e-02 -1.678402e-01  2.115631e-01  1.235467e-01  4.758801e-01
##  [561]  3.474977e-01  6.056974e-01 -1.431625e-01  1.254459e-01  6.008068e-01
##  [566] -3.177834e-01 -1.959285e-01  6.954754e-01  6.738760e-02  1.600044e-01
##  [571]  4.195127e-02 -6.995380e-01  6.276648e-01  1.829404e-01 -4.048921e-02
##  [576]  6.260712e-01  8.338629e-01 -2.067092e-02 -1.973213e-01  3.530552e-01
##  [581]  1.886135e-01  4.253090e-02 -9.002235e-02 -8.135975e-01  3.741419e-01
##  [586] -4.156237e-01 -2.292646e-01 -2.788867e-02  1.486134e+00  2.674690e-01
##  [591]  4.556571e-01 -3.190361e-02  1.767877e-01  7.264505e-01  1.474538e-01
##  [596]  9.167117e-01 -2.190197e-01  2.928686e-01 -2.548745e-01  1.229455e-01
##  [601] -4.957571e-01  6.070731e-02  5.063525e-01  1.859398e-01  4.151664e-01
##  [606] -1.921279e-01 -2.379486e-02  1.013450e-01  1.283272e+00 -2.527790e-01
##  [611]  2.098880e-01  7.199293e-01 -5.258517e-01 -9.026300e-02 -1.295224e-01
##  [616] -1.156899e-01 -1.300923e-03 -5.906785e-01 -2.910042e-01 -3.364946e-01
##  [621] -3.290679e-01  8.859849e-01  9.885939e-02 -7.612059e-01 -5.074031e-01
##  [626] -3.425718e-01  2.238583e-01  6.272427e-01 -3.743701e-01  6.721334e-02
##  [631]  3.257035e-02 -7.096340e-01  2.091665e-01 -8.253722e-01 -2.514599e-01
##  [636] -2.733685e-01  2.868372e-01 -2.849232e-03 -3.368171e-01  8.884872e-02
##  [641] -1.511044e-01  2.360786e-01 -8.223686e-01 -5.284757e-03  3.424102e-01
##  [646] -3.931425e-01 -1.109518e-02  1.884263e-01  8.236490e-01  3.271381e-01
##  [651] -9.181520e-03  7.301034e-01  6.512305e-02 -1.428352e-01  5.426722e-01
##  [656] -1.314187e-01 -8.728848e-01  3.574990e-01 -6.759306e-02  9.610148e-01
##  [661]  1.280078e-01  2.503548e-01  9.746505e-02 -3.276406e-01 -6.013361e-01
##  [666]  2.897949e-01 -4.849842e-01  4.611736e-01  4.424430e-01 -3.280715e-01
##  [671]  2.144834e-01 -4.551418e-01 -1.369689e-01  2.266359e-01 -3.596636e-02
##  [676] -1.595079e-01  1.291688e-01  6.841450e-01  9.082464e-01  1.974766e-01
##  [681] -2.895754e-02 -1.141932e-01 -1.553693e-01  7.115959e-02  1.256677e+00
##  [686]  3.258557e-01  1.919837e-01  4.069130e-02  9.274356e-01 -2.974921e-01
##  [691]  2.804515e-01  6.381541e-01 -1.115150e-01  9.141509e-01  4.560778e-01
##  [696]  7.143779e-01 -1.577128e-02 -2.763202e-02 -9.123873e-01 -1.927630e-01
##  [701]  7.266396e-02  4.960342e-01 -4.286933e-01  5.366887e-02  2.759993e-01
##  [706] -1.711207e-01 -3.359031e-01 -1.759740e-01  3.192273e-01  1.214959e+00
##  [711]  1.281842e-02 -2.570641e-01  5.808135e-01  3.493827e-01  8.049280e-01
##  [716]  1.482182e+00 -5.114536e-01 -3.212951e-01  4.134157e-01  8.334917e-02
##  [721] -1.841290e-01 -3.556498e-01 -6.230101e-01  1.077536e-01  1.667808e-01
##  [726]  5.072021e-01  1.617718e-01  4.377634e-01  2.402678e-01  1.250824e-01
##  [731] -9.623305e-01 -2.443577e-01 -3.079924e-01 -7.848592e-01  2.674690e-01
##  [736]  9.216179e-01  4.925973e-01  3.363684e-01  3.186036e-01 -2.353333e-01
##  [741] -6.377721e-02 -4.218349e-01 -2.994571e-01 -1.429816e-01 -5.473788e-01
##  [746] -6.853907e-01 -2.994571e-01  3.320121e-02  2.192663e-01 -3.366174e-02
##  [751]  2.952837e-01  2.610721e-01 -2.427738e-01 -2.562390e-02 -7.315184e-02
##  [756] -2.934281e-01  2.597937e-01 -3.639489e-01 -3.257705e-01  7.708416e-01
##  [761]  4.834132e-01  1.063369e-01 -1.786780e-01  4.873975e-01 -5.562248e-01
##  [766]  1.774033e-01 -3.153819e-01 -3.128627e-01  1.403747e-01  1.568480e-01
##  [771] -1.056475e+00  6.375678e-02  2.021748e-01 -7.000794e-01  7.141352e-01
##  [776]  2.233926e-01 -4.350367e-01 -2.040888e-01  9.115927e-01  7.394643e-01
##  [781] -5.953510e-01  8.886907e-01 -9.511924e-01  1.077014e-01 -1.583000e-01
##  [786]  1.144425e+00 -5.720915e-01  9.079095e-01  4.008283e-01  7.587584e-01
##  [791] -2.458345e-01  3.908220e-01  8.915185e-01 -3.506075e-01 -2.053645e-01
##  [796]  4.788584e-01 -1.506216e-01  3.070224e-01  4.555659e-01 -8.247519e-01
##  [801]  9.746505e-02  3.079980e-02  2.671842e-01  2.897883e-01  5.793987e-01
##  [806] -1.726866e-01  1.063188e+00  5.997109e-02  1.842992e-01  9.448066e-01
##  [811] -2.926740e-02  1.036759e-01  2.670848e-01 -5.640098e-01  1.132922e-01
##  [816]  1.803301e-01  4.450598e-01 -1.170942e+00  6.201095e-02  1.460078e-01
##  [821]  5.515187e-01 -3.706986e-01 -1.413669e+00 -6.143736e-01  9.362339e-02
##  [826] -2.014446e-01 -3.800446e-01 -3.720739e-01 -9.547799e-01 -1.957961e-02
##  [831] -1.408761e-01  1.531324e-01  3.607887e-01 -4.279954e-02  3.312578e-01
##  [836] -1.118093e-01  3.833885e-01  1.720975e-01  9.962475e-01 -5.804727e-02
##  [841] -3.618603e-01  4.191415e-01 -1.272739e-01  4.784384e-01  6.051012e-01
##  [846] -1.285297e-01  3.503864e-01  1.496272e-01 -8.453685e-02  7.761837e-01
##  [851]  5.195394e-02  3.589825e-02 -1.927029e-01 -6.850059e-02 -5.726941e-01
##  [856]  1.208852e-01  6.352304e-01 -2.081140e-01  8.360055e-01 -2.812535e-01
##  [861]  7.581578e-02 -2.303046e-01 -4.171230e-01 -1.855443e-01 -3.188598e-02
##  [866] -3.233671e-02  4.036727e-02 -1.456390e-01 -2.868441e-01 -6.884831e-02
##  [871] -3.692943e-01  8.706125e-01  1.753361e-01  4.593184e-01 -5.469941e-01
##  [876] -7.609199e-01 -2.977485e-01 -3.961164e-02  4.224436e-01  5.507437e-01
##  [881]  2.358282e-01  6.756979e-01 -2.303824e-01 -2.787903e-01  2.307915e-01
##  [886]  1.478655e-01  8.411069e-02  5.678260e-01  3.833709e-03 -7.652341e-01
##  [891]  4.312812e-01 -2.616233e-01  1.076824e-02  8.585340e-01  8.911838e-02
##  [896]  2.565970e-01  7.381687e-01  6.438408e-02  2.679837e-01  3.620335e-01
##  [901]  1.479967e-01 -2.826696e-02 -1.373891e-01  9.038053e-01  9.915928e-02
##  [906] -1.583000e-01 -1.698026e-01  4.844224e-01  4.226974e-01 -2.410375e-01
##  [911] -4.790420e-02 -3.648473e-01  4.287314e-02  5.238145e-01 -4.136940e-02
##  [916]  3.388182e-01 -3.903311e-01  8.606698e-01  9.853265e-02  1.103981e-01
##  [921] -2.349247e-01  4.213026e-02 -5.518392e-02  6.700601e-01  1.410134e-01
##  [926] -7.210331e-02 -2.223201e-01  4.555659e-01 -2.826696e-02 -1.239138e-01
##  [931] -6.085538e-02 -7.070413e-01  4.834431e-01 -3.839720e-01 -2.787665e-03
##  [936]  9.520477e-02  8.784016e-02 -6.544963e-01 -4.332027e-02  1.368047e-01
##  [941]  5.461364e-01  2.062403e-01  3.173848e-01 -5.214915e-01 -1.369689e-01
##  [946] -1.010460e+00 -2.449769e-02  5.035939e-01 -4.729381e-01  3.038303e-01
##  [951]  6.892540e-01 -5.394823e-01  7.164238e-02 -5.485711e-01  5.525376e-01
##  [956] -1.790809e-01  2.194707e-02 -1.667449e-01 -4.513501e-01  4.311467e-01
##  [961]  8.197330e-01 -4.957235e-02  2.367200e-01  1.019947e+00  1.274830e-01
##  [966] -4.033014e-01  3.053956e-01  4.479244e-01  8.938677e-02  5.194186e-01
##  [971]  5.466676e-01  3.757059e-01  3.268810e-01 -4.186304e-01  5.627300e-01
##  [976]  4.368797e-01 -3.922603e-01  2.210392e-01  9.413269e-01  1.133069e-01
##  [981] -1.128590e-01  1.300679e-01 -4.881767e-01  6.339642e-01  1.181456e-01
##  [986]  1.509870e-01 -4.126112e-01  5.581431e-02  3.668597e-01 -1.052699e+00
##  [991] -7.483330e-01  3.179200e-01  8.883326e-01  4.095123e-01 -3.173120e-02
##  [996]  2.246878e-01  3.871033e-01  7.323513e-02  1.948507e+00 -3.439961e-01
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
```

```r
fit2
```

```
##       mean          sd    
##   0.61094862   0.22427622 
##  (0.07092237) (0.05014552)
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
## [1] -0.42242374 -1.54207026  0.05638018 -0.20603893 -0.18264197  0.12816589
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
## [1] -0.0506
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8887686
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
##     original      bias    std. error
## t1*      4.5 0.001001001   0.9154754
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 7 8 
## 1 1 1 1 2 4
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
## [1] 0.0306
```

```r
se.boot
```

```
## [1] 0.898839
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

