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
## 3 4 6 7 8 9 
## 1 2 1 3 2 1
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
## [1] -0.0071
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
## [1] 2.641149
```

```r
UL.boot
```

```
## [1] 6.344651
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
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
##    [1] 4.2 4.7 4.8 4.2 4.0 6.0 2.9 3.6 4.5 5.2 3.7 3.6 4.3 4.3 5.3 3.5 5.6 3.6
##   [19] 5.7 4.7 4.6 5.6 6.0 5.6 4.0 3.2 3.5 3.7 2.9 4.8 3.4 3.7 5.5 3.9 5.4 4.2
##   [37] 5.9 6.0 4.9 3.7 4.8 3.9 4.6 3.6 3.9 4.9 5.9 4.3 5.3 4.8 3.3 5.6 5.0 4.5
##   [55] 4.2 4.7 4.9 3.3 3.4 4.2 5.4 4.2 4.3 5.2 5.5 5.6 4.8 5.2 5.5 4.8 2.9 3.6
##   [73] 3.7 4.8 3.6 4.9 4.9 3.9 4.4 5.2 5.1 5.2 4.4 5.1 3.9 5.3 4.2 2.6 4.8 4.1
##   [91] 4.7 3.6 6.0 4.1 4.0 3.6 2.7 3.2 4.2 4.5 4.4 5.3 5.2 4.3 5.0 3.7 5.2 4.8
##  [109] 5.0 5.5 4.1 4.7 3.7 4.4 3.8 3.8 3.7 4.3 6.0 4.7 5.8 3.5 6.2 3.2 4.8 4.9
##  [127] 3.9 4.4 6.1 4.6 4.5 4.3 4.6 4.1 4.7 3.4 5.1 4.3 4.9 4.9 3.5 5.7 5.0 3.2
##  [145] 3.9 4.4 3.2 3.9 4.5 2.4 4.7 6.5 3.6 5.1 5.7 5.5 2.5 4.5 4.5 3.9 4.5 3.1
##  [163] 3.7 3.8 5.8 5.6 4.9 6.6 5.1 4.1 5.0 2.7 3.9 4.6 4.6 5.7 2.5 4.0 6.7 2.7
##  [181] 2.9 5.0 6.1 5.1 5.0 4.2 4.8 5.1 4.9 4.0 3.6 3.5 5.4 4.3 3.7 3.5 5.5 4.1
##  [199] 5.2 4.2 6.6 4.6 3.5 4.5 6.4 2.7 5.2 4.5 4.4 4.5 5.7 5.5 6.5 4.6 5.1 6.2
##  [217] 5.7 5.3 4.8 2.5 5.5 4.0 5.2 5.3 3.9 4.2 3.4 3.8 4.9 4.5 3.6 4.9 4.4 3.0
##  [235] 4.1 4.3 5.0 5.4 4.1 4.7 4.4 5.1 5.2 2.8 3.6 4.8 3.4 3.7 3.8 4.7 5.5 4.8
##  [253] 6.3 2.1 4.2 4.1 4.6 4.7 3.4 5.6 3.9 6.3 4.7 4.8 4.4 5.9 4.9 6.9 5.9 4.5
##  [271] 3.1 4.5 2.9 4.3 5.8 4.5 4.8 5.1 4.9 4.8 5.1 4.5 4.7 4.6 5.1 5.1 3.5 5.9
##  [289] 4.8 2.9 5.2 3.7 5.5 4.2 4.0 3.9 4.4 4.6 4.5 4.5 2.4 3.1 4.5 5.0 4.4 4.6
##  [307] 4.2 4.2 4.1 4.7 4.6 4.3 5.3 4.6 6.4 4.3 4.3 4.6 4.5 4.7 2.4 3.9 4.5 4.6
##  [325] 4.3 6.0 6.4 5.7 7.6 4.3 3.4 3.6 4.9 4.6 4.2 5.1 4.3 3.5 5.4 4.2 2.1 4.7
##  [343] 4.2 3.6 5.5 4.2 4.9 4.4 3.8 4.0 4.5 5.2 5.3 4.3 5.0 5.7 4.1 6.3 5.3 3.0
##  [361] 4.5 2.4 6.4 5.3 4.4 5.1 5.4 3.9 5.0 3.7 5.4 4.8 5.0 4.2 3.8 4.2 5.8 4.7
##  [379] 4.4 4.0 3.7 2.9 3.4 4.6 3.4 5.5 2.4 4.4 5.1 6.2 5.0 5.4 4.8 5.3 5.5 5.5
##  [397] 4.4 6.0 4.1 5.6 5.2 4.2 5.7 2.7 3.6 4.4 2.2 5.1 4.7 3.6 4.7 4.6 4.7 3.8
##  [415] 5.4 4.8 5.3 5.2 5.4 3.2 3.9 3.4 5.7 4.8 4.7 4.7 5.2 7.1 3.5 4.4 4.7 5.0
##  [433] 5.9 4.9 4.4 4.3 3.8 5.9 4.5 4.0 2.8 5.1 3.3 2.6 5.8 2.9 5.5 4.0 2.9 4.9
##  [451] 4.7 3.8 2.5 5.3 3.8 3.9 5.7 4.6 6.2 3.7 2.7 3.2 4.7 3.7 4.1 5.0 4.0 4.0
##  [469] 4.3 3.3 4.8 3.9 6.2 4.7 4.3 4.0 4.0 5.1 5.6 4.7 5.3 5.8 4.2 5.4 5.8 4.6
##  [487] 3.3 4.3 4.3 5.4 3.8 4.8 3.2 6.0 4.9 5.2 4.5 3.6 4.1 4.6 5.1 1.8 4.2 4.2
##  [505] 3.1 5.8 3.1 5.5 3.8 4.4 5.0 3.9 4.4 5.0 3.7 3.4 5.5 4.8 6.5 3.2 4.7 4.6
##  [523] 5.0 5.1 5.1 5.2 4.6 4.8 4.4 5.9 4.2 4.6 4.1 6.0 6.6 4.3 2.9 5.4 4.3 4.9
##  [541] 3.8 5.2 3.8 4.6 4.2 3.7 3.9 4.1 6.2 5.9 3.7 3.8 4.2 5.5 5.4 3.5 4.6 4.9
##  [559] 3.9 4.4 2.8 4.1 6.7 6.1 5.7 5.5 2.5 4.3 6.3 4.9 4.4 3.7 4.5 2.7 4.7 3.8
##  [577] 4.4 2.9 4.2 2.8 4.3 4.3 5.9 5.4 5.2 4.8 4.8 3.8 4.5 5.3 4.8 3.3 3.9 4.3
##  [595] 4.0 4.6 4.5 4.0 4.3 5.6 4.0 5.4 5.5 2.6 4.7 4.8 3.0 5.1 6.6 6.0 5.6 6.1
##  [613] 2.0 1.5 4.0 5.5 4.5 3.7 4.4 4.3 5.2 4.9 3.7 5.6 5.9 4.6 4.7 5.0 4.1 5.1
##  [631] 4.3 4.1 3.7 5.4 4.8 5.4 3.9 3.3 5.5 4.2 5.6 5.2 3.4 5.3 4.0 3.1 3.7 6.3
##  [649] 4.8 6.0 3.5 3.8 4.9 6.0 4.8 5.8 4.1 4.4 4.4 4.9 5.3 3.6 6.1 3.1 5.5 5.3
##  [667] 4.3 5.0 5.7 5.2 4.1 5.6 6.5 4.8 3.3 4.4 4.8 4.7 4.8 4.1 5.9 4.8 3.3 6.6
##  [685] 4.1 6.6 5.7 3.9 5.5 4.0 4.1 4.4 5.5 3.9 3.1 4.2 3.6 5.3 3.9 3.4 4.7 2.7
##  [703] 5.6 4.7 3.8 4.9 4.7 5.2 5.4 4.4 5.0 5.5 3.9 5.1 4.4 4.0 3.2 4.5 5.3 3.3
##  [721] 4.3 4.2 4.6 4.2 3.6 5.7 5.1 7.1 4.6 4.2 3.7 5.2 4.1 5.2 4.7 4.7 3.9 3.7
##  [739] 4.6 4.0 2.9 6.0 4.2 4.5 4.9 2.8 2.5 4.9 5.4 3.1 5.1 3.9 4.0 5.4 4.8 4.2
##  [757] 5.2 4.0 4.2 4.2 5.0 4.0 5.7 4.9 3.8 3.6 3.3 4.6 3.9 5.2 3.6 3.9 4.8 4.8
##  [775] 3.3 4.8 4.3 4.1 4.8 6.4 5.1 2.7 6.1 5.8 3.7 5.9 3.4 5.3 3.5 3.4 4.4 4.8
##  [793] 4.8 5.1 4.1 5.8 4.7 4.7 3.7 5.2 4.5 5.1 4.1 4.7 4.1 2.9 3.9 3.1 3.8 5.4
##  [811] 6.2 4.1 5.6 4.5 4.0 2.7 4.3 4.6 5.7 4.1 4.7 4.6 3.4 4.5 5.2 5.7 5.3 5.6
##  [829] 3.5 4.5 5.7 5.0 5.4 5.7 6.3 5.5 4.9 6.5 5.2 5.2 3.9 4.8 4.2 5.2 5.5 4.1
##  [847] 4.6 1.7 5.2 5.6 4.5 3.5 4.1 4.7 3.7 3.7 3.9 4.5 5.0 5.0 4.4 5.1 5.6 6.4
##  [865] 2.5 3.5 4.2 4.0 3.5 4.3 4.9 4.8 4.1 3.9 4.0 3.6 5.0 5.0 4.9 2.7 4.5 4.4
##  [883] 3.7 2.6 4.1 5.0 4.5 4.9 4.5 3.6 3.2 5.5 3.5 4.0 5.0 5.0 4.4 4.0 4.6 3.6
##  [901] 3.3 5.3 3.4 5.2 6.5 3.5 3.2 3.3 3.0 3.1 4.7 5.3 5.0 2.6 3.0 4.1 4.7 4.6
##  [919] 4.6 3.5 4.5 3.5 5.6 3.6 4.9 5.6 3.0 4.4 3.7 4.7 4.5 4.8 4.8 2.4 4.0 3.6
##  [937] 3.9 4.4 5.4 4.7 4.1 4.0 3.9 4.1 5.3 4.4 3.1 4.2 4.5 4.1 3.1 6.1 4.4 3.8
##  [955] 4.3 4.1 5.4 4.6 6.8 4.4 4.8 4.5 3.7 5.0 4.4 5.1 3.0 4.6 6.0 4.3 4.2 4.7
##  [973] 5.9 2.8 4.9 3.3 4.4 5.0 3.1 4.3 4.5 4.7 6.7 3.1 5.6 4.3 4.7 4.5 5.7 3.4
##  [991] 4.2 6.3 4.2 5.2 2.9 2.8 3.6 3.0 4.6 4.7
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
##   2.5%  97.5% 
## 2.6975 6.4000
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
##    [1] 4.3 5.2 6.1 3.2 4.1 4.0 3.8 3.6 5.2 5.2 3.5 4.6 4.1 5.3 3.3 4.5 3.7 4.9
##   [19] 4.5 3.5 5.0 3.0 2.3 2.8 4.2 4.1 5.5 3.8 4.7 5.3 5.7 4.4 6.4 4.5 5.3 4.3
##   [37] 5.3 5.3 3.8 4.2 4.3 4.0 3.3 4.3 4.7 4.2 3.7 3.3 4.7 3.1 4.9 5.7 4.3 5.1
##   [55] 5.1 5.3 5.0 4.6 7.4 5.2 3.0 5.8 4.4 5.8 5.4 4.6 3.6 5.8 4.1 3.2 5.7 5.0
##   [73] 4.7 4.6 6.0 5.8 2.2 4.5 4.3 6.2 4.7 4.4 5.4 6.4 4.2 4.3 5.7 3.7 2.2 5.2
##   [91] 5.1 4.4 6.6 3.7 5.6 3.4 3.8 4.2 6.0 5.0 2.6 4.9 5.6 4.8 4.9 3.5 4.2 4.9
##  [109] 2.5 3.6 2.5 4.5 5.0 3.4 5.1 5.1 2.7 5.3 4.9 5.0 5.3 4.9 4.9 4.4 2.8 3.2
##  [127] 5.0 5.1 4.3 3.2 4.1 5.0 4.7 5.0 3.7 5.5 3.4 5.5 5.3 5.4 3.5 4.7 5.4 3.9
##  [145] 4.5 5.0 3.7 3.9 4.0 4.6 5.0 3.8 5.2 3.7 3.9 4.5 3.1 4.8 4.2 3.8 5.3 4.4
##  [163] 3.7 4.8 4.5 5.2 2.9 4.8 2.8 4.3 3.7 3.2 3.3 5.1 4.6 3.6 4.5 5.2 3.9 3.7
##  [181] 4.1 4.2 4.7 5.2 3.0 3.8 4.6 4.9 3.8 6.2 5.8 3.9 4.0 5.0 5.0 5.9 4.2 4.9
##  [199] 4.7 3.6 2.5 4.6 5.3 5.9 4.4 4.7 3.5 3.3 3.2 3.5 3.3 3.7 3.4 2.7 3.7 5.7
##  [217] 3.4 4.7 4.4 3.3 4.3 4.3 2.8 4.1 4.4 4.9 4.5 4.1 3.3 3.8 5.4 3.7 5.1 4.3
##  [235] 2.7 4.7 3.0 3.9 6.1 5.0 4.9 5.7 4.5 4.8 5.3 4.9 4.4 4.6 4.6 3.2 5.1 3.1
##  [253] 5.5 3.9 4.3 4.5 3.9 4.9 2.6 3.2 5.9 3.7 4.5 3.2 3.3 4.5 4.9 5.3 5.3 5.3
##  [271] 4.6 5.1 6.7 4.0 5.8 4.5 3.8 4.9 4.1 4.6 4.3 3.4 5.5 3.4 4.3 4.1 4.1 5.0
##  [289] 4.8 4.7 3.5 5.2 1.9 2.4 4.0 4.3 4.5 5.0 5.0 4.4 4.9 3.6 6.4 4.1 6.6 3.4
##  [307] 4.7 3.9 3.6 4.5 5.5 3.7 4.4 5.4 3.4 5.7 5.3 5.2 4.8 4.0 4.9 5.2 6.1 3.2
##  [325] 5.7 5.8 5.5 5.2 5.2 5.5 2.3 4.7 4.8 5.7 4.3 3.3 4.8 5.8 4.7 5.7 4.0 2.8
##  [343] 5.3 4.2 3.7 5.2 6.2 2.3 4.6 4.8 3.6 4.8 4.9 5.1 6.0 5.7 5.5 5.4 5.3 4.7
##  [361] 4.5 3.8 4.7 5.0 3.8 5.2 3.7 5.6 1.8 4.6 4.0 4.7 6.0 5.6 4.3 3.5 4.9 5.8
##  [379] 4.3 5.8 4.3 5.5 3.5 3.5 6.9 4.4 4.7 4.2 5.4 3.5 5.3 3.5 4.8 3.9 4.6 4.3
##  [397] 3.6 4.5 5.8 4.5 3.9 4.2 4.9 4.5 5.4 5.0 4.4 5.2 4.8 5.1 4.7 4.5 5.8 4.4
##  [415] 5.3 5.0 3.8 5.1 4.9 3.8 4.6 4.9 3.8 4.1 4.9 5.3 4.4 3.9 4.3 6.2 3.8 4.6
##  [433] 4.0 4.1 3.7 3.7 4.7 3.9 3.7 3.6 4.5 5.7 5.9 5.3 3.5 3.8 4.5 5.5 2.8 4.4
##  [451] 4.3 2.9 2.9 2.8 4.9 4.9 3.0 5.5 3.9 6.5 6.0 4.8 3.7 3.9 5.7 3.9 4.9 4.7
##  [469] 4.7 4.1 4.8 3.6 4.1 5.9 3.6 6.8 3.8 5.9 4.3 3.7 3.8 4.2 2.8 3.2 4.0 5.6
##  [487] 5.0 5.6 5.9 4.0 4.2 3.1 4.2 6.5 3.6 5.0 4.2 3.4 5.3 4.8 5.3 4.6 4.8 5.1
##  [505] 5.3 3.4 3.4 3.9 4.0 3.4 4.6 4.8 4.1 3.2 5.2 6.0 4.7 5.9 5.9 4.8 4.6 3.0
##  [523] 4.4 5.9 4.1 5.0 3.5 3.8 4.1 3.5 5.5 4.0 4.2 5.5 6.2 3.8 4.0 3.3 4.2 5.3
##  [541] 4.7 5.6 4.7 5.7 4.1 4.7 4.5 6.0 3.1 3.8 2.9 4.9 4.9 5.7 5.2 3.6 4.7 6.0
##  [559] 5.3 3.6 5.2 4.8 5.1 6.7 5.2 3.6 3.7 3.2 4.8 4.8 4.6 5.0 4.8 3.8 5.2 4.0
##  [577] 4.4 4.7 5.3 3.2 5.4 4.1 4.6 6.0 5.3 3.2 3.9 5.3 4.6 4.3 5.2 3.6 5.8 5.7
##  [595] 4.8 2.7 6.2 5.0 5.3 4.0 2.3 3.7 5.5 4.9 4.3 4.0 5.6 5.1 4.7 3.9 5.5 4.6
##  [613] 5.2 4.3 5.1 3.7 5.6 4.7 5.5 4.3 3.8 3.8 3.8 3.9 5.8 6.9 2.6 5.2 4.0 3.6
##  [631] 5.0 4.8 4.4 4.1 2.7 5.2 5.6 4.9 4.6 3.9 4.8 3.6 4.0 4.7 5.3 6.1 5.4 3.7
##  [649] 4.6 4.8 4.1 5.3 4.1 4.7 5.4 4.8 5.8 4.7 4.7 4.7 5.3 4.1 4.6 4.5 5.2 4.4
##  [667] 5.3 4.2 3.0 3.9 4.2 5.4 4.9 3.3 3.6 6.0 3.6 4.1 2.8 4.9 4.7 5.0 6.3 3.9
##  [685] 3.6 5.6 3.8 2.0 5.4 4.5 2.9 4.4 3.7 3.2 3.1 3.4 3.1 3.8 3.0 3.4 4.8 2.9
##  [703] 4.8 4.4 3.9 5.8 2.0 4.6 4.7 5.6 3.4 5.9 3.8 4.7 4.6 4.3 4.7 3.6 4.7 5.6
##  [721] 4.6 4.2 3.9 4.4 6.2 3.8 3.9 3.2 3.8 4.0 4.6 5.2 5.7 3.2 4.5 5.3 4.2 5.2
##  [739] 4.1 6.3 6.3 5.6 5.6 4.7 4.6 4.3 5.4 5.8 5.3 4.1 6.1 5.4 1.7 5.1 3.5 5.4
##  [757] 4.7 4.5 4.0 2.9 5.2 4.5 5.4 5.7 3.5 5.1 2.5 3.1 4.3 6.0 2.8 3.8 6.0 4.7
##  [775] 4.0 4.9 4.6 3.0 4.0 5.0 4.4 6.3 3.8 5.8 6.4 4.3 2.6 6.4 6.1 5.3 4.8 4.5
##  [793] 3.9 3.8 4.4 4.0 4.3 4.1 4.7 6.1 3.7 3.8 4.9 4.3 3.9 5.1 4.6 5.2 5.3 4.0
##  [811] 5.5 4.5 3.0 4.5 4.5 3.4 1.9 4.8 4.5 5.8 3.7 3.9 5.4 4.2 4.5 4.3 3.8 4.9
##  [829] 3.4 3.3 4.0 4.7 5.3 5.0 4.9 2.9 4.3 4.7 3.6 4.8 4.7 2.9 2.1 3.6 4.6 3.6
##  [847] 4.4 4.3 5.5 3.5 4.6 5.7 4.1 5.1 4.9 2.3 4.6 5.4 4.8 3.7 3.7 5.1 5.2 3.4
##  [865] 4.8 6.1 4.2 4.8 5.3 5.8 6.6 3.4 3.7 3.4 5.0 5.1 4.9 3.5 5.2 4.4 5.1 4.0
##  [883] 4.0 4.6 5.1 2.9 4.3 3.6 4.4 4.8 5.5 4.9 5.8 5.8 4.6 4.2 5.0 5.1 5.9 2.7
##  [901] 4.8 5.6 3.2 6.0 4.9 4.7 5.7 3.5 5.9 5.6 5.7 5.5 4.7 5.0 2.6 5.0 4.0 4.1
##  [919] 4.4 3.7 4.7 3.7 4.9 4.0 3.2 4.0 3.9 3.4 4.1 4.3 4.2 4.0 4.8 5.2 4.2 4.3
##  [937] 5.1 3.6 3.4 5.1 6.1 3.9 4.9 5.6 2.8 3.8 4.2 3.2 3.5 3.3 3.8 4.3 4.7 4.2
##  [955] 4.3 4.3 5.4 4.1 5.1 5.1 4.4 6.1 4.1 3.7 4.4 2.7 2.1 4.0 4.6 2.5 4.2 4.6
##  [973] 6.0 3.7 5.3 4.6 4.5 5.4 3.1 2.6 2.9 3.8 4.6 5.3 4.6 3.7 4.9 4.7 4.7 5.3
##  [991] 4.6 4.6 3.7 5.2 5.0 3.6 3.2 3.9 3.2 4.1
## 
## $func.thetastar
## [1] -0.0214
## 
## $jack.boot.val
##  [1]  0.54709302  0.36713092  0.28230088  0.12120344 -0.01961326 -0.12716049
##  [7] -0.11899441 -0.32782609 -0.40482574 -0.55504323
## 
## $jack.boot.se
## [1] 1.00983
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
##    [1] 4.6 5.8 6.0 6.2 3.8 3.9 3.9 5.5 4.6 4.5 3.8 6.0 4.7 4.5 4.9 4.5 4.8 3.1
##   [19] 6.5 5.3 4.5 5.2 4.3 4.0 4.3 3.2 3.9 4.5 4.2 5.7 5.1 5.2 5.5 5.7 3.5 3.6
##   [37] 6.2 4.7 4.2 3.0 4.6 4.3 4.4 5.1 3.6 5.7 4.0 5.2 3.1 4.8 5.3 3.3 5.8 5.1
##   [55] 4.4 3.9 5.3 6.9 5.4 3.2 4.5 4.5 4.2 4.2 3.7 5.1 6.2 3.5 4.0 4.8 5.0 2.5
##   [73] 5.3 5.7 5.0 5.7 4.7 5.2 3.4 4.2 5.6 6.8 5.0 4.1 4.4 3.2 5.2 5.5 5.6 3.2
##   [91] 3.2 4.5 5.1 6.1 6.8 4.6 5.3 3.6 4.3 3.0 3.7 4.7 3.8 3.7 5.1 5.8 4.2 4.5
##  [109] 4.0 4.3 3.8 4.5 3.9 4.4 4.5 5.1 5.0 5.6 3.6 5.0 5.0 3.4 4.7 3.2 6.2 4.1
##  [127] 5.2 4.6 3.4 2.8 4.2 5.3 5.4 5.1 4.8 4.1 3.5 4.7 5.0 3.1 5.2 4.3 4.8 4.7
##  [145] 4.7 4.4 5.3 3.6 4.2 4.8 3.0 3.5 5.2 3.6 5.1 4.7 4.6 4.5 5.6 4.3 4.7 5.7
##  [163] 4.8 4.4 4.5 5.1 4.7 5.3 4.4 4.8 4.3 3.6 5.2 5.2 3.7 5.0 3.2 3.6 5.0 4.0
##  [181] 3.9 6.0 5.8 3.9 5.1 4.7 5.2 5.3 5.7 4.7 4.3 5.2 5.0 4.4 5.5 5.8 4.4 3.1
##  [199] 4.5 4.8 5.2 5.1 3.2 4.1 4.2 3.9 2.5 3.8 4.1 4.6 5.7 5.3 4.5 3.4 4.2 3.0
##  [217] 7.0 4.9 4.4 3.7 5.9 3.6 3.4 3.9 4.4 5.1 4.6 4.5 3.5 5.4 4.4 5.0 4.2 4.7
##  [235] 3.6 4.9 5.7 4.2 2.9 3.8 3.9 3.9 4.2 4.3 5.1 4.9 4.7 4.8 4.9 5.3 4.4 6.2
##  [253] 4.8 4.7 4.8 6.2 6.7 4.5 4.9 3.6 3.0 4.9 4.0 4.8 5.0 4.2 4.0 2.9 4.3 4.4
##  [271] 5.6 2.2 3.8 5.8 4.2 5.3 4.3 6.3 5.2 4.6 5.3 4.0 4.4 4.7 4.7 3.8 3.7 4.9
##  [289] 5.2 5.3 4.2 5.3 2.2 6.4 2.6 2.8 4.1 3.9 6.9 3.8 3.6 3.5 5.0 4.4 3.8 5.0
##  [307] 3.6 4.3 2.0 4.5 3.6 5.1 6.1 2.7 5.9 4.5 4.9 3.5 3.2 5.5 4.3 3.8 4.2 3.0
##  [325] 4.8 5.2 4.0 4.7 5.1 6.0 5.1 4.1 5.8 2.8 5.6 3.0 4.8 4.0 5.3 4.2 5.7 4.3
##  [343] 3.7 5.0 4.5 3.6 4.0 4.1 4.7 5.8 3.6 3.1 4.8 3.8 4.1 3.4 2.4 4.1 5.1 5.3
##  [361] 4.4 4.6 5.0 5.6 5.0 5.0 5.5 2.8 3.7 4.1 3.9 5.0 3.0 5.0 4.1 4.3 3.9 5.4
##  [379] 3.1 3.0 2.7 3.5 3.3 3.4 5.3 5.0 5.5 5.8 5.8 4.6 6.1 5.0 6.1 4.2 4.2 4.3
##  [397] 5.1 4.3 5.7 4.6 4.8 5.7 5.4 5.3 5.9 5.5 4.1 4.2 4.3 4.6 3.1 4.1 4.9 2.6
##  [415] 5.1 4.4 4.5 4.9 3.4 4.8 2.9 3.1 4.2 5.3 2.7 4.0 5.0 5.1 5.0 5.4 6.0 4.3
##  [433] 3.2 5.2 5.1 3.6 5.0 4.2 5.3 5.2 3.7 3.3 5.1 4.4 4.4 4.7 4.1 5.0 3.7 4.7
##  [451] 5.6 3.7 3.5 2.9 3.5 3.9 6.4 5.9 4.3 3.9 2.4 5.4 4.6 4.5 4.2 4.6 2.3 4.1
##  [469] 6.1 5.2 5.2 5.2 3.7 2.8 4.7 2.1 4.7 5.3 4.5 5.2 5.0 4.9 4.4 5.3 4.2 5.9
##  [487] 3.6 5.4 4.3 3.3 4.8 6.3 4.9 4.0 3.1 3.7 4.8 3.5 4.3 5.1 4.8 3.3 5.2 6.1
##  [505] 5.0 6.1 4.0 3.8 4.2 4.4 5.5 4.1 5.5 4.9 4.2 3.7 4.8 5.3 6.0 3.5 4.2 4.3
##  [523] 4.4 5.1 4.7 4.1 5.0 3.7 5.9 5.2 4.9 4.6 5.8 3.2 4.1 4.8 4.7 3.9 6.8 3.5
##  [541] 6.1 3.6 4.9 6.1 4.4 3.8 5.1 5.2 5.0 4.7 5.1 4.1 4.1 4.5 4.5 4.2 4.3 4.0
##  [559] 2.7 5.8 5.1 6.5 4.9 4.6 4.8 4.0 5.1 3.9 5.3 5.8 3.5 4.9 4.7 3.5 3.7 5.5
##  [577] 3.3 3.8 3.5 3.4 5.1 5.4 4.5 3.9 3.1 5.2 5.2 4.2 4.5 3.5 4.2 5.0 4.9 5.0
##  [595] 6.2 4.2 5.4 3.7 4.0 3.9 4.1 3.7 2.9 4.2 4.0 3.8 5.8 3.5 4.4 3.0 5.0 4.3
##  [613] 4.6 5.6 4.7 3.4 3.8 2.6 4.6 5.4 5.9 4.5 5.3 3.9 2.3 3.6 5.7 3.9 3.6 4.9
##  [631] 4.5 5.1 3.7 4.4 5.1 4.2 3.7 5.3 3.0 6.5 6.2 4.6 5.7 4.4 3.4 4.5 5.2 3.2
##  [649] 5.3 4.4 6.3 6.5 2.9 4.8 4.7 3.5 5.1 3.9 5.3 5.0 5.4 3.9 5.2 4.3 6.6 4.1
##  [667] 6.1 4.1 4.9 4.9 5.4 5.7 3.6 5.0 4.2 3.6 5.5 5.0 4.5 5.1 3.7 4.8 2.6 3.4
##  [685] 2.8 3.7 4.6 4.8 3.1 4.7 2.6 3.1 3.7 5.7 2.7 6.1 4.4 3.9 5.5 4.8 4.4 4.5
##  [703] 3.9 4.3 5.1 5.3 4.8 3.8 4.4 5.1 3.2 4.8 4.4 4.7 3.9 4.1 5.9 5.7 4.5 4.5
##  [721] 4.2 4.8 4.5 4.8 5.2 4.7 3.3 4.1 4.1 5.7 4.1 4.9 3.8 4.7 3.8 5.8 5.4 5.2
##  [739] 4.7 2.6 5.0 6.0 3.3 4.4 5.1 3.9 4.6 4.8 3.7 4.2 4.0 4.8 4.4 4.7 4.3 3.7
##  [757] 3.8 2.5 3.6 4.2 4.7 3.8 4.3 4.4 3.1 5.1 4.4 3.5 4.2 5.2 3.2 4.0 4.6 5.0
##  [775] 4.8 2.5 6.1 3.9 1.9 4.6 3.6 5.2 4.8 4.1 6.0 5.0 4.9 2.9 4.0 4.3 5.3 5.2
##  [793] 4.8 3.3 4.0 5.2 3.5 3.7 3.6 4.2 4.7 3.8 3.9 4.6 4.7 6.0 4.3 3.7 3.7 5.1
##  [811] 4.1 3.3 2.4 6.7 3.9 5.7 4.5 4.0 4.4 4.0 4.6 3.7 4.2 5.4 5.9 5.3 3.7 4.6
##  [829] 6.4 4.0 5.1 4.9 4.7 6.2 5.2 3.2 4.6 4.4 5.0 4.1 4.6 6.2 3.3 4.8 3.4 2.5
##  [847] 4.8 4.5 4.9 4.2 5.3 5.6 4.5 4.2 5.5 4.7 4.0 5.0 4.7 5.2 4.1 5.6 4.9 4.2
##  [865] 4.0 5.1 5.2 5.4 3.1 3.9 4.1 5.2 5.1 4.5 5.6 5.2 3.7 5.2 4.4 4.7 6.3 4.9
##  [883] 5.6 5.2 5.0 4.4 5.3 4.2 4.6 5.8 5.0 6.3 3.7 4.8 5.1 5.2 4.3 3.7 5.2 3.7
##  [901] 5.0 5.0 4.6 4.6 3.6 4.6 2.8 4.1 6.1 4.5 2.3 4.7 4.1 3.8 3.7 3.8 3.9 3.8
##  [919] 4.1 3.0 4.5 5.1 4.5 5.8 6.0 4.4 2.9 3.6 6.9 3.4 5.1 5.0 4.5 2.6 4.8 3.8
##  [937] 3.8 2.3 4.2 5.5 4.5 4.6 5.2 5.8 3.3 2.6 4.7 3.1 3.7 4.0 4.4 6.3 4.4 4.8
##  [955] 2.9 3.3 5.7 3.3 3.7 3.7 4.4 3.3 6.0 4.7 3.7 3.0 3.4 4.0 4.0 5.6 2.5 4.4
##  [973] 5.3 5.3 4.6 4.6 4.3 5.6 4.8 4.0 3.9 3.3 4.4 4.5 3.7 6.4 5.0 4.2 4.9 5.1
##  [991] 4.6 4.2 6.3 4.5 4.4 4.8 6.7 5.1 5.8 3.9
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.3 5.3 5.3 5.2 5.1 5.0 5.0 4.8 4.7 4.4
## 
## $jack.boot.se
## [1] 0.8532878
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
## [1] 0.5166083
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
##    8.050530   14.169823 
##  ( 3.528222) ( 6.407722)
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
## [1]  0.86626173  0.24929123  1.20515954  0.61615373 -0.07673115 -0.74690321
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
##    [1]  0.3492998666  0.2938687702  1.4133023623  1.0864512227  0.0136809217
##    [6]  0.3301343030  1.5067818612 -0.0528691397  0.0127957456  0.1141756290
##   [11]  0.4503766123  0.6894636366 -0.2252098596 -0.0746817122  0.3934967111
##   [16]  0.4325176762  2.0080869128  0.3230928516  0.1253461381  1.3150402015
##   [21] -0.3624224436 -0.3113176748 -0.4130972382  0.7913813312  0.0644959232
##   [26] -0.1680907228  0.7145907679  0.4252796210  1.0440093439  0.0651248985
##   [31]  1.4796754874  0.1581719604  0.7364428449  0.6326144238  0.5988035926
##   [36]  1.4195387060 -0.2439529816  0.5908473129  1.5036894225  1.2014425512
##   [41] -0.6909118163  1.2417687545  0.5574696767  0.4101853171  0.7702172632
##   [46]  0.1521190635  0.2408938416  0.3700617718  0.3563250576 -0.0215639721
##   [51] -0.2472947824  0.8056628512  0.3624413311 -0.1014865439  0.9763816437
##   [56]  0.5023291169  1.2426081514  0.7281081496  0.5331471074  0.7639878171
##   [61] -0.0125522376  0.5370315269  0.5359359820 -0.0228584684  1.3240171759
##   [66]  0.3689277458 -0.0038504929  1.4277031086 -0.1732007734  0.3205467305
##   [71]  0.5104861194  1.0680699099  0.2071788358  0.1535079669  2.1982988965
##   [76]  2.2885536621  0.8923295165  0.9724064325  0.8363184465  1.1001233219
##   [81]  0.4991610637  0.6173608640  0.4187650296  0.8462627638  0.3218584332
##   [86]  0.6300289522  0.5857991038  0.3785285514  2.3380172550  0.5437072219
##   [91]  0.2880863729  0.5237477799  0.4158115722  0.2429218765  0.7087435276
##   [96] -0.3366350794  0.6754000809  0.1907292041 -1.1408401610  0.0183458643
##  [101] -0.0818387340 -0.0542019292  0.1175979639  2.1038714719  1.2531821033
##  [106] -0.2005809987  0.4710791007  0.7912036210  0.1178439839  0.1269485080
##  [111]  0.3743535461  1.6485226456  0.7835075352  0.4946287555  0.4472496165
##  [116]  0.5945705144 -0.0622239413  0.3126309066  1.3523138306  0.8715633007
##  [121] -0.0282895734  0.5308852340  0.3199949923 -0.0258409369  1.5659801732
##  [126]  0.4522179524  0.7942195041  0.7932078340  1.1708038437  0.1425841672
##  [131]  0.0967559338  1.0319964100  0.1940671398  1.3940375286  0.2115982289
##  [136]  0.8481033587  0.1598034050  0.7210802155  0.6985917252  0.4730866900
##  [141]  0.8807487883  0.1680400524  0.8924318160  1.1795898208  1.2205104419
##  [146]  0.9556328771 -0.5564935676  2.2420738329  0.0730980338  0.2965407840
##  [151]  0.4777365807 -0.1670664194 -0.3857833957  1.3003906826  0.3758664468
##  [156]  0.0317725123  1.5880296454  1.3569368300 -0.2024349696  2.3161908606
##  [161]  1.3354155190  1.2750377909  1.3102573152  1.3222994883 -0.5383601556
##  [166]  0.7212165666 -0.2246962854  0.1844563501  1.3071393148  0.7272768352
##  [171]  0.0255452728  0.9792761392  1.0328025269  0.3348092777  0.9680692364
##  [176]  0.0433039380  0.4877878114  0.9265430228  0.8177718909  0.1268413650
##  [181]  0.2947710376  0.9839659650  1.3553075831  0.1587171879  0.9120970465
##  [186]  0.5442286075  0.0077240545  0.3887014387  0.1864165358  1.2095228365
##  [191]  0.9504518003  0.1341939223  0.0398255917  0.7203503687 -0.0145208766
##  [196] -0.3713631339  0.4861812070  0.4777365807  0.9284687876 -0.1896678358
##  [201]  0.0394712971 -0.5770374957  0.4079086737  0.9742988412  1.0589940253
##  [206]  1.3024771911  1.3328047090  1.3037894338  0.4102692195  0.5707319478
##  [211]  0.8555322138  0.6487557736  0.9038999864  0.3688729816 -0.0387239398
##  [216]  0.0413798031  0.7795081067 -0.1620005150  0.9200265589  0.2826177115
##  [221]  1.3350992575  0.5450914330  1.3478611529  1.1820893432  0.9481247966
##  [226]  0.4097241080  0.5309298957  0.1193931021  0.1375850081  0.3440166371
##  [231] -0.9635473913 -0.7631605892  0.4876593858  1.7226365332  1.5533937266
##  [236] -0.6466820169  0.9414876048 -0.1384270324 -0.6315917130  0.7544476549
##  [241] -1.0299702152  0.9232984062 -0.7785747251  0.4950808255  0.3758078030
##  [246]  0.2751179714  0.6269469287  0.8123766323  0.6637291865 -0.0169516543
##  [251]  0.8801571529  1.7053512626  0.5690011672  0.8940528884  0.4900184110
##  [256]  0.5915998442  0.6256179388  0.3762757121  0.1520735382 -0.3566394797
##  [261]  1.5004130822 -0.2090063672  0.6784074185  1.7616777656  0.5269683274
##  [266]  0.6707301622 -0.2586425348 -0.3824219332 -0.4887293977  0.2722252483
##  [271]  1.4416788212  0.3863085674  2.3567588023  0.3362046724 -0.1790462538
##  [276]  0.8404211047  0.2302636939  0.2775130263  2.1271334910 -0.4766188290
##  [281] -0.7965109339  0.5541281620  1.5241886244 -0.5757794336  0.9383504782
##  [286]  0.4564312161  0.2005026669  0.1424848570  0.0404260839  0.5563060095
##  [291]  0.5836526194 -0.0499027737  0.6756862841 -0.1810026975  0.6034863025
##  [296]  0.4657601335  0.9532800333  0.0745740469 -1.1040774318  0.0172184553
##  [301]  0.0827447894  0.8736824464  0.1506414759  1.5080719265  0.1535079669
##  [306]  0.3104467674  1.0357508714  0.5469810833  1.0279330303 -0.2317108652
##  [311]  0.8361044310  0.5331471074  0.6442898776 -0.9627267299  0.1100626294
##  [316]  0.1439160996  0.0449238589  0.0264699515  0.6943000848  0.4911676609
##  [321]  0.9307942198  1.1275707477  0.8441811828  1.0332423749 -0.0002130969
##  [326]  0.6454212983  1.4308539179  0.2549846019  0.2887073619 -0.3626663797
##  [331] -0.0803881923  0.4039066293  0.5994504896 -0.3558624706  0.6239577058
##  [336]  0.3212229291  0.0254104554 -0.2324435150  0.8130430772  1.3373218386
##  [341]  0.0228645895  0.5105046281  0.5457144328  0.0234952009  2.0438642805
##  [346]  0.6266874063  1.2441449981  0.7199923504  0.4020991156  0.9035790383
##  [351]  1.3298191326 -0.0102509186  1.0701888746  0.5596114701 -1.3186462190
##  [356]  0.4944592535  0.6926536738 -0.4260525271  0.2095867686  0.6955110324
##  [361]  0.8249040104  1.0098785287 -0.0100085335  0.7819413649  0.8178276932
##  [366]  0.9465154079  0.3293342500  1.3804264186  0.2374656982  0.2270564086
##  [371]  0.5360535244  1.5360019954  0.2257087647  0.9623849546 -0.3158998193
##  [376]  0.3605673166  0.7491590771  0.3320844384  0.0470658521  0.8055281642
##  [381]  0.9520929630  0.5469810833  0.3288672507 -0.3168468361  0.4916953736
##  [386]  0.0880439977  0.8047926379  1.2285731776  1.0014038633 -0.3536633933
##  [391]  1.2950132781  0.8500445925 -0.2512338260  2.0158209401  0.3700122897
##  [396]  0.6896731436  0.0829005535 -0.2021401275 -0.3352413706  1.2834775049
##  [401] -0.6010133202  0.9400686673  0.9574112110  0.7263025913  1.1047871647
##  [406]  1.5964150143 -0.2677977791  0.5439442628  0.4028508444  0.9371989063
##  [411]  0.8121834083  2.1626016964  0.2190070107  0.7316501599  0.3852378596
##  [416]  0.9259353827  0.9091168507 -0.4849551527  0.4777365807  0.4505594248
##  [421]  1.3844233081  0.1791033430 -0.9337872842  0.8825470967  0.2855099731
##  [426]  0.6380707746  0.7175483720  1.2708665634 -0.0764604868  0.5874029861
##  [431]  0.1050940151  0.4828885952  0.4900393888  0.2649308361  0.4631895344
##  [436] -0.4826429795  0.8950618725 -0.1976678578 -0.0285985579  0.4887778295
##  [441]  0.1663847169 -0.4001940402  0.6354953344  0.1114264865  0.3681598724
##  [446]  1.6625037669  1.5356917011  1.0674279646 -0.1052486747  0.0950344429
##  [451]  1.1316254751  1.4809452617  0.4514860118 -0.0510439049  0.1139181349
##  [456]  0.7937054530  0.5534415021  0.5583409623  2.0515133808 -0.1926208381
##  [461]  0.0560365483  1.0475155176  0.7238187996 -0.2769346921  0.1233641182
##  [466]  0.8786792369  0.1727339134  1.9111569255  1.5282343184  0.1645271076
##  [471]  0.3119989537  0.2115782951 -0.1525264083  1.1889756570  0.7303090850
##  [476]  1.2974188449 -0.5056146361  0.4805076248  0.2848699590  0.4135679187
##  [481]  0.3453039703  0.8146275484 -0.8093276184 -1.4930928241  0.9676591162
##  [486]  1.4409154173  0.3213825286  1.3434383849  0.1052945971  0.2752583414
##  [491]  0.0533822393  0.4028508444  0.1434252743  0.0747565751  0.9239925050
##  [496] -0.1615366607  0.7805772803 -0.3045064648  0.6747319250  0.8351092910
##  [501]  0.1577332393  1.0357508714 -0.2464427712  0.8201918692  0.4183549439
##  [506] -0.2868388050  0.5656307360  0.4883429147  0.6410410764  0.7700592036
##  [511] -0.1326575213  0.6140763121  0.1180374570  1.4836102595 -0.1620156247
##  [516]  2.5781518255  0.3041529630  1.3286498844  0.0679119243  0.8013721258
##  [521]  1.2661723981  0.0625344121  1.7695223171  0.3572147146  0.7926328650
##  [526]  0.9467061869  0.1617915259  0.0234204029  1.2653178263  0.1333813947
##  [531]  0.1190967834  0.0249663145  0.4400245290  0.8281310903  1.4123710742
##  [536]  0.1336757723  0.5271136024  0.4620632362  0.8031842424 -0.0795266835
##  [541]  0.7552826034  0.0614529308 -0.2254414179  0.5136197041  1.5469765839
##  [546]  0.1943307022  0.9930412282 -0.2823205560  0.1962507549  1.3276278145
##  [551] -0.4510433193  1.4864462619  0.6771865348  0.0809995190  1.4303828237
##  [556]  0.4188952673 -0.2833674480  0.9780142935  0.3342713641  0.1207775984
##  [561]  0.2573064329  0.3632880269  1.3414973776 -0.3366642193  0.3104191040
##  [566]  0.1129951656 -0.3872806125  0.7560748579 -0.0782070410 -0.1819937681
##  [571] -0.1834993870 -0.2465187001  0.6640270346  0.6999193431  0.2305745499
##  [576]  0.8504186301  0.9744820329  1.0187680807  2.1164260946 -0.3984635054
##  [581]  0.1634094255  0.0390337508  0.2595382962  0.5832308257 -0.4565746601
##  [586] -0.2542288861  0.4926245700  0.4930643279  1.1116069765  0.5426134566
##  [591] -0.0004868300  1.5714876723  0.1450359421  1.3670632161 -0.7206453412
##  [596]  1.1940236825  1.9379330865  0.6489844918  1.0928935671  0.3456489173
##  [601]  0.2510700326  0.8101180547  0.9251561817  0.1438502360  1.2302729481
##  [606]  0.4654441990  1.3746003877  0.5918990517  0.8586575827 -0.6005227134
##  [611] -0.3862113649  0.8278145632  0.7931812905  0.0096008453  1.2969910038
##  [616] -0.4048779012  0.6100499396  0.3679359878  0.2389827747  1.4460564479
##  [621]  0.9187153068  0.8014004797  0.2229701876  0.0967678468  0.1989797120
##  [626]  0.9592019643  0.4272157545  0.0019308367  0.5137737528  0.3939226944
##  [631]  0.3964646536  0.6901108067  0.7796203684 -0.0451823272  0.9471318577
##  [636]  0.8801048607 -0.0933655640 -0.0209596373  0.1183029452 -0.3090360138
##  [641]  0.2308858111  0.5405202802  0.6157411851  0.1447132088 -0.0418241539
##  [646]  1.3105720134  0.3574043462  0.9698581941  1.0040259244 -0.3054643288
##  [651]  0.2876907777  0.4082304354  0.6912063215  0.9279984830  0.7412554585
##  [656]  0.6832941272  0.5473598539 -0.1347195342  0.4272980854  0.6110796230
##  [661]  1.2382861868  0.5583999436 -0.0054090306  0.8247399873  0.3120361659
##  [666] -0.3410761995  0.5824806094 -0.2331585696  0.5649161472  0.8411972189
##  [671]  0.1819235836  0.1510363828  0.1610621927  0.5457982903  0.5001744269
##  [676]  1.5403929969 -0.2462373449  0.1717173527  0.0191580814 -0.4165470086
##  [681]  0.9623849546  0.3547990365 -0.0472599253  0.4342238122  0.9086231630
##  [686]  0.3415930729  1.6091384389 -0.0060985738 -0.1107740274  1.2361366849
##  [691] -0.0191920215  1.2497818847  0.8147867590  0.6635456151 -0.1045722387
##  [696]  0.4838983749  0.9710737414  0.1040259301  1.9110323305 -0.1949557143
##  [701] -0.6410342205  1.1223143508  0.3349720255  0.2440896571  0.8645744654
##  [706]  1.3998481453  0.1052945971  0.0800091841  0.4236916198 -0.0405652870
##  [711]  1.0163079939  1.4012013054 -0.7999942998  1.4665980454  0.8549151357
##  [716]  1.0183664586  2.2109337281  0.7429626847  0.0553591491  0.7366131105
##  [721]  0.5054208820  0.7383794137  0.0593963829  2.1568205160  0.8432083936
##  [726]  0.5545708233 -0.1958150852  2.1677185204  0.7800280199  0.3089350437
##  [731]  1.4191142909  0.3679836626  0.1981979671 -0.3664498287  0.8604999368
##  [736]  0.6605641871  1.2834775049  1.0972934273  0.6556464198  0.6056972374
##  [741]  0.4796311025  0.3959952320 -0.1014865439  0.3462550820 -0.0181211192
##  [746]  1.6338175849  0.4663032638  0.0337097843  0.6533146347  0.0668510053
##  [751]  0.6057545358  0.7241289943  0.6076412226  0.0515702257  0.0544544048
##  [756]  1.4153420839  0.2782791316  0.3576960855  0.4267020595  0.5304844712
##  [761]  0.5668916241  0.7511000120 -0.0795384409  0.6692044022  0.5989807802
##  [766] -0.0808189205  0.2599989387  1.5612047439  0.8406490733  0.3798322436
##  [771] -0.4117810505  0.2382289842  0.0396186593  0.3938329295  0.3265241729
##  [776]  1.1550474548 -0.0168649304  2.0321000927  0.1278880250  1.6421871105
##  [781]  1.2568253622  1.9516374461  1.0654068328  0.7937054530  0.6333966495
##  [786]  0.7111706851  1.3503473561  1.5982124991  0.7564140260  0.4114978377
##  [791]  0.7735828013  0.8266560169  1.3500402094  1.1708038437  0.1095301431
##  [796]  0.4437469921  0.1506985535  1.2290007689  0.5657200194 -0.1782738183
##  [801] -0.1746317823 -0.3304868966 -0.0163834507 -0.1295883914  0.0114376406
##  [806]  0.1105471190  0.1197154860  0.8966111647  0.8011023898  1.3769150349
##  [811]  0.8473862339  0.1726667052 -0.0117456035  0.4362058122  0.3991019812
##  [816]  0.7683506033  0.4970065864  0.2550366866  0.8941030105  0.3685934687
##  [821] -0.6540510624  0.6355575932 -0.6930303402  0.4771838167  0.7825913430
##  [826]  0.4057193144  0.5298512016  0.3190745097  1.0422686770 -0.2090549517
##  [831]  0.4091903882 -0.2394616054  1.5121824507  1.0048062971  0.1134058179
##  [836]  0.1510710580  0.8281530595  1.5126159781  0.7158497484  0.0448731308
##  [841]  1.5622308342  1.5102960570  0.1509602005  0.5217566097 -0.3149564862
##  [846]  1.2962469077  1.0628639007  0.0326241407  0.4197729372 -0.0130146252
##  [851]  1.5962248544  1.5048441898  0.1141363641  0.0841608162  1.5428729127
##  [856]  0.5246446283  0.6919342953 -0.3849266458  0.1453346642  0.9064657547
##  [861]  0.8141351814 -0.5574315746  0.4289071421  0.9245441268  0.3863367282
##  [866]  0.8509262107  0.0885387350  0.6840960402  0.8393274675  0.3839647675
##  [871]  1.7924364454  2.3396382987  1.2830577464  0.7802409482  1.4035775298
##  [876]  1.2521418907  0.4484843060 -0.2421183114  0.2845506140  0.9862311273
##  [881]  0.5125403951 -0.5213043718  0.7698449156  0.8849549075  0.6750119005
##  [886] -0.0897636450  0.2829259168  0.8753858876  0.8236151831  0.3997150992
##  [891]  1.3181983334  0.0885387350  0.6893124720  0.8314523192  0.6885553060
##  [896]  0.7797819096  1.4322666541  0.3652762666  0.2678543150  1.3608367774
##  [901]  1.0112355123 -0.2079381107  0.5045384498  0.9546859344  0.1225113039
##  [906]  0.3874765288  0.3377529839 -0.0938596233 -0.7783306633  2.2163319935
##  [911]  0.7615266905  0.1069990029  2.1214778469  1.5778519100  0.5638830363
##  [916]  1.9041121081 -0.1906935015  0.2997058467  1.5615039428  0.9847560356
##  [921]  0.8368053256  0.7994003375  1.0848452051  0.7778227961  0.8413654124
##  [926]  0.8647928216  0.8381152065  0.5006990258  0.1276332697  0.9428041135
##  [931]  0.7139355498  0.1093993039  0.5274758929  0.9704446213  0.0400155826
##  [936]  0.7313303548 -0.6737438524  2.2069657652  0.4956331167  0.5586939966
##  [941] -0.3383854521  0.5073642942  0.9038999864  0.3560207235  0.9109325102
##  [946] -0.3317124174  0.4743789911 -0.1143681292  1.4687619271 -0.0064182473
##  [951]  0.6075224110  1.6090333027  1.0372255010  1.2917246819  0.0114109852
##  [956]  0.9436649197  2.0052616135 -0.4774300388 -0.0577338304  0.7607075636
##  [961]  0.0092191822  1.4200127729  0.5264420728 -0.3596989968  0.6901108067
##  [966] -1.2359148877  0.6983166988  0.1532112468  0.5254776371  0.6283567752
##  [971]  0.7051119772 -0.4825243890  0.3790492840  1.4244187093  0.3232239598
##  [976]  0.8674933669  0.2947501686 -0.4557443281  0.7380114107  0.7437367300
##  [981]  1.0603098772  0.5135126326  0.3147025851  0.3459739183  0.1274124234
##  [986]  1.2237429679  0.9125321814 -0.0377306403  0.5387559560 -0.1317493720
##  [991]  0.5537494043  1.0252645996  0.8023581618  2.3362343307  0.8877811347
##  [996]  0.7472088778  0.8915088349  0.9303969699  0.8638945949  0.6592641373
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
```

```r
fit2
```

```
##       mean          sd    
##   0.56814173   0.20585985 
##  (0.06509860) (0.04602734)
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
## [1] -1.26016793 -1.23624389  0.25553764  0.05743872 -0.30798479 -0.32740335
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
## [1] 2e-04
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8709182
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
## t1*      4.5 0.03603604   0.8941486
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 3 6 7 8 
## 1 2 2 2 3
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
## [1] -0.0528
```

```r
se.boot
```

```
## [1] 0.9166638
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

