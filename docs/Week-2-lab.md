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
## 0 1 3 4 5 6 8 9 
## 1 1 1 1 2 1 2 1
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
## [1] 0.0029
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
## [1] 2.66616
```

```r
UL.boot
```

```
## [1] 6.33964
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
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
##    [1] 5.0 4.6 4.9 3.9 3.5 4.6 4.5 3.7 4.7 5.2 4.0 5.9 4.5 6.6 4.3 4.3 4.3 4.0
##   [19] 4.6 3.5 4.6 4.0 4.2 4.3 4.2 5.2 4.5 3.8 3.4 3.7 4.3 7.0 4.3 4.8 4.3 2.9
##   [37] 5.5 4.7 4.9 4.5 3.8 4.8 3.6 3.2 4.5 5.8 5.5 4.4 5.0 3.9 4.6 4.7 3.6 4.3
##   [55] 4.1 4.2 4.0 4.6 4.2 5.2 4.5 4.6 2.6 4.3 4.0 1.8 4.8 4.3 5.2 4.1 4.6 2.5
##   [73] 5.1 4.8 5.8 6.0 5.0 5.3 4.2 4.9 3.8 4.7 6.1 4.2 5.0 4.3 6.1 3.2 5.3 2.3
##   [91] 3.6 5.4 4.4 4.5 4.1 4.0 4.3 6.0 4.5 5.2 4.9 3.6 5.9 5.0 4.8 2.9 3.8 5.1
##  [109] 4.2 5.8 4.2 4.4 5.0 5.3 4.5 3.8 4.3 4.7 3.3 4.3 3.9 2.7 5.3 3.5 4.7 4.7
##  [127] 5.0 4.6 4.6 4.5 5.4 4.2 5.0 4.4 3.9 4.6 4.1 5.1 2.5 4.7 3.3 4.5 5.2 4.5
##  [145] 4.5 4.4 5.6 4.7 4.3 5.5 3.6 5.3 5.5 4.4 3.4 2.9 4.4 5.9 4.3 3.0 5.4 5.3
##  [163] 3.9 4.1 5.6 4.4 4.1 5.2 4.2 4.2 4.4 3.6 4.4 4.6 5.7 4.8 4.1 5.8 4.6 4.7
##  [181] 4.9 4.5 5.6 4.8 3.4 3.6 4.2 4.1 6.6 3.3 4.7 4.9 3.0 4.6 4.2 4.7 3.5 5.6
##  [199] 4.7 4.2 4.4 5.2 5.3 4.5 4.3 5.4 4.4 3.7 5.2 5.3 4.7 4.8 3.1 4.7 5.7 4.9
##  [217] 4.5 3.7 2.9 3.7 4.9 5.5 3.4 5.1 3.6 3.5 4.9 5.0 3.5 5.0 5.7 4.4 5.0 3.8
##  [235] 5.5 4.8 5.2 4.6 2.9 4.0 5.4 3.4 5.4 4.5 4.0 3.1 5.6 2.9 4.8 3.0 4.5 4.1
##  [253] 4.9 2.6 4.8 5.9 4.2 6.0 3.9 3.9 5.4 5.0 3.9 3.5 2.8 4.5 4.6 4.4 4.5 5.1
##  [271] 6.3 5.5 5.2 4.7 4.6 4.9 4.8 5.8 6.4 4.5 4.3 4.8 3.7 3.4 4.3 5.2 3.7 4.5
##  [289] 6.5 5.3 4.5 3.5 3.2 5.9 3.1 2.9 3.1 5.5 4.9 4.9 4.6 3.3 5.2 3.4 4.8 3.6
##  [307] 5.5 4.6 4.4 5.1 3.9 3.6 5.7 3.9 3.4 4.9 3.7 3.4 2.8 4.0 2.3 4.4 4.4 4.2
##  [325] 5.1 4.1 6.6 4.6 4.2 5.2 4.1 4.8 3.4 3.5 3.1 4.7 6.3 4.6 4.6 6.7 4.3 4.8
##  [343] 2.5 4.3 4.1 4.6 4.9 5.3 5.2 6.1 3.6 3.6 5.3 5.1 4.1 4.0 5.5 4.7 4.7 3.6
##  [361] 5.2 4.5 4.2 5.7 3.2 3.2 4.6 5.1 4.4 3.4 4.3 3.1 4.6 4.6 5.9 5.5 5.3 4.9
##  [379] 3.6 3.8 5.3 4.2 4.5 5.0 5.3 4.5 4.3 4.0 3.7 4.9 6.2 4.8 5.3 5.8 3.8 4.2
##  [397] 4.7 3.5 4.5 3.6 5.2 6.3 2.9 4.7 4.6 3.5 4.8 6.1 5.3 5.1 5.4 6.1 3.8 4.9
##  [415] 2.7 3.9 3.2 4.7 3.4 4.4 3.9 3.8 3.5 3.9 4.9 5.1 3.7 2.5 6.0 4.9 3.4 5.2
##  [433] 4.2 4.8 5.4 5.4 3.9 5.4 4.6 4.4 2.8 5.4 3.6 4.9 3.1 4.3 5.0 6.6 5.2 3.3
##  [451] 3.3 4.2 4.4 4.1 3.6 5.4 5.1 4.2 4.5 5.1 3.6 4.8 3.6 5.7 4.2 4.3 3.8 3.2
##  [469] 4.2 3.6 4.2 5.2 5.0 3.1 5.3 5.5 3.7 5.1 5.2 6.2 4.8 4.2 5.7 3.9 3.6 3.8
##  [487] 5.1 4.4 4.5 4.9 4.2 4.6 5.4 6.0 5.6 4.2 5.1 5.4 4.1 4.6 4.7 4.9 4.8 5.0
##  [505] 5.5 6.4 5.3 4.9 4.1 4.3 5.2 4.8 5.7 5.1 5.8 4.9 4.4 4.1 3.8 4.9 4.4 3.5
##  [523] 3.6 5.5 4.3 2.0 5.6 3.3 4.5 4.5 5.6 6.7 5.1 3.8 4.6 3.9 4.0 2.8 3.5 4.3
##  [541] 3.9 3.5 5.3 3.7 4.2 4.8 4.2 5.6 5.5 5.2 6.1 4.1 3.9 5.7 5.9 4.7 4.9 5.7
##  [559] 3.5 3.9 4.8 5.3 4.6 4.2 4.7 3.8 4.7 3.5 2.4 4.1 4.8 6.2 5.8 4.8 4.7 4.5
##  [577] 5.4 4.9 5.9 5.5 4.5 4.0 4.1 5.1 4.0 5.2 6.0 2.6 4.7 5.7 5.7 4.6 4.5 5.5
##  [595] 5.9 4.9 4.9 3.6 5.3 3.3 4.3 3.4 4.8 4.2 3.6 5.9 4.1 4.4 5.7 4.5 3.9 6.8
##  [613] 4.3 3.6 4.4 4.6 3.8 2.6 5.2 5.1 5.2 4.1 4.0 5.4 3.8 4.1 4.8 1.9 4.5 3.1
##  [631] 5.6 4.6 5.7 4.4 4.6 3.1 3.9 2.5 4.0 4.5 4.8 3.4 4.1 5.3 5.4 5.8 4.5 5.7
##  [649] 1.8 4.4 3.0 3.5 4.6 3.7 4.0 5.4 7.0 3.6 4.1 5.0 3.6 4.7 3.7 6.6 4.9 4.0
##  [667] 5.0 3.7 4.0 4.6 5.3 4.2 5.4 4.4 3.0 4.3 5.4 3.3 4.2 4.4 5.3 3.6 3.9 3.8
##  [685] 5.9 4.2 3.2 5.3 5.4 3.6 4.3 2.8 3.2 4.8 3.4 5.3 3.9 4.0 4.5 4.8 5.2 5.1
##  [703] 3.9 3.6 3.1 3.1 4.7 4.8 4.4 5.0 4.2 5.3 3.8 4.4 4.1 4.4 4.8 5.6 5.9 4.0
##  [721] 3.2 4.6 2.8 4.5 4.7 4.6 3.3 4.2 4.0 4.8 1.8 4.3 4.3 5.1 4.7 5.0 4.7 3.9
##  [739] 5.0 6.2 4.4 3.7 4.4 3.0 5.2 3.2 4.3 5.4 3.6 4.3 4.0 3.0 3.7 4.2 3.2 5.5
##  [757] 3.7 6.2 4.2 3.1 6.3 4.5 5.0 4.1 3.0 6.0 5.8 3.9 4.8 5.1 4.3 5.7 5.4 3.6
##  [775] 3.6 4.2 2.7 5.1 3.7 4.8 5.0 5.1 5.1 4.7 4.8 4.1 4.8 4.4 4.2 5.5 5.6 4.9
##  [793] 3.8 4.1 5.1 3.2 4.1 5.3 4.2 3.6 3.8 3.9 4.8 5.6 4.8 4.7 4.0 5.5 3.0 4.3
##  [811] 4.3 3.8 5.9 3.3 3.8 4.4 4.0 4.3 5.4 3.9 4.8 4.7 2.7 4.7 5.4 4.4 4.4 3.6
##  [829] 5.4 5.6 4.2 5.2 4.2 4.5 6.1 3.6 5.7 5.3 3.9 4.3 3.8 4.5 4.9 4.6 4.8 4.1
##  [847] 3.5 3.5 4.8 5.8 5.8 4.0 4.3 5.3 4.6 4.7 3.9 5.0 5.2 3.5 2.9 3.7 3.8 3.7
##  [865] 5.1 5.7 4.0 2.9 5.0 3.7 3.4 5.4 5.5 5.6 3.4 4.5 5.2 3.3 3.6 4.0 3.4 4.1
##  [883] 3.3 5.2 5.3 6.2 4.5 4.7 5.5 4.1 5.4 5.4 4.1 2.3 4.7 5.6 5.8 4.3 3.6 5.2
##  [901] 5.8 3.5 3.6 3.6 5.7 4.3 6.1 5.1 6.1 4.3 3.5 4.8 5.1 2.9 5.0 5.2 4.2 4.1
##  [919] 4.5 5.3 5.2 3.2 6.0 3.5 4.4 4.0 4.0 5.4 5.0 5.4 3.9 4.4 2.8 3.9 4.1 5.3
##  [937] 3.4 6.1 3.0 5.1 5.2 4.4 5.4 4.5 4.1 3.8 4.7 4.4 3.5 5.1 4.5 5.8 3.6 4.6
##  [955] 5.6 5.6 3.0 3.3 4.7 4.1 4.0 3.3 4.9 5.4 5.1 4.4 5.3 4.7 4.4 4.7 3.1 4.4
##  [973] 4.5 5.7 3.5 5.1 3.0 4.5 3.6 5.8 5.3 3.7 3.0 2.7 5.0 4.9 4.5 6.3 4.6 4.3
##  [991] 4.5 4.5 5.1 4.9 5.5 5.4 3.1 5.2 6.2 5.7
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
## 2.8000 6.1025
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
##    [1] 5.0 4.7 3.3 5.6 5.4 4.1 4.2 3.8 4.2 5.1 4.7 4.5 4.7 3.6 2.8 3.8 2.8 4.6
##   [19] 2.7 5.3 4.4 3.1 4.8 5.5 6.2 4.6 5.4 4.8 3.5 3.9 4.8 5.9 2.9 4.2 5.8 4.9
##   [37] 3.8 4.5 3.8 4.7 5.2 4.2 3.7 3.9 3.9 4.3 4.6 5.5 3.3 5.1 5.5 4.5 3.6 5.5
##   [55] 3.1 4.3 4.4 4.8 4.5 3.1 4.2 2.5 6.2 4.4 5.1 4.0 3.4 3.8 5.1 4.5 5.6 5.3
##   [73] 3.9 4.1 6.4 4.6 5.2 3.4 3.7 4.2 3.4 4.1 4.9 5.3 3.8 4.3 4.9 5.4 4.7 4.7
##   [91] 4.6 3.0 4.0 6.7 5.6 3.4 4.0 4.4 3.8 5.4 4.0 3.8 5.0 3.8 5.3 5.3 4.6 4.1
##  [109] 2.7 4.8 2.6 3.0 3.8 4.1 4.7 5.3 3.9 6.8 4.4 5.1 5.1 5.2 6.0 5.6 5.7 6.1
##  [127] 5.3 5.1 3.0 5.7 4.0 4.4 2.6 4.4 3.4 3.9 4.1 3.9 3.8 4.0 3.7 4.9 5.2 4.6
##  [145] 5.8 5.2 5.4 4.1 4.3 6.4 3.9 5.9 5.4 4.0 3.2 5.1 3.7 5.6 6.0 4.4 6.1 4.2
##  [163] 5.1 4.6 4.7 3.7 4.9 4.2 3.8 3.9 5.4 3.8 4.8 5.4 6.1 5.9 3.0 5.8 3.2 3.4
##  [181] 4.0 4.2 4.5 4.8 4.4 3.1 4.5 5.4 4.8 3.7 3.1 2.6 5.1 3.4 3.2 3.3 5.2 4.2
##  [199] 4.9 4.3 4.1 7.1 4.4 4.7 5.3 4.8 4.4 5.0 4.6 3.5 3.6 4.8 3.8 3.2 3.8 5.1
##  [217] 5.1 4.2 3.4 3.5 4.2 4.2 4.4 5.0 4.4 4.2 2.6 4.6 5.0 4.5 4.8 4.6 4.1 4.4
##  [235] 4.3 4.8 4.7 5.4 4.1 5.1 5.6 4.6 3.6 3.3 2.6 4.4 3.6 3.9 5.0 5.3 4.0 3.7
##  [253] 4.8 4.2 4.9 5.7 4.6 6.0 3.6 3.2 4.4 4.4 4.1 3.3 4.0 4.1 6.9 5.3 4.8 3.9
##  [271] 4.3 3.8 4.9 4.4 3.8 5.4 4.7 3.7 5.0 4.9 4.4 3.4 4.8 3.9 5.3 4.3 3.6 5.4
##  [289] 5.0 5.6 4.0 5.2 4.5 2.1 5.7 3.5 5.7 2.4 5.8 4.3 3.9 3.3 6.2 4.3 5.0 4.6
##  [307] 3.9 3.5 3.3 3.9 3.0 4.2 4.5 4.9 4.5 5.5 3.3 4.2 3.2 5.2 4.1 3.3 4.3 4.8
##  [325] 2.8 4.1 5.8 5.6 3.8 4.3 5.0 5.9 5.0 5.8 3.7 3.5 4.4 3.0 5.8 5.3 3.9 4.5
##  [343] 5.1 5.1 4.9 4.1 3.8 4.1 5.2 4.8 5.0 3.6 4.8 3.7 5.8 5.9 2.4 4.8 3.6 4.5
##  [361] 4.6 3.6 3.5 4.3 5.1 6.1 4.6 4.4 4.2 2.8 6.0 4.0 2.6 4.4 3.9 4.7 5.0 4.7
##  [379] 5.7 3.4 4.7 4.7 5.3 4.0 5.2 4.4 4.8 3.9 3.9 4.1 4.3 4.8 5.0 4.3 4.4 4.3
##  [397] 4.3 3.9 5.2 4.5 5.1 2.4 3.8 6.2 4.4 6.3 4.1 5.0 4.7 2.8 4.7 4.9 3.4 5.4
##  [415] 5.2 5.1 5.9 4.2 5.2 5.0 4.4 4.3 3.8 5.6 4.3 3.8 6.2 4.3 4.9 4.8 4.9 4.1
##  [433] 4.8 5.9 4.7 3.2 4.4 4.5 5.8 6.0 3.6 4.3 2.3 2.4 5.9 5.5 4.4 3.4 5.4 5.1
##  [451] 6.2 5.4 4.5 2.2 5.2 4.1 4.9 4.4 4.1 5.4 4.1 6.2 2.9 4.4 5.5 4.2 3.9 4.5
##  [469] 4.3 4.9 3.4 4.5 4.5 3.4 4.2 4.2 2.9 4.3 4.7 4.9 5.0 3.8 3.6 4.6 5.0 3.9
##  [487] 6.5 4.9 6.2 4.7 4.9 4.7 2.8 3.1 5.2 5.3 5.3 6.3 5.8 4.7 3.7 4.6 4.4 4.0
##  [505] 7.0 3.5 5.9 3.1 6.5 5.1 3.9 5.9 3.2 3.5 3.9 3.8 4.8 6.5 5.0 4.4 5.5 4.8
##  [523] 5.9 5.1 3.8 5.4 4.7 5.5 4.9 4.4 5.3 4.3 3.3 5.2 5.1 4.6 4.5 5.8 5.0 5.3
##  [541] 5.2 4.9 5.5 3.3 4.5 4.2 2.7 4.1 4.5 4.1 4.0 5.1 5.1 5.3 4.0 5.3 4.3 5.2
##  [559] 6.7 5.4 3.7 4.5 5.0 3.6 5.8 2.9 3.7 4.4 4.4 4.3 4.2 5.4 3.9 4.4 3.2 5.4
##  [577] 4.5 3.9 3.8 3.8 5.3 4.1 4.1 4.3 5.9 4.9 4.8 4.4 5.8 4.6 5.4 3.4 4.0 5.7
##  [595] 5.2 5.4 4.7 4.0 4.6 3.6 2.9 5.0 4.1 5.3 4.1 4.5 4.1 3.4 4.1 5.5 3.7 2.7
##  [613] 4.9 3.9 6.1 4.9 5.1 6.0 3.3 4.0 5.1 5.2 3.9 4.1 3.7 5.0 4.2 5.8 2.8 4.7
##  [631] 4.4 5.5 5.4 5.1 4.4 3.8 4.8 5.1 4.2 5.4 3.7 5.4 4.3 5.0 3.8 4.0 4.7 3.5
##  [649] 5.0 4.4 5.1 5.0 5.0 3.6 6.4 4.8 3.9 5.5 4.1 3.0 3.0 6.5 4.4 5.5 3.0 5.5
##  [667] 4.6 3.9 3.2 5.2 4.0 5.5 5.5 3.4 4.0 3.2 5.7 4.7 4.6 4.0 5.0 5.2 6.0 4.6
##  [685] 4.8 3.1 3.3 4.3 4.1 4.0 2.6 6.0 6.4 5.5 4.0 4.4 4.3 4.3 4.7 3.3 2.7 3.3
##  [703] 5.9 5.3 5.3 5.3 5.5 5.7 4.2 2.6 5.8 6.6 4.7 4.1 5.1 3.6 4.0 4.0 3.8 5.7
##  [721] 3.9 4.2 4.7 3.3 3.6 7.4 4.0 5.6 4.8 3.0 4.6 4.1 5.5 4.1 3.9 4.3 5.9 5.0
##  [739] 3.8 3.8 6.1 4.4 5.0 3.9 4.3 5.9 4.5 5.4 3.7 5.2 5.2 4.4 4.3 2.2 4.2 7.7
##  [757] 5.3 4.3 4.5 3.7 5.7 3.3 5.0 4.6 5.0 5.2 5.6 5.2 2.8 5.0 4.9 5.0 2.3 3.8
##  [775] 4.3 4.8 5.2 4.3 5.4 3.6 4.1 5.3 5.6 4.2 4.6 5.3 5.3 4.1 3.9 4.8 3.5 2.5
##  [793] 4.4 3.8 4.7 4.3 4.0 3.9 5.3 5.6 4.5 5.8 3.0 5.5 5.0 5.9 1.9 3.6 3.3 5.4
##  [811] 6.0 4.4 4.2 3.1 3.4 3.8 4.6 5.5 4.5 6.1 5.9 3.4 4.6 4.8 4.9 4.3 4.1 5.0
##  [829] 5.2 5.2 4.5 3.9 4.8 3.0 4.8 3.1 4.1 3.8 5.3 4.4 3.4 4.7 3.5 6.0 5.1 4.7
##  [847] 4.7 4.9 6.0 3.7 6.5 4.1 4.9 5.4 3.5 4.9 3.4 6.0 3.7 4.7 4.4 4.7 4.1 3.7
##  [865] 2.7 2.4 4.1 4.0 6.4 3.9 3.3 3.8 4.6 5.5 3.2 4.5 4.7 5.4 5.2 6.4 4.7 4.4
##  [883] 4.9 4.5 5.2 5.3 5.3 3.8 4.3 3.6 4.5 4.1 4.3 4.3 4.0 5.4 3.5 4.2 4.7 3.7
##  [901] 3.2 4.4 4.4 3.4 6.2 3.6 3.2 4.7 3.1 5.7 4.2 4.6 5.8 3.5 5.0 4.7 5.4 4.2
##  [919] 6.4 4.4 3.4 4.5 3.4 4.9 4.2 3.8 2.9 6.3 5.2 3.7 4.4 6.0 5.4 4.8 4.4 4.9
##  [937] 3.9 3.9 4.3 4.5 3.0 5.5 5.5 2.8 5.4 4.8 4.7 5.4 2.9 4.8 4.7 4.7 5.4 4.5
##  [955] 5.3 5.9 3.4 5.9 4.3 4.8 3.9 4.0 4.8 5.5 4.4 3.8 4.6 5.7 4.6 4.0 5.6 4.6
##  [973] 6.0 4.7 3.4 5.9 5.0 3.5 5.1 4.6 4.5 4.3 5.5 2.6 5.5 3.8 5.7 4.7 5.9 4.5
##  [991] 4.0 6.1 5.9 4.2 4.5 6.0 4.1 5.3 7.0 5.2
## 
## $func.thetastar
## [1] 0.022
## 
## $jack.boot.val
##  [1]  0.48130081  0.43098592  0.33160920  0.17833333  0.05727273 -0.02719033
##  [7] -0.20416667 -0.23626062 -0.33596491 -0.50273224
## 
## $jack.boot.se
## [1] 0.9596728
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
##    [1] 5.9 3.6 4.6 4.6 4.7 3.2 4.0 4.9 4.6 4.8 5.8 4.1 5.1 4.7 4.7 4.5 4.1 4.0
##   [19] 4.4 5.1 2.5 5.4 3.6 5.0 4.5 4.8 5.4 2.9 3.3 3.2 3.9 4.0 3.7 3.7 3.8 5.2
##   [37] 3.0 2.8 4.0 6.4 5.8 4.9 3.9 6.4 6.7 2.9 5.5 4.3 4.3 6.0 5.2 2.9 5.2 4.5
##   [55] 3.9 3.6 5.9 5.0 4.8 4.8 4.5 4.7 6.1 4.5 5.2 4.5 5.4 4.5 4.1 4.3 5.5 3.5
##   [73] 3.2 4.8 3.5 4.8 4.2 3.4 3.8 4.4 5.6 4.3 4.7 5.1 5.2 4.8 4.8 3.9 3.2 4.0
##   [91] 5.2 3.2 3.3 5.5 6.1 4.5 3.7 4.8 5.1 4.2 3.5 5.0 4.0 4.3 4.3 5.2 3.1 3.6
##  [109] 4.9 5.2 4.5 3.6 4.2 5.2 4.3 4.0 2.7 4.9 4.5 5.5 3.9 5.0 3.5 4.9 5.5 4.2
##  [127] 5.8 3.6 4.0 6.0 5.8 4.0 4.7 2.1 5.1 3.2 6.1 4.5 5.2 4.8 4.1 4.6 5.0 6.4
##  [145] 4.5 3.7 4.3 4.0 4.6 5.6 5.2 5.0 5.5 4.2 4.7 4.1 4.8 4.8 4.6 4.9 4.0 4.6
##  [163] 6.2 5.4 3.5 4.0 5.0 4.5 4.2 4.3 5.0 5.7 4.8 7.0 5.0 4.2 3.5 4.0 3.4 5.0
##  [181] 5.1 3.9 2.0 5.0 5.6 4.3 3.1 4.6 4.8 3.8 4.9 4.4 5.9 5.3 4.8 4.5 4.1 3.1
##  [199] 2.7 4.9 5.0 4.6 5.4 5.4 2.9 5.7 3.4 5.1 4.5 5.2 4.8 3.8 3.9 3.4 4.6 5.9
##  [217] 3.5 5.6 5.3 4.6 5.4 4.3 4.8 4.9 4.6 5.0 3.9 5.1 2.7 2.7 2.9 3.7 4.0 4.1
##  [235] 2.9 4.5 4.3 4.7 3.9 3.1 5.1 4.9 5.5 4.0 4.2 4.9 3.2 4.9 3.0 3.8 5.5 3.3
##  [253] 4.2 4.3 4.0 5.3 4.8 6.2 4.7 3.5 4.0 5.6 3.4 6.6 3.6 4.7 3.0 3.9 2.8 5.3
##  [271] 3.3 6.0 4.5 3.5 5.4 5.5 3.2 5.3 4.2 4.0 5.2 3.5 1.8 3.9 4.8 4.7 5.0 4.9
##  [289] 5.5 5.3 4.8 4.2 5.6 4.9 5.7 4.9 6.4 4.7 4.2 5.2 4.6 3.1 4.6 4.5 5.9 6.2
##  [307] 3.3 3.8 4.1 4.5 4.7 3.6 3.5 5.1 4.9 3.5 4.4 4.5 5.0 4.2 4.7 4.5 5.3 4.2
##  [325] 4.2 3.7 5.6 3.8 4.3 5.1 4.8 3.8 4.1 2.5 4.3 3.6 3.0 1.8 4.6 4.4 5.6 5.2
##  [343] 3.5 4.2 5.3 3.9 5.0 5.7 4.1 5.1 3.8 4.5 3.4 4.6 3.6 4.2 5.9 4.1 4.5 4.9
##  [361] 3.8 4.7 4.9 4.5 3.5 4.9 5.5 3.2 5.6 3.0 4.6 4.0 4.8 4.4 2.6 5.2 6.1 4.1
##  [379] 5.2 3.1 5.3 3.3 4.0 5.0 5.4 3.4 5.3 5.2 4.0 3.2 4.4 5.1 4.8 5.2 3.8 4.6
##  [397] 5.3 5.9 5.5 4.7 5.0 4.0 5.1 4.7 4.6 4.0 4.0 4.5 2.7 3.1 5.6 4.7 5.0 3.7
##  [415] 5.1 4.7 4.0 4.2 4.8 6.7 4.1 2.8 4.4 4.0 4.9 4.0 5.0 3.8 3.4 3.5 4.3 5.4
##  [433] 5.1 4.9 4.3 4.0 3.8 4.5 6.4 3.7 3.8 5.0 6.6 5.1 4.7 4.3 5.2 3.7 4.6 3.4
##  [451] 2.8 3.9 5.4 5.0 4.4 6.2 3.1 4.3 5.0 3.1 4.6 5.1 3.8 3.6 5.1 3.7 3.0 5.1
##  [469] 6.5 5.9 5.5 4.4 3.7 4.4 3.7 4.8 4.4 3.2 4.2 5.1 4.1 4.6 4.2 3.9 6.0 4.6
##  [487] 2.8 5.3 4.9 5.4 3.7 4.7 4.1 5.4 5.7 5.9 4.9 3.7 6.6 4.7 4.4 4.4 4.9 4.9
##  [505] 4.9 4.0 5.1 3.5 5.7 5.3 4.7 4.3 4.0 4.4 4.6 5.1 3.6 5.5 2.8 4.8 4.9 5.4
##  [523] 4.6 3.8 2.4 3.6 5.3 4.5 5.1 4.4 4.8 4.3 6.1 6.0 5.2 4.7 5.3 2.9 4.3 3.8
##  [541] 3.3 6.2 3.9 5.2 5.3 4.8 4.5 3.6 4.0 4.0 4.9 5.4 4.1 4.2 4.3 5.2 4.7 4.8
##  [559] 4.6 4.5 5.9 2.8 5.5 4.5 4.8 5.1 4.9 4.5 4.2 5.6 5.0 3.6 5.3 4.6 3.8 3.4
##  [577] 5.1 3.2 4.4 5.4 4.2 3.0 3.2 4.8 5.1 4.6 4.1 4.6 4.3 3.3 4.1 3.2 3.4 3.0
##  [595] 3.3 4.0 3.4 4.1 4.7 3.7 3.9 4.7 5.5 4.1 4.9 5.0 4.7 4.5 4.0 4.5 5.7 4.7
##  [613] 3.0 3.5 4.2 3.8 5.1 4.1 5.0 2.9 6.3 4.3 3.8 3.1 3.7 3.8 4.5 3.9 4.7 3.9
##  [631] 4.9 6.1 3.4 3.7 5.1 4.6 5.9 4.4 4.5 4.2 3.8 4.3 6.6 4.4 5.2 5.5 4.9 3.5
##  [649] 4.5 4.9 4.8 4.1 5.4 5.9 4.2 4.9 5.1 4.2 3.4 6.7 5.3 4.8 3.1 4.6 5.1 5.4
##  [667] 4.7 4.8 4.4 3.5 4.7 6.2 2.9 5.6 6.1 3.9 4.2 4.5 4.7 4.4 5.7 5.2 3.0 2.9
##  [685] 3.8 4.6 2.3 4.3 4.4 4.2 4.6 5.0 3.3 2.5 3.8 3.7 4.5 5.3 4.3 3.8 3.9 4.5
##  [703] 5.6 4.1 4.0 4.4 4.7 2.6 4.1 3.6 6.3 4.4 4.2 5.8 4.5 5.0 4.4 3.2 2.7 3.7
##  [721] 5.1 6.9 2.8 3.9 4.9 4.4 3.9 4.6 4.1 4.0 4.2 3.5 6.2 4.9 4.1 4.8 4.1 5.2
##  [739] 5.6 3.7 4.2 4.2 3.8 4.8 3.2 3.9 5.5 3.2 5.2 4.8 5.1 5.1 4.9 3.8 4.5 3.8
##  [757] 4.5 5.0 5.0 6.2 4.5 3.9 4.2 2.9 3.8 5.4 3.9 4.0 5.7 5.1 3.8 4.2 3.5 6.1
##  [775] 3.1 5.4 5.0 5.1 4.2 1.4 5.6 5.6 5.3 5.6 4.4 4.8 3.2 5.4 4.1 4.4 3.2 5.9
##  [793] 4.4 4.9 3.8 5.1 5.3 5.2 3.5 3.6 4.1 4.2 4.0 3.9 4.9 5.7 5.2 4.5 2.7 3.3
##  [811] 4.6 3.3 5.1 3.6 3.6 3.1 5.1 4.6 3.6 5.5 3.1 5.1 6.2 4.8 4.9 4.6 5.1 5.4
##  [829] 3.7 3.7 4.0 5.5 3.3 4.3 5.2 6.9 5.3 4.8 4.9 3.8 6.1 5.0 5.3 6.8 5.0 4.0
##  [847] 4.7 4.0 3.4 5.0 5.9 3.8 3.5 6.0 3.8 2.8 4.7 5.4 3.0 3.5 3.4 5.5 4.7 4.9
##  [865] 4.1 5.8 4.4 5.6 4.9 4.3 3.9 4.2 3.6 4.5 2.5 4.1 5.2 5.5 4.3 3.1 4.2 3.9
##  [883] 5.2 5.1 3.9 3.9 4.0 4.1 2.4 4.4 5.8 4.1 4.6 2.5 2.9 5.4 2.4 4.7 6.5 6.0
##  [901] 3.8 4.2 2.6 4.8 3.9 4.3 3.5 3.5 3.6 4.2 5.3 2.9 3.6 4.5 4.9 4.7 5.3 4.0
##  [919] 3.1 4.7 6.6 3.8 4.3 5.4 3.6 5.3 4.0 5.6 4.9 5.9 4.1 4.7 4.8 4.6 4.5 5.3
##  [937] 3.7 5.2 5.0 3.7 4.0 3.3 4.8 3.8 3.9 4.7 4.0 5.0 4.2 4.8 3.5 4.4 5.2 2.9
##  [955] 4.6 5.4 5.5 3.4 4.1 4.6 4.1 4.5 4.7 6.0 4.0 4.8 4.7 3.3 4.0 3.6 4.9 4.4
##  [973] 5.4 5.2 4.2 3.6 5.3 5.3 5.2 4.4 4.0 5.7 3.7 2.4 3.5 3.7 3.3 3.9 5.0 4.9
##  [991] 4.8 5.6 4.6 4.7 5.4 4.2 3.7 4.4 4.8 3.6
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.300 5.200 5.200 5.100 5.100 5.000 4.800 4.800 4.700 4.464
## 
## $jack.boot.se
## [1] 0.756575
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
## [1] 0.3681797
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
##    5.307019   10.487620 
##  ( 2.302643) ( 4.772736)
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
## [1] 1.2751407 0.2165341 0.4805254 0.6770296 0.5317949 0.6259320
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
##    [1]  0.7823865328  0.3592973668  0.1076926288  0.8080665129 -0.6533805627
##    [6]  1.2487985670 -0.0196564980  0.6943916658 -0.4021309856  0.6923956496
##   [11] -0.3627768097 -0.0277966208  0.3854508952  0.8340287972  0.3628629983
##   [16]  0.7613557107  0.3481967928  0.7600423387  0.8195960745  0.6933358526
##   [21]  0.0102019493 -0.3673817551  1.3654015911 -0.3509619352  0.0110542112
##   [26]  0.0929402989 -0.4056702693  0.8346219123  1.2995010988  0.0279879019
##   [31] -0.0108043336  0.9699718031  0.2966918617  1.3661826441 -0.0544071851
##   [36] -0.3847588125  0.3516554877  2.2378752135  0.3484211372  0.8516406135
##   [41]  0.3251974325  0.4493200864 -0.0804233998 -0.2979633324  0.0736288734
##   [46] -0.0838280649  0.4236903184  1.3461634373  0.2856359634  0.2853779917
##   [51] -0.3682182399 -0.0824774679  1.3709514862  0.3564155567 -0.4354762036
##   [56]  0.0137522566  1.2693702579  0.4033471893  0.7454097624 -0.1033571019
##   [61]  0.7118899408  0.3889997065 -0.3435231799  0.3811339614  0.3991932095
##   [66] -0.6343624601 -0.0567138869  0.3017677447  0.4074630684  0.7212261482
##   [71]  0.3082360649  0.0113053281 -0.5252428057 -0.0515012359  0.7643531591
##   [76]  0.3564902457  0.0324113480  0.7386532879  1.9470937839  0.8272362771
##   [81]  0.6861027340 -0.0104985693  1.2041147054 -0.4674998220 -0.3432549509
##   [86]  0.4416687747 -0.3604988423 -0.1228062646 -0.0679622122  0.8743047039
##   [91] -0.7441046870  0.0641086294  0.0177863127  0.0239297288  0.6697122452
##   [96]  0.0322011193  0.3252080473  0.8195103392  0.6581283856  0.3835131235
##  [101] -0.0823081177  1.0035577910  0.0936352878 -0.3950047737  0.4990112032
##  [106]  2.2231830048  0.3904691571  0.3858188155  0.2925566190 -0.7654821431
##  [111]  0.7602156815  0.8048525425 -1.3725084035 -0.4211166894  0.3697534618
##  [116]  0.3189373155  0.2855892395  0.8197626745  0.7709141127  1.4615719791
##  [121]  0.3730641653  0.7192196836 -0.0736318375  0.4129955356  0.7292024658
##  [126]  0.3797200226  0.7920768758 -1.3012258780 -0.3875733940  0.4944394488
##  [131]  0.3917345069  0.7862936939  0.7314702006  0.0334602795 -0.1180605963
##  [136]  0.7491782140  0.6567054515  0.4032856740  0.0269914553  0.4907767076
##  [141]  1.3285635632  0.7292251276  1.2544043248  0.3744589820  2.3028092751
##  [146]  0.6720157297  0.1253858726 -0.8240628496 -0.3292648593  0.7699127646
##  [151]  0.4174890842 -0.3191538632  0.0191516926  1.3081683783  0.7945835148
##  [156]  0.1511832739  0.0119061701  0.8371987064 -0.3421210665  1.4365348661
##  [161]  0.7000004882  0.7900811813  0.7587324711 -0.0604692482  2.0573181314
##  [166]  0.7729701993  0.1481455871  0.7664542981 -0.3530337385 -0.0123703937
##  [171]  0.7914518732  0.8021060389  0.4470222343 -0.0660967159 -0.3142468947
##  [176]  0.3871359490  0.7735954639 -0.2916395363 -0.8910887628  0.0258495763
##  [181]  0.3724238531  0.3897361673  1.4372607908  0.4190612347  0.7567363490
##  [186]  0.9432222593  0.2867683805  0.7370239639 -0.0277452599  0.9421233830
##  [191]  0.0054691452  0.8256971221  1.2194612725  0.7535857525  0.7526085334
##  [196]  0.3009700970  1.4733303529  0.3373882901  0.8600113336  0.0299877320
##  [201] -0.3035795978  0.3037873895  0.4184998529  0.4155532498 -0.8628551874
##  [206] -0.3259591338 -0.2810789743  0.7337661963  0.7704265766 -0.2635019180
##  [211] -0.3337814842  1.3415341890  0.7782661146  0.7667627106  0.4727857792
##  [216] -0.0444588452 -0.3490929003  0.0697431457  0.8291054941  0.4167620852
##  [221]  0.7292347617  1.2256549777  1.2933661194  0.3679667804  0.4659862188
##  [226] -0.0198626282 -0.0502291099 -0.8134852603  0.3441768827  1.3396888927
##  [231] -0.0881541631  2.1990508755  0.3912139878  0.3558788550  2.0116638032
##  [236] -0.0504298285 -0.0246422693 -0.2275960352  1.3252833846  0.4710391839
##  [241]  0.3967243562  0.3807577144  0.3814227870  2.2274111643  1.2712834906
##  [246] -0.0281627225  0.8352689133  0.3298305624  0.1482486501 -1.1776220946
##  [251]  1.2220874521  1.2708749246 -0.4289615587  0.3210287669 -0.4917585593
##  [256]  0.8456219947 -0.3515999457  0.3608339351  1.3125601461 -0.3655623284
##  [261] -1.2346827049 -0.0308305528  0.3209632318 -0.0297667220 -0.3071109967
##  [266]  0.0750010371 -0.0410123872  0.8403633738  0.8369832544  1.1918104424
##  [271]  0.3702416182 -0.0133359064  0.7470648250 -0.7714164766 -0.3567495360
##  [276]  1.1522738747 -0.3424382827  1.3686208773  1.3941104271  0.0093079969
##  [281]  0.7408511585  0.8628158571  0.7638839781  0.9534710636  1.2892984784
##  [286]  1.3442297999 -0.0458995668 -0.4320608993  0.8103359826  0.8113223862
##  [291]  0.1066446421 -0.2685263825 -0.0825010902 -0.6927110266 -0.3041806735
##  [296]  1.1475974140  0.7022596957 -0.4683233618 -0.0041632420 -1.2294986047
##  [301]  0.8201504319  1.1704245748  1.3519818172  1.4314024458  1.3262924501
##  [306]  0.0522349243 -0.2402537999  0.2730073274  1.1956057072 -0.0134930070
##  [311] -0.0815006884  0.7172143683  0.7798208679 -0.4674663612  0.0094791066
##  [316] -0.0502570567  0.3310987155 -0.4348330958 -0.0645324358  0.3144738313
##  [321]  0.6854021911  1.2480483990  0.0156268192  2.1945025031  0.2898777922
##  [326]  0.3620644649 -0.3609863921  0.7468185006  0.0329149379  0.6821762265
##  [331]  0.9440645406  0.3488205004 -0.0494427470  1.2943291399  0.3333996385
##  [336]  1.4829566961  0.2586667548 -0.0206304353  0.8492462205  1.3573761560
##  [341]  0.6498820379  2.2639315952  0.0335098707 -0.0741958430  0.7377872881
##  [346]  0.4320736763  0.2831461851  0.7610012905  1.4406599576  0.3259353972
##  [351]  0.8888889388  0.3927841086  0.3579783524  0.7083703565  0.0059344513
##  [356]  2.0790059459  0.8637023403  1.2903532498  0.3249085074 -0.7916123713
##  [361] -0.0671868887  0.3478713892  1.2963439822  1.8817542541  0.3708588811
##  [366]  0.4181390282  1.2932451040  0.3373392104  0.7424685851  0.6520840403
##  [371]  0.3933794516 -0.8183912736 -0.4293692612 -0.3070749283  0.0175666085
##  [376]  2.2176794004  0.0116148101  1.1400568895  1.2272246834 -1.2381080575
##  [381]  0.7625860120  2.1324133815  0.0793505593  0.4743966222  0.0547740492
##  [386]  0.6895342749  1.3138259541  0.3916330547  0.3749002664  1.3408191698
##  [391]  1.2747328360 -1.4065270135  0.2913895307  0.4409592932  0.0075676656
##  [396] -0.7305428426  1.1479847115  0.7417076301  0.3471173668 -0.0311944691
##  [401]  0.7415300213 -0.3783945366  2.2383548782 -0.3904080460  0.8595853920
##  [406]  0.0240585092  1.2933661194  1.6057147480  1.1880508972  1.5852775039
##  [411]  0.6944786056  0.8218869123 -0.3670223482  1.9525066074  0.0049323726
##  [416] -0.4860490538  0.8418941656  0.4340819080  0.3742001016 -0.0375162587
##  [421]  1.2086773442  2.0740147848  0.7293518844  0.0012006771  0.3629089183
##  [426]  0.3265505994  0.7691062185  1.3339345059  0.4779018564  0.3080378876
##  [431]  1.2152738745  0.0753325582  0.4422041903  0.6220290258  0.7827634595
##  [436]  0.7823853177 -0.6869930751 -0.4506156113  0.3136121701  0.8643121208
##  [441]  0.7581486742  0.3888308321  0.4338973399  0.1422493162  0.2792304063
##  [446]  0.0069089649  0.8582458479  0.7496201823 -0.3211360278  0.7244895756
##  [451] -0.0754024260  0.8538658415  0.3671209525  0.8712131602 -0.0557231528
##  [456]  0.6704847195  1.2970223700  0.8042919579 -0.8762087952 -0.4035861470
##  [461]  0.8051138379  0.3738134567  0.4653870428  0.7427511217 -0.7958273643
##  [466]  0.7929142990  1.3202549279  0.8118747734  0.3969171164  0.3688974940
##  [471] -0.2842269822  0.3849427842  0.9466009727 -0.3703629901 -0.0536795257
##  [476]  0.7954693672  1.2821629994  1.1648077722  0.3578655623  0.3473382989
##  [481] -0.0560316340  0.4809768854  0.0234047308  0.3481339411  0.1078735235
##  [486]  0.7482263503  0.1563592838  0.3393924030 -0.8046522143 -0.0476605143
##  [491]  0.9568681012  0.0186745937  0.7484246352  0.4000436743  1.4090051423
##  [496]  0.4328800408  0.7763070917  1.2400828812  0.3431159260  0.0014271858
##  [501]  0.7562768839  1.2813847509  0.0958758620 -0.8252539597 -0.4559904982
##  [506] -0.0018913786  0.3850119043  0.0034619002  0.7770924886  0.7045918953
##  [511]  0.7888796588  0.7799854812  0.3176612461  0.4172134650  0.7317722207
##  [516]  0.8121735782  0.8468859688  0.4341752561  0.3348994451 -0.4307097799
##  [521]  1.3468342791  0.0455096861 -0.3528435315  0.3728996349  1.3499840469
##  [526]  0.0163335034  0.3424820547 -0.3106732819  0.8960226537  0.3998160378
##  [531] -0.3413591137  0.7040868026  0.9263534108  0.7403676935 -0.0488743234
##  [536] -0.0890923125  0.8072178953  0.7597465875  1.2228826144  1.3600983367
##  [541] -0.3162817931 -0.0738739077  0.6964967571  0.8319830473  0.3366959156
##  [546]  0.9413706671 -0.0048689790 -0.3508449364  0.7918889868 -0.3235217854
##  [551]  0.7800893600  0.3856837459 -0.3866068565  0.2891215162 -0.0394323268
##  [556] -0.4281280722 -0.2890227979  0.7653542074 -0.0168969830 -0.0439133672
##  [561]  0.3892226816 -0.7752740244  0.4285043432  0.7489046093  0.0598982920
##  [566]  0.4341032957  0.6990593880  0.6980936855  0.7327348621  0.3612763975
##  [571]  0.4880585955  0.9532276391  0.4036700030 -0.0179958299  1.3345496498
##  [576]  0.7748444587  0.7348582078  0.0126486484 -0.3032597785  0.3495197603
##  [581]  1.1944721228  1.2614720256  0.4387189172  1.1987580397 -0.0258684931
##  [586]  0.7910073280  0.8362745035  0.2905436841  0.0578620493  0.2772555991
##  [591]  0.4611190611  0.3521137024  0.7347175627  0.3293093119  0.8183002044
##  [596]  0.3391030263  0.3953060472  0.3272462838  0.8106112399  1.2169127077
##  [601]  0.4910734353 -0.1130473737  0.7377301744 -0.0434580985 -0.0568475801
##  [606] -0.0008846693  0.7776518780 -0.5000722655  0.7202327870  0.3115333955
##  [611] -0.7951160474 -0.0387635957  0.8569659293  0.4067786780 -0.0673464394
##  [616]  0.8106007909  2.1676105446 -0.1176409533  0.0036618170  0.0100984782
##  [621] -0.3858443196  0.3064653745 -0.7706771332 -0.2237797975  1.7397695002
##  [626] -0.0666288747  0.7907005899 -0.4530572559 -0.8964355661 -0.3502575122
##  [631]  0.3385028440  0.3352471984  0.7696657988  0.6543245693 -0.0231533043
##  [636]  0.3048223794  0.9641932836  0.0777635335  0.7592919876 -0.4726139003
##  [641] -1.3680614425  0.0243180637  0.0073343085  1.2912787963  0.3905201444
##  [646]  0.3392085924  0.0322011193  0.7669042124  2.1173747792  0.4324329574
##  [651]  0.3678160727  0.7928658201  0.7824588870  0.0733938720  0.7924749478
##  [656]  0.2880078419 -0.7249182762  0.4157501624  0.4140130347  0.3983057031
##  [661]  1.2289963289  0.6728074569  0.7483818075  1.4268901505  0.4841995427
##  [666]  0.8315441150 -0.0677500310  0.8021448173  0.3746473267  2.1690855374
##  [671] -0.0539252011  0.4568523339  0.8986943209  0.6437363347  1.2047792008
##  [676]  0.7400539417 -0.0824774679  0.3316135640  0.8223564766  1.3877926682
##  [681]  1.3056075774  0.8661625984  0.0120225618  0.7832013116 -0.4615476738
##  [686] -2.1370828960  0.4563703883  0.4396430515  0.7387387177  1.2092326558
##  [691] -0.4218176429 -0.3633076883  0.7769824258  0.8127564131  1.2124284295
##  [696]  1.3499440974  0.7858218148  1.2336759415 -0.2640987467  1.4012313093
##  [701]  0.3902246807  1.3560916291  0.7606349975  0.3384722108  0.3073918258
##  [706] -0.4025676622  0.9349983675  0.7428812492  0.3837794956  0.7394238561
##  [711]  0.3983057031  0.3688378760  0.8067533150  1.3325037556  0.3395088326
##  [716] -0.3693511796  0.2359478201  1.1120308353  1.1957916092 -0.0526167017
##  [721] -0.4264195984  1.4304381280  1.3457798307  0.3270559156  0.3379218028
##  [726]  0.0670740545  0.3454385787  0.3711049227  0.3516637233 -0.0390389440
##  [731]  0.2406176602  0.8199144984 -0.2582851308  0.7438011865 -0.2940457204
##  [736]  0.7646449019  0.1467902498  0.7271712582  1.8805606397  0.0178748993
##  [741]  0.7393625880 -0.9967240097  0.2966036516  1.9026184602  1.7210158153
##  [746] -0.0339668691  0.6444417791  0.7308770961  0.8063175050  1.1949244964
##  [751]  0.7484935559  0.3792490135  0.8678824761  0.4552205280  0.3703519778
##  [756]  0.8160078970 -0.0184205060  0.7663602407  0.7478084010  1.1986868994
##  [761]  0.0188287470 -0.7903641036  0.0100939267 -0.3620389516 -0.8678895845
##  [766] -0.7308607934  0.7320178071  0.0169098253 -0.3482343919  0.7155770398
##  [771]  0.0276034650  0.3742001016  0.8158902837 -0.2486194038  0.8093113530
##  [776]  0.8029803508  0.3854790717  0.3399554245  0.0066740242  0.3905703095
##  [781] -0.4198115273  1.9166945293  0.7311575568  0.0022592718  0.8047396827
##  [786]  0.3397294198  0.2830375874  0.2734155125  0.0779542157  0.8096012965
##  [791]  0.3705228001  0.8211696123  0.0665687684  0.3563057808  0.3670602133
##  [796]  0.3406989535  2.0248209187  1.2001194486  0.6152126723  0.4290162804
##  [801]  0.7577586760  1.2767264456  0.4078520975  0.0416901687  0.3306047520
##  [806]  0.4139713233  0.3571857881  0.4291872771  1.2506324493  0.6977957521
##  [811]  0.3095160056  0.3404659242  0.3871830852 -0.3257724641  0.8493060304
##  [816]  0.3157306987 -0.3761160922 -0.0562420515 -0.0312602150  0.7800183207
##  [821]  1.4705122002  0.7909116885  0.8450842525  0.6454155149  0.8592731487
##  [826] -0.1432555211 -0.8141450711  0.2479729405 -0.0526167017 -0.0125844117
##  [831]  0.3121772901  0.8643214401  0.7687320260 -0.4672763682  0.7083198458
##  [836] -0.2794133908 -1.3100127875  0.7845608050  0.0881982033  0.2897529435
##  [841] -0.0571815789  0.3708878855  0.8296116911  0.0032814065  1.2029988394
##  [846]  0.3435621410 -0.3668604195  2.0846271164 -0.0341861831  0.3491667936
##  [851]  1.2515506276 -0.3905402080  0.8524027391  0.8550119041  0.0017677194
##  [856]  1.3021798503  1.4340941312  0.8487839843  0.8025362301  0.1097988622
##  [861] -1.2884291772  0.4092184284  0.0709129241  0.7240777393  0.7501388076
##  [866]  0.7562153648  0.8682223109 -0.3972191719 -0.2655101315  0.7613086587
##  [871]  1.9074863909 -0.0753724405  0.7297033189 -0.0255426569  1.1872766587
##  [876]  0.7562153648 -0.0895547510  0.3223119604 -1.4043735151 -0.0329009010
##  [881]  0.7616954540  0.8384272833  0.4044262133  0.4063358273  0.0349140236
##  [886]  1.2197135296  1.4709266619  1.2077710938  0.7941289882  0.7727880664
##  [891]  1.1933142422 -0.4567132835 -0.3602378091 -0.3513894360  1.5637555988
##  [896] -0.0626215155  1.9720829998  0.7323866470 -0.0382145690 -0.3522038890
##  [901]  0.2963452440 -0.0073655039  0.3058592747 -0.3079509135 -0.8102381661
##  [906]  0.3578655623 -0.3151491433 -0.3982191348  0.3637146159  0.9158278970
##  [911] -0.0436212574  0.4493806506  0.8218700913 -0.8619109005 -0.2860866665
##  [916]  0.3163918111  0.3961854607  1.2943691818  0.7199433334  0.8735636827
##  [921]  0.3380456023  0.7396109745  0.4523782142 -0.0787667177  0.8246351812
##  [926] -0.4173342251 -0.0414456496  0.7432010364  0.8083488108  0.7428812492
##  [931] -0.2692964840  0.4100166070  0.7108772099  0.4341234471  0.8576397207
##  [936]  1.8283561077 -0.0576329332  1.2934799778  0.7040491636  0.8056181545
##  [941]  0.7416401719 -0.6877773393  0.3339954403 -0.3249001798  0.3747343896
##  [946]  0.2531080063  1.3457711915  0.8271364939 -0.3063570432  0.8536490169
##  [951]  0.2735273777  1.2890513732  0.7045918953  0.3802479320  0.8141142129
##  [956]  0.4260738236 -1.3621352026  0.4254716247 -0.8378853340  0.7772789754
##  [961]  0.2906588625  0.3681797332 -0.0050229556  0.3856762106 -0.4340022174
##  [966]  1.0442261715  1.3725766938 -0.0489943558  0.3428698607  0.7412153426
##  [971]  0.8508129675  0.3729498000 -0.0217151809  0.2978294506  0.0228935886
##  [976]  0.8273650144  1.3487171187 -0.7669969826  0.0151875942  0.2947970555
##  [981]  0.3341539627  0.8026166087 -0.4064157266  0.0423194324  0.3221991348
##  [986]  1.1802631400  1.2115465073  2.1312329366  1.3176152028  0.3895992090
##  [991] -0.9152293921  0.3035968458  1.0732042865  0.0258914668  0.3292779504
##  [996]  0.1785281444 -0.7860235591  0.7573545225  1.2890716956  0.4498629591
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
##   0.50602876   0.22104467 
##  (0.06990046) (0.04942372)
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
## [1]  0.48536034 -0.51164656  1.25632897 -0.04364021  0.24238275  0.08951524
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
## [1] -0.0036
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9070997
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
## t1*      4.5 -0.03943944   0.8592454
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 6 8 9 
## 3 1 3 2 1
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
## [1] -0.0179
```

```r
se.boot
```

```
## [1] 0.862156
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

