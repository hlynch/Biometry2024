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
## 0 1 3 5 8 9 
## 1 1 4 2 1 1
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
## [1] -0.0291
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
## [1] 2.778921
```

```r
UL.boot
```

```
## [1] 6.162879
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.1000
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
##    [1] 4.2 2.4 5.1 3.9 5.3 4.1 4.8 3.2 4.8 4.8 4.8 3.0 4.6 4.3 4.5 4.0 3.2 3.3
##   [19] 5.1 4.8 3.7 3.7 4.2 3.8 4.5 4.2 2.9 6.5 3.9 4.2 5.8 5.6 5.0 5.2 4.8 3.5
##   [37] 4.2 3.8 5.5 6.2 5.2 2.7 4.7 5.0 3.8 4.2 5.2 5.1 4.6 3.8 4.8 5.5 6.3 6.0
##   [55] 5.8 3.2 4.0 4.9 4.3 3.6 5.7 4.9 4.8 4.3 5.4 3.3 3.8 7.1 4.5 4.7 6.0 5.6
##   [73] 4.7 4.3 3.1 5.3 3.9 3.2 4.1 3.9 3.6 5.5 4.6 3.7 3.9 4.4 5.3 5.2 2.9 5.0
##   [91] 4.9 6.6 2.4 3.8 3.8 4.4 6.3 4.5 4.5 5.5 4.1 4.4 5.2 4.9 5.4 3.9 5.0 4.9
##  [109] 3.7 6.2 2.7 4.5 4.8 3.6 5.0 4.7 3.8 5.3 3.3 4.9 4.2 4.5 3.0 2.3 4.7 5.4
##  [127] 4.1 5.4 4.7 4.4 4.2 3.3 4.0 5.2 3.4 4.9 3.3 4.9 6.5 3.6 3.9 3.4 4.6 2.5
##  [145] 4.4 2.9 4.9 4.2 3.9 3.9 4.0 4.7 4.9 4.7 1.7 5.0 4.5 5.0 4.4 6.1 4.4 5.6
##  [163] 4.1 3.4 5.4 3.8 4.0 3.3 4.4 4.7 4.4 4.3 5.2 4.1 4.5 3.8 6.6 4.5 4.9 5.7
##  [181] 4.6 5.9 5.3 4.4 3.2 4.0 5.4 4.9 5.8 6.4 4.5 4.9 3.0 3.0 4.7 5.3 5.1 3.5
##  [199] 4.0 5.4 3.0 3.6 5.0 4.5 4.5 3.7 4.1 5.1 3.9 3.9 3.1 5.1 6.0 5.0 5.5 4.7
##  [217] 3.9 3.2 5.2 3.3 4.3 4.0 4.5 4.6 3.9 3.7 4.8 3.8 3.9 4.6 3.6 5.4 5.0 5.0
##  [235] 4.4 5.2 3.3 3.8 4.0 4.1 3.5 5.4 3.7 5.8 4.0 4.5 3.8 5.5 2.3 4.5 5.7 4.9
##  [253] 4.2 4.3 4.8 4.2 4.1 4.6 4.8 4.0 5.1 4.4 4.6 4.6 5.0 4.4 4.2 5.9 3.1 4.1
##  [271] 3.9 3.5 6.3 3.8 3.9 5.1 4.5 3.4 4.1 4.0 6.0 6.2 3.3 5.4 4.9 3.8 4.5 6.5
##  [289] 3.6 4.9 2.6 4.2 4.3 5.3 3.9 5.4 4.0 4.9 4.5 4.2 4.2 4.0 4.0 4.0 3.4 6.2
##  [307] 5.9 4.8 2.9 5.9 4.2 4.3 5.5 2.7 5.7 5.4 5.3 5.8 6.6 2.0 3.1 4.1 3.6 5.7
##  [325] 3.6 4.3 3.4 4.1 2.4 3.6 3.6 4.1 4.6 2.7 5.5 3.3 5.8 5.1 5.9 5.5 4.6 3.3
##  [343] 4.5 3.6 5.9 4.0 4.3 2.4 4.7 4.5 3.3 3.5 4.8 4.7 3.1 4.5 3.5 5.4 2.6 3.9
##  [361] 4.0 4.8 4.9 4.4 5.6 5.2 3.3 3.6 3.3 3.6 3.8 3.5 3.4 5.8 6.4 6.0 4.2 4.9
##  [379] 5.0 5.6 4.9 3.9 4.0 5.1 3.6 3.7 3.4 3.8 5.1 5.8 3.8 5.4 5.4 5.7 4.7 5.5
##  [397] 5.2 5.3 5.1 3.8 4.5 4.6 4.3 5.8 4.7 4.8 4.5 4.9 4.2 4.0 5.2 5.8 4.3 4.2
##  [415] 5.2 2.8 4.2 4.0 4.7 2.6 4.3 4.3 3.3 3.0 3.3 6.6 5.3 4.1 3.7 4.3 5.3 3.5
##  [433] 4.1 4.9 5.0 5.3 4.2 4.8 4.5 4.2 3.7 5.2 5.4 4.6 4.4 4.9 5.3 5.1 4.1 4.4
##  [451] 4.6 4.7 4.7 3.5 4.3 3.6 4.2 4.2 3.3 4.9 4.4 3.1 5.1 4.0 2.8 4.3 6.2 4.5
##  [469] 5.2 5.0 4.9 3.4 4.2 4.3 4.1 3.1 4.4 4.6 3.9 4.4 5.0 5.0 4.3 4.4 6.4 4.5
##  [487] 4.6 5.1 4.5 5.0 4.3 5.4 5.4 4.8 5.8 5.6 4.1 3.2 4.9 5.3 4.2 6.2 3.9 4.0
##  [505] 4.7 3.5 4.3 5.8 2.9 3.6 5.3 5.7 4.5 3.5 4.1 5.0 4.8 3.3 5.0 5.5 4.1 4.4
##  [523] 5.2 5.6 2.9 4.8 3.3 4.9 4.6 5.7 5.6 4.9 2.9 6.2 7.0 4.5 4.5 4.6 4.4 5.0
##  [541] 5.4 3.3 4.8 3.0 5.1 3.6 4.1 5.0 5.0 3.5 2.4 3.7 6.7 4.9 3.8 3.5 3.1 4.9
##  [559] 4.8 5.9 2.8 5.2 3.5 5.0 5.4 4.2 4.6 5.3 3.5 5.0 2.7 3.8 4.5 4.1 4.2 3.2
##  [577] 4.1 6.4 6.1 4.3 3.9 5.9 2.8 5.3 5.2 5.0 3.7 3.5 5.0 5.3 5.1 3.6 4.9 4.8
##  [595] 5.5 3.7 5.4 5.5 3.8 3.2 5.1 4.7 5.2 4.3 4.8 4.7 5.1 3.4 6.0 4.4 2.5 2.4
##  [613] 3.2 4.7 5.1 4.8 3.8 4.8 3.4 3.8 5.7 3.6 3.7 6.4 4.6 3.1 4.8 2.8 4.7 4.0
##  [631] 4.2 4.7 4.0 4.2 2.8 3.1 4.7 5.2 4.2 4.1 3.8 4.9 5.1 5.0 2.8 6.7 3.4 4.0
##  [649] 4.2 2.4 3.8 4.4 4.9 3.8 5.3 4.8 4.3 5.7 5.6 3.9 5.3 5.4 5.2 4.5 5.5 4.2
##  [667] 4.5 5.6 4.6 3.9 4.8 3.8 5.4 5.2 5.5 4.2 4.3 6.5 5.0 3.9 4.7 3.8 5.6 4.5
##  [685] 4.2 4.2 3.9 3.2 4.5 3.6 3.5 4.5 5.5 5.2 3.6 5.0 5.2 3.8 3.5 5.8 5.9 3.5
##  [703] 2.7 4.8 4.7 3.9 5.3 4.9 4.8 3.7 2.6 5.2 5.8 3.4 4.4 5.3 4.2 3.5 4.2 5.8
##  [721] 3.9 4.5 4.2 6.2 3.2 5.1 4.7 3.5 3.8 4.5 3.7 4.4 4.0 3.0 3.8 3.1 4.4 3.6
##  [739] 3.8 4.1 4.6 4.4 3.6 2.9 3.6 4.7 4.3 3.3 4.9 5.1 4.6 3.7 3.2 5.0 4.9 2.3
##  [757] 5.4 4.1 4.5 4.7 5.0 7.1 4.5 4.6 3.6 6.1 4.0 4.5 5.6 4.0 5.4 5.1 4.0 5.3
##  [775] 3.3 4.0 4.5 4.9 5.4 3.7 4.3 4.8 3.0 5.2 4.0 3.9 5.0 6.1 4.0 3.0 4.9 4.5
##  [793] 3.3 4.6 4.2 4.5 4.0 3.4 2.6 4.4 5.0 3.8 3.7 6.6 3.9 3.4 4.9 4.1 3.7 4.0
##  [811] 3.2 3.6 3.9 5.0 5.1 6.0 6.0 2.8 3.7 4.1 4.2 4.5 4.9 4.0 5.3 4.9 5.2 3.6
##  [829] 3.8 4.0 5.2 3.6 3.9 3.1 3.4 3.4 4.4 4.7 3.8 4.1 5.4 4.8 5.0 3.9 4.3 4.5
##  [847] 6.3 2.6 3.7 4.8 5.1 3.6 4.9 2.7 4.0 5.7 3.8 4.5 4.9 4.6 4.3 6.5 3.8 3.5
##  [865] 4.9 3.4 4.9 5.7 4.1 5.3 4.2 4.2 4.9 4.6 4.0 4.2 3.7 4.2 5.0 4.9 4.3 4.2
##  [883] 4.6 4.4 5.4 6.3 2.2 3.5 4.6 4.0 5.2 4.1 4.9 3.8 5.0 5.5 4.2 4.1 4.0 5.2
##  [901] 4.9 4.8 4.5 3.4 4.5 5.7 3.3 4.5 3.8 4.9 4.7 5.1 3.9 4.9 5.5 6.0 5.0 6.1
##  [919] 2.9 5.4 4.1 3.6 5.8 5.9 6.0 5.1 5.0 5.1 4.6 6.5 5.3 3.1 5.1 5.3 5.1 2.7
##  [937] 2.0 4.8 4.0 3.4 5.3 4.2 6.1 5.2 3.5 5.0 6.1 3.8 4.5 4.3 4.5 3.8 4.6 4.1
##  [955] 4.0 4.0 3.9 4.0 5.2 5.0 5.4 5.4 5.1 5.0 4.0 4.6 3.8 4.1 4.7 3.9 5.7 4.7
##  [973] 3.2 4.4 5.0 3.7 4.6 5.4 6.2 5.6 4.6 6.2 4.9 4.4 3.0 4.3 5.4 3.5 5.9 6.1
##  [991] 3.7 4.3 5.2 4.5 4.1 4.7 3.0 4.8 3.7 5.8
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
##    [1] 4.2 4.5 5.0 5.2 3.9 5.0 4.6 5.4 4.3 4.7 3.4 4.5 3.7 3.9 5.0 6.0 4.8 3.9
##   [19] 4.9 3.9 4.1 5.6 3.2 4.7 5.0 6.0 2.9 5.9 5.5 3.8 7.0 5.6 4.3 4.0 4.1 6.2
##   [37] 4.9 4.7 3.5 3.2 5.4 3.9 1.8 3.7 4.6 3.4 3.3 3.8 4.5 4.9 4.0 3.7 2.4 4.3
##   [55] 4.8 5.4 3.8 3.5 5.5 5.3 4.5 4.0 2.2 4.6 3.8 5.1 3.6 4.4 5.7 4.4 5.6 4.6
##   [73] 4.5 3.7 4.1 3.4 5.4 5.9 4.8 5.2 4.5 4.7 4.8 5.6 5.6 3.8 6.0 5.1 5.0 4.9
##   [91] 4.3 5.1 5.3 3.8 5.4 5.3 4.5 4.3 4.9 5.9 3.1 6.0 5.2 5.2 4.0 3.5 6.3 4.0
##  [109] 3.8 5.3 4.2 5.4 2.6 3.1 4.3 5.1 3.6 4.1 2.9 4.9 3.6 4.7 4.0 4.3 5.0 5.0
##  [127] 4.7 4.3 6.3 2.7 2.5 3.2 3.0 4.7 6.0 3.5 4.4 5.1 5.1 5.3 4.2 3.6 4.7 5.2
##  [145] 3.6 5.0 3.2 5.9 6.1 5.0 5.3 4.6 5.8 3.8 3.4 3.3 5.3 5.0 3.4 4.3 5.2 2.8
##  [163] 4.7 4.2 4.8 6.1 5.1 4.1 5.3 4.5 4.9 3.3 3.3 4.3 5.4 4.8 4.8 4.1 3.3 5.3
##  [181] 7.3 6.2 4.6 4.2 5.3 4.8 5.2 4.7 3.9 4.0 5.0 7.1 3.6 2.5 4.6 4.8 5.1 5.9
##  [199] 4.7 4.8 4.4 4.8 5.1 4.9 5.3 3.9 3.6 4.9 3.5 5.3 4.8 4.1 4.5 5.3 5.7 5.3
##  [217] 5.6 4.1 4.3 4.6 4.6 3.3 3.6 4.0 2.8 4.3 2.8 5.0 5.0 4.4 5.2 4.6 4.9 3.4
##  [235] 3.3 5.7 4.5 2.5 5.6 5.4 5.6 3.6 4.7 3.0 4.8 4.3 5.8 5.0 4.8 4.3 4.9 3.5
##  [253] 5.9 5.6 4.5 4.0 2.6 4.7 4.1 4.8 4.1 4.2 3.4 3.9 3.5 3.6 4.6 3.9 3.4 5.2
##  [271] 4.3 3.5 5.3 2.6 5.2 4.9 3.5 5.4 4.7 5.2 3.5 4.9 4.8 3.8 2.7 4.9 3.4 3.4
##  [289] 5.5 3.2 4.2 4.4 5.8 3.5 5.1 2.8 4.6 5.8 4.9 3.9 5.0 5.4 6.2 5.1 3.9 3.9
##  [307] 5.4 6.4 4.9 3.4 4.9 5.4 5.5 4.6 4.5 5.2 3.9 4.6 5.0 4.3 4.8 3.5 3.3 4.7
##  [325] 2.6 5.2 4.8 4.9 5.2 5.1 4.0 3.4 4.1 4.1 5.5 3.2 3.3 4.2 4.3 3.6 4.6 3.0
##  [343] 4.2 4.7 4.1 4.6 5.5 5.8 5.4 4.7 5.1 5.7 6.1 6.7 5.5 3.4 4.9 4.9 4.5 4.9
##  [361] 4.1 4.5 3.3 3.0 4.9 5.0 3.2 4.0 3.4 4.0 4.6 5.2 4.8 4.5 5.0 5.4 3.2 4.0
##  [379] 5.5 4.2 4.9 4.2 5.5 6.4 4.9 4.7 5.9 3.8 4.6 5.4 3.4 4.0 4.5 5.5 4.3 4.5
##  [397] 4.4 4.6 3.7 5.3 5.8 4.4 3.2 5.2 6.6 5.8 4.5 5.9 3.0 4.9 3.7 4.4 5.2 3.7
##  [415] 2.5 5.8 3.9 5.0 2.2 5.5 4.3 5.9 3.9 4.9 4.0 3.4 3.6 4.8 5.1 3.3 4.5 4.4
##  [433] 3.9 4.5 5.2 4.6 4.1 3.4 5.2 3.8 4.2 4.2 4.2 5.3 4.7 5.8 4.3 4.2 5.4 6.6
##  [451] 4.4 3.7 4.3 6.2 3.5 3.7 4.8 4.2 5.9 5.4 2.8 5.6 4.2 4.2 4.3 4.9 5.9 4.6
##  [469] 2.9 4.7 4.5 3.4 4.5 4.0 6.0 3.5 3.9 5.6 3.4 4.9 5.3 3.8 5.6 4.9 6.4 4.5
##  [487] 5.2 4.8 4.7 5.3 3.3 4.8 3.4 4.0 4.4 4.6 5.4 3.8 3.5 5.4 4.2 4.5 5.8 4.9
##  [505] 5.8 5.5 3.9 5.6 5.3 4.6 2.9 5.3 4.7 6.5 2.6 5.4 5.3 3.2 3.6 4.6 4.0 3.0
##  [523] 4.1 4.3 6.3 4.7 2.1 5.1 4.4 4.7 3.5 4.9 4.9 5.0 4.1 4.3 4.6 4.7 4.9 4.4
##  [541] 6.0 3.6 5.0 4.0 5.5 4.8 3.4 2.5 4.6 5.7 4.3 4.7 3.6 5.4 5.1 5.6 4.7 4.9
##  [559] 5.7 3.8 6.2 4.9 4.0 3.4 3.9 6.0 5.3 4.3 5.8 5.8 4.9 4.1 4.2 4.6 2.8 5.6
##  [577] 5.3 4.0 4.7 5.3 3.6 2.8 4.6 4.4 5.3 6.4 2.8 4.8 5.7 5.2 4.5 4.3 3.8 5.8
##  [595] 4.7 4.9 5.6 5.5 2.7 5.8 4.1 5.8 4.5 4.8 5.1 3.5 3.3 3.5 4.0 5.1 4.6 5.1
##  [613] 6.3 3.8 3.3 3.9 4.3 5.0 4.0 4.4 3.8 3.4 4.5 4.5 2.3 5.0 5.1 4.6 4.5 2.8
##  [631] 3.8 4.5 5.1 5.9 4.2 4.8 3.6 3.9 3.6 5.9 3.2 4.8 5.2 5.2 4.8 2.6 3.5 4.2
##  [649] 4.5 5.1 4.3 4.4 4.7 4.6 5.6 5.7 4.8 4.0 3.9 4.5 4.7 4.6 4.0 4.2 5.1 5.7
##  [667] 3.5 4.6 3.8 4.3 5.7 4.7 2.9 2.9 5.6 4.4 6.3 3.9 4.1 5.8 4.8 4.0 3.8 5.4
##  [685] 4.4 2.7 5.3 5.5 4.7 5.3 5.1 3.7 4.7 4.1 5.0 3.8 3.8 4.5 2.8 4.8 5.1 5.3
##  [703] 3.8 4.9 3.6 4.6 3.3 5.0 3.5 4.1 3.3 4.0 5.8 2.9 5.5 3.5 3.6 5.6 5.7 4.3
##  [721] 5.1 2.5 3.4 3.6 5.0 5.3 3.3 5.7 5.4 4.3 5.6 3.5 5.6 4.2 4.2 4.3 4.1 5.7
##  [739] 4.0 3.1 4.6 5.2 5.2 4.8 5.3 4.0 3.1 3.3 4.0 3.9 2.4 3.7 5.2 4.7 4.4 6.1
##  [757] 6.1 4.2 4.1 3.7 3.6 4.8 4.3 3.5 4.2 4.7 5.1 5.2 5.4 3.5 4.8 5.6 6.2 3.8
##  [775] 4.0 4.2 5.5 5.4 4.1 2.5 3.8 4.2 4.9 4.1 3.5 4.5 3.6 4.3 4.7 5.0 4.0 4.7
##  [793] 3.9 4.0 3.9 3.9 2.8 4.1 4.9 3.9 4.3 4.3 4.4 3.5 4.7 4.5 3.9 3.7 3.6 4.8
##  [811] 4.5 4.3 4.3 4.4 4.0 3.2 3.8 6.2 3.1 2.8 7.0 3.2 6.7 5.2 4.9 4.4 3.9 5.0
##  [829] 4.2 5.4 5.2 3.6 4.9 5.3 4.5 4.1 4.6 2.8 4.5 4.3 4.1 4.3 5.2 4.0 4.7 6.7
##  [847] 4.1 4.9 3.3 4.6 2.8 5.1 3.7 5.7 3.8 4.3 3.6 4.2 4.7 3.0 5.1 4.4 4.5 4.6
##  [865] 6.6 3.8 5.8 5.8 3.9 5.4 5.3 3.7 4.7 4.1 4.7 5.7 6.3 4.7 3.5 5.3 4.1 5.6
##  [883] 4.8 5.0 5.3 4.4 3.8 5.3 4.9 4.8 5.2 5.2 4.9 4.6 4.3 4.0 3.3 3.6 4.5 5.3
##  [901] 4.2 4.3 5.8 3.5 4.1 5.3 5.6 3.0 4.9 6.0 4.4 3.3 4.4 3.5 5.3 4.8 5.1 4.4
##  [919] 5.5 3.9 3.9 5.7 4.8 6.5 4.0 3.8 5.6 5.3 4.5 5.6 3.9 5.2 4.0 2.2 3.9 2.1
##  [937] 5.3 4.4 3.9 3.9 4.5 3.6 4.4 5.1 4.3 5.0 4.1 5.6 4.9 5.3 4.7 3.6 5.1 5.8
##  [955] 5.0 3.6 4.4 4.6 4.1 4.5 4.3 6.3 5.7 3.7 6.6 3.1 4.8 4.8 5.2 4.9 5.5 3.7
##  [973] 3.9 5.6 4.6 4.3 2.5 3.6 5.3 6.1 4.6 5.6 3.2 4.2 3.6 3.6 4.8 4.6 4.2 3.4
##  [991] 3.4 3.7 3.2 4.5 5.4 5.7 3.3 4.8 3.0 4.8
## 
## $func.thetastar
## [1] 0.0076
## 
## $jack.boot.val
##  [1]  0.52982456  0.41775148  0.26077844  0.18089888  0.05722380 -0.01032609
##  [7] -0.19236842 -0.31085044 -0.36823899 -0.52173913
## 
## $jack.boot.se
## [1] 0.9952597
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
##    [1] 5.1 4.9 4.3 4.4 3.6 5.3 5.4 5.6 4.2 4.9 5.3 3.4 4.1 4.4 5.8 5.1 3.3 5.0
##   [19] 5.6 4.7 6.3 4.1 5.9 5.4 4.4 4.2 3.4 2.7 5.6 5.4 4.2 5.5 6.0 4.7 4.4 3.8
##   [37] 3.5 3.3 4.6 4.9 2.8 5.9 6.2 2.8 5.1 3.3 5.1 4.4 3.6 4.0 3.6 4.4 5.5 4.1
##   [55] 4.4 4.3 4.9 4.1 6.7 6.6 5.6 4.8 4.0 4.8 4.5 3.4 3.3 6.3 3.6 4.7 5.7 3.3
##   [73] 5.3 4.6 5.3 3.6 3.9 5.5 4.4 3.3 3.6 3.4 5.0 6.0 3.5 5.8 3.9 2.7 4.6 4.1
##   [91] 4.2 5.2 4.0 3.9 4.8 4.1 4.0 3.2 3.6 4.8 4.0 5.1 3.6 4.8 4.5 5.3 5.5 4.9
##  [109] 4.8 4.9 5.5 4.4 5.0 5.2 3.9 2.7 4.1 4.2 4.9 4.9 4.1 4.1 3.7 5.5 4.7 5.5
##  [127] 4.9 4.7 3.6 4.1 4.1 5.7 5.5 5.5 4.4 3.9 4.6 4.5 5.0 4.4 4.4 5.2 5.8 3.9
##  [145] 5.4 4.0 5.0 5.7 5.2 5.0 5.9 5.1 4.3 4.0 3.9 5.0 4.9 5.0 4.6 4.9 3.4 6.2
##  [163] 4.1 5.2 5.3 5.6 4.6 5.3 5.5 3.8 4.4 4.0 3.8 3.0 4.3 3.9 3.3 4.6 2.8 5.6
##  [181] 4.3 3.9 4.6 5.4 4.2 3.3 5.1 4.4 4.9 4.0 5.1 5.0 3.2 5.9 2.3 5.5 5.1 5.0
##  [199] 5.8 3.9 4.8 4.4 5.8 4.5 5.3 4.5 3.5 4.5 3.4 3.9 5.2 4.3 3.0 3.7 5.5 6.0
##  [217] 4.6 5.6 4.9 4.4 3.6 3.8 4.6 3.5 4.3 5.7 3.6 6.2 4.5 2.2 4.2 3.7 5.0 1.8
##  [235] 3.8 4.2 4.0 6.2 4.0 4.2 4.6 5.2 3.6 4.0 4.9 4.5 4.5 4.6 2.5 4.5 3.9 2.7
##  [253] 4.1 3.7 4.3 2.6 4.2 3.9 3.2 4.6 5.2 5.8 3.1 3.5 2.2 4.2 4.4 2.9 4.4 4.5
##  [271] 4.3 4.0 4.7 4.7 4.4 4.5 4.1 4.9 4.9 4.1 5.3 4.8 3.4 3.6 4.9 4.8 4.7 4.3
##  [289] 4.7 3.3 6.2 4.3 4.8 4.6 2.9 4.2 4.6 4.1 4.6 3.6 5.2 4.5 3.4 5.7 3.5 2.3
##  [307] 3.6 5.5 5.1 4.0 3.3 5.2 6.4 4.0 4.4 4.0 4.1 5.4 5.7 5.5 4.2 5.4 4.2 5.6
##  [325] 4.4 4.3 4.6 4.7 1.7 4.3 4.8 3.3 4.9 5.2 3.9 3.7 5.1 4.5 5.0 5.9 5.0 6.2
##  [343] 5.9 4.6 4.6 4.2 4.9 5.0 5.3 4.3 5.1 5.6 4.1 5.2 4.6 3.1 3.4 3.9 5.7 4.1
##  [361] 4.3 3.6 5.5 6.5 4.3 5.6 4.9 5.3 4.7 5.7 3.0 4.0 5.2 3.6 6.2 5.4 3.9 3.0
##  [379] 3.4 4.5 3.9 5.0 4.0 5.3 5.5 3.5 5.1 5.0 3.1 4.7 5.3 4.3 4.5 4.9 5.1 4.5
##  [397] 6.4 4.9 4.9 4.2 4.4 4.3 6.0 3.6 3.5 5.0 4.6 3.9 5.5 3.7 4.1 3.9 4.0 4.5
##  [415] 5.2 5.1 4.6 4.4 3.8 3.6 3.8 3.4 3.6 4.1 6.0 4.0 3.8 5.1 5.1 4.6 3.8 2.4
##  [433] 5.1 3.7 5.1 5.7 6.2 5.9 4.5 4.0 3.6 6.1 4.4 3.8 4.4 5.4 5.1 5.8 3.5 4.5
##  [451] 3.7 4.1 5.3 3.0 2.4 4.3 3.5 4.4 5.0 4.2 3.5 5.5 3.6 5.8 4.6 4.0 4.5 4.0
##  [469] 5.7 3.4 3.7 5.1 4.1 5.3 5.1 5.1 4.3 3.9 4.9 3.1 3.4 4.6 3.9 5.2 6.4 5.8
##  [487] 2.4 3.8 5.5 2.9 5.4 5.8 4.8 4.1 5.4 4.6 4.5 4.7 5.0 5.5 4.5 5.6 3.7 5.1
##  [505] 3.1 4.7 5.1 4.2 5.3 2.8 4.7 4.5 5.0 5.9 4.9 3.3 5.8 3.0 4.8 4.9 3.0 4.9
##  [523] 4.6 5.4 3.7 5.7 4.2 5.2 5.8 4.0 3.7 5.3 4.4 5.3 4.9 3.6 4.6 4.4 5.2 3.4
##  [541] 4.4 3.5 3.5 4.5 5.0 3.9 5.3 6.4 4.7 5.0 5.5 3.8 6.1 4.8 3.5 3.9 5.2 4.5
##  [559] 4.5 3.6 5.5 5.2 4.2 4.8 4.4 4.9 2.9 4.8 4.7 3.0 4.4 4.6 4.7 4.2 4.6 4.0
##  [577] 3.0 5.1 4.4 1.8 3.7 3.8 5.2 4.7 6.6 5.8 4.5 6.0 4.3 5.6 4.5 4.9 4.9 3.8
##  [595] 4.1 5.4 5.4 4.2 5.0 4.8 4.1 5.0 4.9 4.0 4.9 3.2 4.4 4.6 4.5 4.8 4.5 4.4
##  [613] 3.8 4.9 3.3 5.3 4.6 4.8 4.9 3.8 2.9 4.5 3.3 4.8 4.3 6.3 5.1 4.0 5.0 3.5
##  [631] 4.9 4.5 5.1 3.8 6.3 3.8 4.4 4.6 5.1 3.6 3.7 3.2 4.6 5.1 4.0 5.2 4.2 3.5
##  [649] 4.2 4.7 4.1 3.7 4.3 4.4 6.5 3.7 4.3 4.4 4.9 3.1 4.9 5.1 3.4 5.1 6.0 3.9
##  [667] 5.9 4.0 4.0 5.7 4.6 4.4 5.3 4.1 5.0 4.7 3.6 5.2 4.5 5.1 2.8 5.7 3.6 2.9
##  [685] 4.2 4.6 5.4 4.2 3.0 5.3 6.4 5.2 4.8 5.9 3.8 4.7 4.3 4.7 5.1 2.6 4.7 4.3
##  [703] 5.9 5.8 3.3 5.2 4.8 5.5 3.6 4.2 5.2 4.6 5.1 3.4 4.8 4.6 4.9 4.4 4.7 5.0
##  [721] 4.6 4.0 4.5 5.2 4.0 3.2 3.9 4.9 5.0 3.3 2.4 4.0 4.3 4.4 3.9 5.1 5.0 4.7
##  [739] 4.5 3.4 5.0 4.8 4.9 4.7 4.3 3.3 4.6 4.5 3.8 5.2 5.3 3.9 5.8 5.8 3.5 2.7
##  [757] 3.9 4.5 2.9 6.3 3.8 4.6 2.7 3.0 2.9 5.5 5.5 4.6 5.2 5.3 5.3 3.9 4.9 4.1
##  [775] 2.7 5.5 5.7 4.3 5.5 2.6 6.6 4.9 4.2 4.7 4.4 3.0 3.6 4.2 4.7 5.3 5.8 4.9
##  [793] 2.9 4.5 4.1 4.4 3.5 5.5 5.0 7.4 5.2 5.4 5.2 4.8 2.6 4.5 6.0 2.2 3.8 3.1
##  [811] 6.8 3.9 5.5 4.9 2.7 4.5 3.9 5.2 3.6 3.7 4.0 5.4 3.3 3.6 2.4 5.8 3.8 3.1
##  [829] 4.0 6.5 3.7 2.1 5.7 2.1 4.6 4.8 4.6 3.8 5.2 4.0 3.8 3.8 4.0 4.0 5.6 3.8
##  [847] 4.1 3.7 5.3 5.2 4.6 4.8 4.0 5.3 4.7 3.8 4.6 3.2 4.4 4.8 5.9 3.4 4.6 4.7
##  [865] 5.8 3.5 2.4 4.5 5.5 2.4 4.5 4.8 2.8 5.2 5.2 3.7 5.3 3.4 4.8 4.4 5.7 4.4
##  [883] 3.5 4.8 5.2 4.4 5.5 5.2 5.2 4.8 2.5 3.5 5.5 6.5 3.0 5.2 3.6 2.9 4.0 5.7
##  [901] 3.8 3.7 4.1 3.7 3.8 5.0 4.0 5.4 3.2 6.0 4.7 5.6 5.2 3.6 4.5 3.0 2.7 3.8
##  [919] 5.2 4.2 4.1 5.4 3.3 3.5 4.4 3.2 4.8 5.5 2.6 6.6 4.4 5.4 5.5 4.8 4.8 5.3
##  [937] 4.0 3.6 6.2 4.9 6.1 5.7 5.5 5.4 4.5 4.2 3.9 3.4 5.5 4.2 3.7 2.5 5.2 5.1
##  [955] 4.7 4.8 5.4 4.1 5.3 4.7 4.1 3.9 3.7 5.7 4.4 4.6 4.7 3.3 4.5 3.0 5.5 4.1
##  [973] 5.1 5.0 5.4 3.6 3.9 3.9 5.5 4.9 3.6 5.1 5.0 2.4 3.2 4.1 4.8 4.4 3.9 5.6
##  [991] 6.3 3.9 2.1 5.6 4.5 4.5 3.9 5.7 3.6 3.9
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.3 5.3 5.1 5.0 4.9 4.8 4.6 4.5
## 
## $jack.boot.se
## [1] 0.9396276
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
## [1] 0.6897955
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
##   2.2789208   4.9473795 
##  (0.9540075) (2.3159083)
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
## [1] 0.4214201 0.2762869 0.8286739 1.9014644 1.6809372 0.8178569
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
##    [1]  4.204471e-01  1.741044e-01  2.789661e-01  7.609241e-01  7.234667e-01
##    [6]  3.827567e-01  3.946177e-01  4.796436e-01  1.450942e+00  4.906233e-01
##   [11]  1.017930e+00 -9.181148e-02 -8.310040e-02  1.647503e-01  9.817296e-02
##   [16]  2.973424e-01  3.047994e-06  1.479524e+00  9.868568e-01  9.148609e-01
##   [21]  6.640416e-02  4.449744e-01  2.336428e-01  1.489843e+00 -5.379913e-01
##   [26]  6.981583e-01 -3.577739e-02  5.351113e-01  3.631662e-01  3.609819e-01
##   [31]  1.028285e+00 -9.105594e-03  7.239362e-01  1.503545e+00  1.357191e-01
##   [36]  4.330137e-01  1.759005e+00  7.103541e-01  1.262832e+00  1.433254e+00
##   [41]  2.237151e-01  4.472898e-01  7.561740e-02  1.675184e-01  4.573065e-01
##   [46]  1.784622e+00  2.106049e-01  1.979258e-03  9.114588e-01 -1.711043e-01
##   [51]  2.848289e-01  2.499923e-01  1.141176e+00  1.678286e+00  3.450196e-01
##   [56]  9.582931e-03  4.571934e-01  1.533429e-01  8.561169e-01  2.943287e-01
##   [61]  1.067621e+00  4.222466e-03  6.451166e-01  1.166367e+00  2.574733e-02
##   [66]  5.384476e-01  3.547471e-01  6.197746e-02  1.331963e+00 -6.721609e-01
##   [71]  8.844552e-01  3.696403e-01  9.354512e-01  9.473595e-01  1.144767e+00
##   [76]  2.184582e-01 -9.420878e-02  2.744507e-01  2.602416e-01 -4.117419e-01
##   [81]  2.933328e-01  4.752889e-01  4.505356e-01  4.492430e-01 -7.693294e-02
##   [86]  9.598405e-01  4.038702e-01  4.374305e-01  8.254854e-01 -9.467800e-01
##   [91]  1.042192e+00  1.865228e+00  4.728156e-01  5.070210e-01  3.288932e-01
##   [96]  1.474342e+00 -2.033232e-01  4.608040e-01  1.401785e+00  2.423944e-01
##  [101]  6.531361e-01  1.491771e-01  3.410977e-01  1.481107e+00  4.208575e-01
##  [106]  9.762869e-01  1.292551e+00  8.396084e-01  6.375940e-01 -3.264124e-01
##  [111]  4.351276e-01 -6.772423e-03  1.042626e+00  7.642821e-01  9.194975e-01
##  [116] -3.908432e-01  3.812796e-01  4.500196e-01  8.636549e-01  8.055391e-01
##  [121]  8.604021e-01 -2.322081e-01  2.017196e-02  1.138690e+00  2.486738e-01
##  [126]  4.642809e-01 -1.982678e-01  6.408311e-01  1.426481e-01  7.095927e-01
##  [131]  6.097232e-01  2.231157e+00 -5.572365e-02  7.012073e-01  2.596529e-01
##  [136]  2.267140e-01  2.278469e-01  5.885662e-01 -5.937874e-02  9.790632e-01
##  [141]  2.826295e-01  7.252655e-01 -7.175177e-01  8.269998e-01  1.503316e+00
##  [146]  3.915744e-01  1.015395e-01  7.751511e-01  3.173944e-01  1.755359e-01
##  [151] -2.703030e-01  7.556017e-01  5.124356e-01  7.928967e-01 -2.690030e-01
##  [156]  5.306283e-01  1.141464e+00  5.207091e-02 -1.318462e-01  1.062208e-01
##  [161]  1.443372e+00  1.392418e-02  8.949104e-01  1.377533e+00  9.543489e-01
##  [166]  1.263834e+00  5.626372e-01  1.451126e+00  5.076272e-01  9.792772e-01
##  [171]  7.630320e-01  1.049409e+00  2.176187e+00  7.001090e-01  8.970647e-01
##  [176]  5.310071e-01  2.094616e-01 -5.444303e-01 -7.515968e-02  1.658284e-01
##  [181]  1.087777e-01 -2.390752e-01  1.641654e+00  2.718375e-01  3.337005e-01
##  [186] -1.201687e+00  7.572761e-01 -7.148110e-01  4.742732e-01  5.864075e-01
##  [191]  1.061632e+00  3.848583e-01 -5.259539e-01  3.914770e-01  8.400737e-01
##  [196]  8.864957e-01  1.562511e+00 -3.709677e-02  4.097602e-01  2.155666e-01
##  [201]  9.320153e-01  4.801662e-01  2.630943e-01  9.355423e-01  5.473616e-01
##  [206]  5.296298e-01 -5.426517e-01  2.368863e-01 -1.415511e-03  5.900847e-01
##  [211]  2.009504e-01  9.839697e-01  6.615419e-01  5.223975e-01  9.956246e-02
##  [216]  8.232984e-01  7.492317e-01  5.045758e-01 -5.239773e-02  2.200454e-01
##  [221]  6.484654e-01  2.849288e-01  1.009892e+00  5.920708e-01  8.164033e-01
##  [226]  8.329395e-01  1.084300e+00  3.111039e-01  7.538504e-01  1.858040e+00
##  [231]  1.250353e+00  9.272315e-01  7.629419e-01  6.205671e-01  4.168244e-01
##  [236]  4.463588e-02  9.852056e-01  5.486353e-01  1.363853e+00  5.533785e-01
##  [241]  1.594661e+00  1.008898e+00  2.205370e-01  5.969200e-01 -1.814601e-01
##  [246]  1.596773e+00  1.013890e+00  3.118697e-01  8.827320e-01  3.207004e-01
##  [251]  5.720567e-01  2.034748e-02 -1.040688e-01  2.986053e-01  7.579156e-01
##  [256]  7.941359e-01  3.231844e-01  1.122651e+00  3.184636e-01  2.733931e-01
##  [261]  6.105827e-01  7.723288e-01  1.099754e+00  3.056307e-01  3.906518e-02
##  [266]  1.170388e-01 -5.904578e-01  5.525333e-03  6.754285e-01 -1.316896e-01
##  [271]  1.381205e+00  4.737651e-02  5.386363e-01  9.015221e-02  6.156978e-01
##  [276]  6.147201e-01  6.014808e-01 -7.903610e-02  1.168522e-01  1.580202e+00
##  [281]  1.583599e+00  4.745395e-01  1.555601e+00  7.078496e-01  8.593871e-01
##  [286]  1.122679e+00 -1.066507e-01  4.835189e-01  5.193721e-02  1.561073e-01
##  [291]  1.995463e-01  5.395545e-01  5.638699e-01 -2.928557e-03  5.970468e-01
##  [296]  7.144060e-01 -1.484296e-01  3.648488e-01 -4.444729e-01  8.063419e-01
##  [301]  1.650543e+00  1.225848e+00  6.987322e-01  4.514040e-01  6.534989e-01
##  [306]  7.484231e-01  2.821462e-01 -6.441082e-02  2.804712e-01  4.682442e-01
##  [311]  8.539067e-01  1.124178e+00  3.511122e-01  7.417377e-01  5.080783e-01
##  [316]  7.238992e-01  7.448885e-01  1.168837e+00  4.256813e-01  7.428182e-01
##  [321]  6.722656e-01  4.887427e-01  8.420359e-01  6.200354e-01  6.537310e-01
##  [326]  4.780337e-01  5.342534e-01  1.042890e+00 -2.594762e-01  4.090051e-01
##  [331]  4.469897e-01  8.142007e-01  9.371356e-01  8.662855e-01  4.788703e-01
##  [336]  5.494634e-01  4.928127e-01 -3.320928e-01  2.388861e-01 -4.706321e-02
##  [341]  5.321476e-01  8.811009e-01  6.696491e-01  3.483585e-01  1.049810e+00
##  [346]  4.040726e-01  8.242973e-01  3.039236e-01  5.286068e-01  1.109925e+00
##  [351]  4.334120e-01  1.006735e+00  4.555721e-01 -3.919158e-01  3.149513e-01
##  [356]  2.277747e-01  9.812326e-01  3.294944e-01 -1.125695e-01  5.536815e-01
##  [361]  1.377406e-01 -9.928555e-02  1.288015e+00  1.185689e+00 -2.191724e-01
##  [366]  1.944156e+00  3.315753e-01 -6.193196e-02 -5.419579e-01 -5.450675e-01
##  [371]  3.094498e-01  7.666493e-01  9.937035e-01 -7.440007e-02  1.410037e+00
##  [376]  9.769036e-01  4.269801e-01  5.846796e-01  4.091771e-01  6.606815e-01
##  [381]  7.007126e-01  2.485264e-01  3.537222e-02  9.447231e-01  6.433596e-01
##  [386]  5.805257e-01  9.128672e-01 -7.248167e-02 -1.872026e-01  5.397949e-01
##  [391]  1.030397e+00  7.879209e-01  3.331825e-01  1.246804e+00  9.185404e-01
##  [396]  5.924499e-01  3.626513e-01  5.560605e-01  9.106911e-01  5.255819e-01
##  [401] -9.156278e-02 -4.137404e-01  1.192153e+00  3.889146e-01  9.194975e-01
##  [406]  6.715352e-01  2.373582e-01  3.860292e-01  8.694867e-01  1.687988e+00
##  [411]  9.333775e-01  6.050441e-02  9.455791e-01 -4.698419e-01  1.031578e+00
##  [416]  6.805403e-01  1.323536e-02 -2.174271e-01 -1.258422e-01  1.551539e+00
##  [421]  5.668710e-01  6.600568e-01  6.529180e-01  1.194325e+00  7.183994e-01
##  [426] -7.164039e-02  2.195008e-01  1.750680e-01  2.316867e-01  4.261739e-01
##  [431] -1.764797e-01  8.396733e-01  1.554500e-01  1.511209e-01  3.073533e-01
##  [436]  9.947115e-01 -1.916653e-01  7.420884e-01 -2.274911e-01  7.020155e-01
##  [441]  6.222788e-01  4.922696e-01  7.037753e-01  7.583919e-01 -3.125019e-01
##  [446] -1.753712e-01  9.630331e-02  6.789879e-01  1.563975e-01  1.143177e+00
##  [451]  5.761098e-01 -7.065245e-02  2.770087e-01  9.740883e-01  4.764242e-01
##  [456]  6.529481e-01  1.175488e+00  1.114552e+00  9.841690e-02  1.177852e-01
##  [461]  8.854245e-01  5.473036e-01  6.605208e-01  3.526619e-01  9.229024e-01
##  [466] -6.193196e-02  1.278828e-02  2.316872e-01  3.139722e-01  1.287970e+00
##  [471]  1.749850e-01  1.129859e+00  8.319655e-01  5.897621e-01  6.575122e-01
##  [476] -1.845477e-01 -4.288065e-02  1.990615e-01  7.132908e-01  9.718158e-01
##  [481]  8.253302e-01  7.173195e-01  4.064494e-02  2.933328e-01  3.186180e-01
##  [486]  2.976974e-01  4.353671e-01  2.823587e-01  5.332456e-01  9.305110e-01
##  [491]  4.195048e-01  1.306778e+00  1.336239e+00  6.196943e-01  1.124437e-01
##  [496]  5.079849e-02 -9.666955e-02  6.312046e-02  6.428142e-01  9.691447e-01
##  [501] -5.226557e-03 -5.830558e-02  6.741900e-01 -3.988163e-01 -1.558567e-01
##  [506]  6.191290e-01  1.513825e+00  4.936378e-01 -2.712693e-01  6.808867e-01
##  [511]  7.714417e-01  1.391586e+00  3.529235e-02  8.950456e-01  1.096678e+00
##  [516]  5.282392e-01 -2.254105e-01  9.053175e-01  5.967942e-01  1.194479e-01
##  [521]  1.678286e+00 -1.178345e-02  2.762858e-01  5.788508e-01 -1.451741e-01
##  [526]  1.034301e+00 -2.369648e-01  9.656366e-01  9.526266e-01  4.149601e-01
##  [531]  3.139108e-01  7.249320e-02  9.296328e-01  9.346557e-01  9.540252e-01
##  [536]  2.089813e-01  1.514972e+00  9.728005e-03  2.068604e-01  7.518948e-01
##  [541]  9.121144e-01  3.711791e-01  8.798176e-01  7.522793e-01 -5.777177e-02
##  [546]  2.643997e-01  9.909587e-01  1.037349e+00  5.443412e-01  5.242773e-01
##  [551]  1.240989e+00  4.356066e-01  7.763834e-01 -9.533735e-02 -2.051225e-01
##  [556] -1.315075e-01  9.404364e-01  6.759428e-01  1.120434e-02  4.592315e-01
##  [561]  1.186260e+00 -3.935758e-01 -1.273657e-01  2.437130e-01  2.165966e-01
##  [566]  1.468678e+00  1.256843e+00  1.330938e-01  6.217178e-01  8.691733e-01
##  [571]  7.294001e-01  1.689880e-01  7.630320e-01 -5.005443e-02  3.934638e-01
##  [576]  3.923803e-01  7.348041e-01  5.639638e-01  5.573551e-01 -6.418372e-01
##  [581]  4.655808e-01  4.497262e-01 -3.500131e-03  9.826879e-02 -3.608680e-02
##  [586]  3.239001e-01  5.973799e-01  6.099519e-01  4.606351e-01  2.155666e-01
##  [591] -6.905701e-02  4.543899e-01  6.406336e-01  1.021430e-01  7.000518e-01
##  [596]  2.940215e-01 -2.370196e-01  9.722507e-01  5.360289e-01  7.187178e-01
##  [601]  5.975721e-01  4.683830e-01  8.525826e-01  2.655857e-01  3.888601e-01
##  [606] -7.908051e-01  6.571318e-01  5.756374e-01  1.076161e+00  6.166307e-01
##  [611]  3.771267e-01  1.093484e-01  7.320253e-01  7.824911e-01 -4.446518e-01
##  [616]  1.105594e+00 -8.417486e-01  3.799499e-01  1.287114e+00  8.001170e-02
##  [621]  4.322458e-01  2.560105e-01  1.147804e+00 -9.261255e-02  3.789040e-03
##  [626]  7.674606e-01  1.044902e+00  9.822228e-01 -1.010263e-01 -1.456494e-01
##  [631]  1.190036e+00  7.598218e-01 -1.253048e-02 -5.898546e-02 -1.246543e-01
##  [636]  8.115078e-01  7.825051e-01 -6.114562e-01  1.597777e+00  1.041233e-01
##  [641]  1.166980e+00  7.481520e-01  6.920596e-02  7.183552e-01  8.326256e-01
##  [646]  5.969200e-01  7.548622e-01  6.492309e-01  5.370721e-01  1.110455e+00
##  [651]  1.519280e-01 -1.363879e-01 -5.549330e-03  6.082585e-01 -3.521519e-01
##  [656]  1.163702e+00  1.069436e+00 -4.598432e-02  4.833774e-02  5.926887e-01
##  [661]  1.007100e+00  3.685426e-01  2.396817e-01  2.413293e-01  6.056664e-01
##  [666]  3.260606e-01  8.087084e-01 -6.196487e-01  4.102701e-01  8.233168e-01
##  [671] -3.767459e-01  1.858446e+00  7.580751e-01 -7.500795e-02  5.679688e-01
##  [676]  1.575047e-01  7.982906e-01  5.920355e-01  5.471267e-01  8.425193e-02
##  [681] -4.278132e-01  7.178436e-01  4.671623e-01  2.037310e-01 -8.950551e-02
##  [686] -5.737821e-02  8.422787e-01  7.623786e-01  5.750460e-01  3.457389e-01
##  [691]  1.015257e+00  1.144010e-01  3.674445e-01  5.900362e-01  9.608099e-01
##  [696]  5.157251e-01 -1.882716e-01  8.054517e-01  1.411730e-01  7.400603e-01
##  [701] -1.534272e-01 -1.644589e-02  7.863036e-01  2.772502e-01  1.333642e-01
##  [706]  7.236015e-01  3.786839e-01  1.190036e+00  1.196724e-01  1.021199e+00
##  [711]  7.154701e-01  5.668710e-01 -2.455534e-01  2.951981e-01  9.552800e-01
##  [716]  8.404094e-01  1.699329e-01 -3.867220e-02  6.757931e-01 -1.467775e-01
##  [721]  6.359356e-02  2.059289e+00  6.966844e-01  3.082570e-01  5.940104e-01
##  [726]  4.872137e-01  6.076769e-01  2.667031e-01 -2.958960e-01 -1.219061e-02
##  [731]  1.260351e+00  6.237424e-01 -1.404943e-01  1.429681e+00 -8.651385e-02
##  [736]  4.229487e-01  3.636857e-01  1.068750e+00 -3.289031e-01  6.927539e-01
##  [741]  4.859040e-01  2.155169e+00  1.030983e+00  7.676556e-01  2.731890e-02
##  [746]  8.939196e-01  8.184914e-01  9.401826e-01  2.529987e-01 -5.458990e-01
##  [751]  1.691568e+00  3.008852e-02  1.040624e+00  7.830614e-01  6.926211e-03
##  [756]  6.952531e-01  9.031690e-01  5.873833e-01  1.728927e+00 -9.346035e-02
##  [761] -2.728789e-02  7.468233e-01  9.152851e-01  3.275064e-01  1.126131e+00
##  [766]  6.588374e-01  4.116484e-01  4.964550e-01  1.436464e-01 -1.064305e+00
##  [771]  5.359977e-01  7.330195e-01  9.850619e-02  7.526674e-03  1.074727e+00
##  [776]  9.818662e-01  8.735526e-01  9.970091e-01  2.668445e-01  1.322433e+00
##  [781]  7.204896e-01  1.236283e+00  6.909349e-01 -4.085267e-02  3.187998e-01
##  [786]  2.914723e-01  1.504139e+00  3.897889e-01  1.258720e+00  7.541837e-02
##  [791]  4.261639e-01  3.164142e-01  9.725186e-02  7.053763e-01  9.660337e-03
##  [796]  1.719585e-01  6.843808e-01  9.471562e-01  8.775703e-01  6.569828e-01
##  [801]  2.324859e-01  1.307311e+00  1.454265e-01  7.303010e-01  4.752089e-01
##  [806]  5.381478e-02  1.201091e+00  2.443968e-01  1.490896e+00  9.300495e-01
##  [811]  8.659990e-01  5.831414e-03  4.061545e-01  1.183504e+00  1.220109e-01
##  [816]  9.011232e-01 -2.732018e-02  8.456754e-01  1.916184e-01  1.088344e+00
##  [821]  5.244280e-01  7.130835e-01  3.502508e-01  7.226340e-01  5.591500e-01
##  [826]  2.908572e-01 -2.377472e-01 -4.505084e-01  3.154817e-01  1.335588e+00
##  [831]  7.777385e-01  7.568156e-01  3.093579e-01  8.867886e-02 -1.173052e-01
##  [836]  1.216625e-01  4.512217e-01  5.701920e-01  7.463621e-01  2.309511e-01
##  [841] -2.570132e-01 -8.397462e-01  3.294261e-01  7.331414e-01  7.436780e-01
##  [846]  1.150904e+00  4.017075e-01  3.916742e-01  6.776546e-01  6.159868e-01
##  [851]  4.571934e-01  2.533509e-01  5.758381e-01  7.487113e-01  1.392145e+00
##  [856] -3.737124e-01  5.269928e-01  6.506781e-01  5.564604e-01  1.990615e-01
##  [861]  9.088510e-01  3.335358e-01  5.476506e-01  9.198974e-01  6.280329e-01
##  [866]  3.303000e-01  8.880091e-01  4.907668e-01  6.746509e-01  5.020620e-01
##  [871]  3.272300e-01  1.752983e+00 -3.664759e-01  9.284786e-01  3.439720e-01
##  [876]  6.234049e-01 -4.052220e-01  8.575184e-01  5.606995e-01  7.997732e-01
##  [881]  2.044005e-01  5.661894e-01  7.438459e-01  4.588666e-01  1.167716e-01
##  [886]  9.394428e-01 -4.821971e-02  4.711914e-01  1.218989e+00  7.312694e-01
##  [891]  4.363908e-01  7.543648e-01  1.660757e+00  1.294294e+00  1.160222e+00
##  [896]  2.935369e-01 -2.612399e-01  3.453143e-02  1.231835e+00  1.296629e+00
##  [901] -2.549113e-01 -3.008253e-01  3.717287e-01  1.468045e-01  1.067493e-01
##  [906] -7.650976e-02 -3.322230e-01  1.104961e+00 -3.357605e-01  1.705319e-01
##  [911]  4.619025e-01 -1.993690e-01  3.686648e-01  1.064431e+00  2.706550e-01
##  [916]  3.552798e-01  6.573003e-01  2.792647e-01  2.005254e-01  6.987322e-01
##  [921]  1.141336e+00  1.838094e-01  7.092273e-01  1.882785e-01  1.726709e-01
##  [926]  1.145537e+00  7.058137e-01  3.598451e-01  7.184765e-01  6.747496e-01
##  [931]  6.456682e-01  1.629742e-01  7.720634e-01  6.648033e-01  7.769687e-01
##  [936]  4.364584e-01  3.512225e-01  7.229842e-01  1.929653e-01  3.676213e-01
##  [941]  1.398031e+00 -3.825526e-01  6.474031e-01  7.748977e-01  5.562819e-01
##  [946]  5.731976e-01  3.338102e-01  8.736118e-01  2.118242e-01  4.630006e-01
##  [951]  7.303996e-01  5.936757e-01  1.079180e+00  1.579478e+00  1.637410e+00
##  [956]  1.814663e+00 -2.724085e-01  9.788438e-01  4.622435e-01  6.393662e-01
##  [961]  5.949887e-01 -3.786235e-02  1.678286e+00  4.284515e-01  6.937470e-01
##  [966]  3.928083e-01  2.000564e-01  3.023402e-01  1.159955e+00  4.099102e-01
##  [971]  1.072362e+00  1.698582e-01  5.319668e-01  1.064370e+00  6.643980e-01
##  [976]  1.089954e+00  7.208104e-02  4.474270e-01  5.930120e-01 -1.142539e-01
##  [981]  2.680096e-01  7.634486e-01  1.339967e+00  1.286291e+00  3.402871e-01
##  [986]  2.480446e-01  2.753913e-01  4.669628e-01  1.012324e+00 -1.028435e-01
##  [991]  1.337364e+00  8.480882e-01  7.567125e-01  7.003960e-01  9.155715e-01
##  [996]  1.513985e+00  1.682977e+00  2.755825e-01  1.314578e+00 -3.102425e-01
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
```

```r
fit2
```

```
##       mean          sd    
##   0.46063168   0.29502457 
##  (0.09329496) (0.06596605)
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
## [1]  0.03217563  0.58945989 -0.71041599 -0.59821025  0.17163645 -0.24459262
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
## [1] -0.0346
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9232308
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
## t1*      4.5 -0.05185185    0.914246
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 6 8 9 
## 2 1 2 1 2 1 1
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
## [1] -0.0287
```

```r
se.boot
```

```
## [1] 0.9131156
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

