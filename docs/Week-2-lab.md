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
## 1 3 4 5 7 8 
## 1 2 2 1 2 2
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
## [1] 0.005
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
## [1] 2.598371
```

```r
UL.boot
```

```
## [1] 6.411629
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
##    [1] 4.7 5.6 4.4 4.3 6.2 5.2 4.7 7.0 6.4 3.8 6.2 5.6 4.9 4.0 4.0 4.8 4.1 4.1
##   [19] 4.0 4.0 3.6 4.9 4.3 4.4 4.2 3.8 4.3 5.6 4.8 4.0 4.1 3.5 4.4 4.9 5.6 4.4
##   [37] 4.2 2.6 4.8 4.7 5.2 5.2 5.3 2.6 4.4 3.5 3.6 5.3 4.1 4.1 4.1 4.9 4.1 5.4
##   [55] 5.2 3.9 6.1 5.5 4.0 3.6 5.0 4.9 3.3 6.7 5.0 3.0 3.3 4.2 4.3 5.5 3.0 3.7
##   [73] 4.3 3.3 4.6 4.7 4.1 3.4 6.0 4.2 3.6 4.4 4.7 4.4 4.0 3.6 5.9 4.6 3.7 5.0
##   [91] 4.4 5.7 4.1 4.3 5.3 4.1 4.0 5.1 4.6 5.2 5.6 3.8 6.5 4.5 3.5 2.1 4.4 5.9
##  [109] 3.6 4.0 4.9 4.1 3.8 4.1 4.1 3.8 4.7 3.7 6.4 4.8 2.7 4.8 3.4 4.8 5.5 5.0
##  [127] 3.0 3.4 5.1 5.6 4.9 5.1 3.3 6.2 4.8 5.7 3.6 3.3 4.0 4.1 4.3 5.0 4.0 3.4
##  [145] 4.0 5.6 4.3 4.4 4.4 4.5 5.0 2.2 4.5 4.3 4.5 5.4 5.2 4.6 4.7 3.6 5.4 3.8
##  [163] 6.1 4.6 4.7 6.3 3.5 4.6 4.9 5.6 6.1 4.5 2.9 5.6 4.3 2.9 5.7 6.0 4.8 3.8
##  [181] 4.0 5.7 5.6 6.0 6.0 2.8 3.3 4.8 5.3 3.2 5.5 4.3 4.2 4.4 5.6 5.0 5.1 3.8
##  [199] 3.4 5.0 5.1 5.1 3.8 5.2 4.5 6.1 4.4 4.7 4.3 3.9 3.6 4.8 6.3 5.2 2.9 6.0
##  [217] 3.6 4.4 4.3 5.3 4.2 4.6 3.4 4.8 4.3 4.2 2.8 3.7 4.0 3.8 4.1 4.6 3.9 4.2
##  [235] 3.3 3.8 5.3 4.5 4.4 3.1 4.3 5.3 4.2 5.0 4.5 4.0 6.1 4.2 4.4 4.2 3.9 4.1
##  [253] 3.7 6.1 4.4 5.5 3.6 3.9 6.5 5.3 4.9 5.1 3.7 3.4 4.5 5.6 4.2 5.1 5.9 4.9
##  [271] 4.0 3.3 3.1 5.3 5.6 4.0 5.1 5.8 4.7 5.2 2.2 5.4 4.7 5.4 4.1 3.8 4.2 5.1
##  [289] 4.1 6.1 5.4 4.7 4.7 4.7 4.3 4.8 5.4 4.4 4.0 3.1 4.7 4.7 4.2 4.9 5.1 3.7
##  [307] 3.5 2.6 3.4 5.5 4.6 4.7 3.2 5.0 3.8 5.8 5.4 3.4 5.1 4.9 4.1 3.5 5.8 4.7
##  [325] 3.4 4.3 4.7 4.6 3.9 3.1 3.3 5.6 5.3 5.1 4.8 5.0 4.9 3.9 5.1 3.2 3.6 3.3
##  [343] 4.0 4.6 5.0 5.0 3.2 3.8 5.9 3.2 5.0 5.3 4.5 3.9 4.7 5.1 5.4 4.9 4.3 3.5
##  [361] 3.0 4.0 3.7 3.4 4.2 4.5 4.0 5.1 3.7 4.1 5.6 6.3 5.1 3.7 4.8 3.4 4.8 4.2
##  [379] 5.7 6.1 3.3 2.9 7.2 3.9 3.1 3.8 4.5 4.6 5.3 5.5 3.7 3.1 3.5 6.2 6.1 4.8
##  [397] 4.3 5.1 5.3 4.8 5.1 5.1 3.3 4.0 4.8 4.6 4.9 4.8 4.7 4.6 5.1 3.3 5.3 3.5
##  [415] 4.7 5.7 5.0 4.0 2.3 4.9 4.8 4.7 4.0 3.1 3.0 4.7 5.3 5.0 4.2 4.7 5.0 4.7
##  [433] 4.7 5.5 5.0 3.5 4.4 2.7 5.2 2.7 5.1 5.1 4.5 4.1 4.4 4.7 4.8 5.1 4.3 6.2
##  [451] 3.5 5.4 4.3 5.0 4.7 4.5 4.0 4.9 4.0 5.0 3.8 3.5 4.8 4.1 2.9 3.1 5.0 4.1
##  [469] 3.6 6.0 3.8 4.7 4.9 4.7 5.0 4.6 5.7 4.3 4.9 5.6 4.5 4.5 3.8 4.8 4.5 5.4
##  [487] 5.5 4.5 5.7 3.2 4.5 6.2 3.9 3.3 5.4 5.5 5.5 5.2 4.7 4.8 3.6 3.5 4.4 4.5
##  [505] 4.4 4.6 4.2 3.6 4.3 2.7 4.7 5.1 3.9 3.3 3.5 3.1 4.1 5.1 4.0 3.1 5.5 4.2
##  [523] 3.4 5.3 5.4 4.4 4.7 6.3 4.4 4.9 5.0 4.0 4.2 5.2 3.0 4.3 4.4 3.6 2.5 1.6
##  [541] 4.0 3.0 4.9 5.0 4.7 3.3 4.9 5.4 4.3 4.8 4.7 5.7 5.1 4.9 4.2 1.7 4.7 4.8
##  [559] 6.2 3.7 3.0 4.0 4.1 4.6 3.3 5.4 5.0 6.8 3.0 3.4 3.3 3.0 3.6 5.5 6.0 5.8
##  [577] 5.3 6.6 5.2 6.9 4.3 5.9 4.0 3.9 4.1 3.8 5.4 4.2 3.9 4.1 3.3 3.8 3.9 6.1
##  [595] 4.8 4.4 5.5 4.8 5.5 3.5 3.7 4.5 5.2 4.7 4.5 4.9 6.0 2.7 5.3 3.2 4.9 4.3
##  [613] 3.0 4.7 3.8 5.3 3.5 4.3 5.1 4.5 4.2 6.2 5.1 3.3 4.8 5.4 5.1 5.0 4.1 4.3
##  [631] 4.8 4.0 4.0 3.7 3.7 4.2 6.2 2.9 3.8 5.1 3.2 4.6 4.4 4.4 3.3 4.3 4.1 5.0
##  [649] 4.8 4.0 2.6 4.3 3.8 6.5 4.0 5.5 4.5 5.1 5.8 4.6 5.7 4.8 5.2 3.7 2.9 5.9
##  [667] 2.6 4.8 5.2 3.9 3.3 4.2 3.4 5.4 4.5 4.4 5.5 4.6 4.5 4.6 5.7 6.6 5.0 4.7
##  [685] 5.2 4.4 3.7 6.4 5.7 4.8 5.9 6.1 4.9 2.7 5.1 3.5 4.2 5.2 4.8 2.4 3.5 2.7
##  [703] 4.3 3.8 4.4 3.9 3.9 5.2 4.1 5.7 4.0 5.4 4.8 3.6 5.5 3.7 5.0 4.1 4.3 4.4
##  [721] 5.8 4.3 6.3 5.4 4.2 3.8 5.1 2.9 4.0 4.5 5.2 6.2 4.2 4.4 5.3 5.6 2.8 3.9
##  [739] 4.5 5.5 3.3 4.2 5.1 2.9 4.1 5.9 3.3 5.5 4.8 4.7 4.2 3.8 4.5 4.0 3.9 4.6
##  [757] 5.2 4.2 3.7 5.9 4.5 5.2 5.7 5.2 4.5 4.9 6.6 3.5 4.2 4.0 6.1 4.2 6.3 5.4
##  [775] 4.1 4.7 3.6 4.1 4.4 3.9 6.3 3.9 4.0 4.7 4.1 5.0 1.6 3.9 4.6 5.2 3.4 4.9
##  [793] 4.5 4.3 3.8 4.6 4.3 4.8 3.8 3.5 3.5 3.9 4.2 5.7 4.5 4.4 4.3 4.0 4.1 4.3
##  [811] 6.3 4.4 4.5 3.8 3.5 5.5 5.6 5.6 3.5 4.1 4.8 4.6 4.5 5.6 6.3 5.1 4.6 4.2
##  [829] 4.7 4.8 3.9 4.2 6.1 5.2 5.3 3.0 5.4 4.0 3.6 5.4 3.4 4.7 5.4 4.8 5.3 4.4
##  [847] 5.1 4.2 5.5 3.5 4.1 4.3 4.9 6.5 4.5 3.0 4.6 4.0 4.3 5.1 5.5 4.8 4.9 4.7
##  [865] 4.3 4.0 4.7 5.5 2.6 3.7 4.0 4.8 4.0 5.9 6.0 4.9 4.0 4.1 5.8 6.0 3.8 4.4
##  [883] 3.4 3.6 4.8 4.1 6.1 6.0 4.8 3.3 4.0 6.0 6.0 3.4 4.8 4.4 3.9 4.5 4.1 3.4
##  [901] 4.5 5.7 5.8 4.0 6.1 3.9 2.9 3.8 4.2 4.5 4.4 3.5 4.2 4.5 4.8 4.8 5.5 5.2
##  [919] 5.3 4.9 2.3 6.3 3.4 4.2 4.7 4.3 4.8 4.0 5.8 3.1 5.8 4.8 4.5 3.5 4.3 3.3
##  [937] 4.5 4.8 5.2 6.5 3.6 3.5 4.5 4.1 5.0 5.4 5.1 5.2 5.4 3.1 5.4 5.6 3.1 4.0
##  [955] 5.6 6.0 4.8 4.8 4.3 5.2 5.4 5.9 4.5 3.8 5.3 3.3 3.3 3.8 5.6 4.3 3.5 5.6
##  [973] 3.8 3.5 5.0 6.8 5.9 3.5 4.0 3.6 3.5 4.8 4.2 4.1 6.3 5.3 4.1 5.1 4.0 6.3
##  [991] 4.6 3.9 4.4 5.1 4.3 4.6 3.8 4.7 5.8 3.9
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
##    [1] 5.3 5.1 2.2 3.4 4.6 4.8 3.4 3.7 5.6 6.3 3.1 6.4 4.2 4.5 4.7 4.9 6.0 3.5
##   [19] 3.8 3.3 4.9 3.0 3.3 4.9 3.9 3.7 4.3 4.5 4.4 5.7 6.4 3.2 4.7 4.0 5.0 4.9
##   [37] 5.6 4.6 4.7 3.6 5.4 3.4 3.8 4.3 4.8 3.7 5.2 4.4 6.1 2.7 4.9 4.4 4.0 5.2
##   [55] 5.4 4.1 4.6 4.3 3.1 4.4 3.7 4.1 3.1 5.3 4.2 4.3 5.0 5.0 5.1 5.4 4.6 5.3
##   [73] 6.1 5.6 4.6 3.7 4.8 6.0 4.2 4.2 4.5 2.8 3.3 6.0 4.5 4.4 4.5 5.8 5.2 3.0
##   [91] 4.9 4.5 5.1 5.3 2.8 6.5 4.8 4.4 4.9 3.1 4.7 4.4 3.9 4.8 6.0 4.1 3.8 3.8
##  [109] 3.6 4.2 4.2 4.2 5.1 4.9 5.1 6.4 4.6 4.5 4.9 4.1 3.9 4.8 5.2 4.8 5.3 4.1
##  [127] 4.6 5.0 2.1 4.2 3.4 4.2 5.6 5.4 4.7 4.5 4.7 4.0 4.0 4.8 3.5 4.0 4.8 3.3
##  [145] 5.0 3.9 4.8 5.8 3.4 2.2 4.2 5.9 4.2 5.2 4.5 4.7 3.2 4.0 6.2 5.9 5.3 6.6
##  [163] 4.9 4.3 5.0 4.6 5.1 4.0 4.4 4.6 3.7 4.0 4.6 5.5 3.7 3.8 4.9 5.2 4.7 3.9
##  [181] 3.7 5.3 5.9 3.7 4.0 4.2 3.8 4.9 4.2 5.0 5.3 5.0 5.1 4.4 4.6 2.9 3.5 4.9
##  [199] 4.9 5.0 3.3 3.9 5.2 4.9 4.7 4.2 3.2 4.2 4.4 4.7 3.8 4.1 4.6 2.3 4.6 4.4
##  [217] 3.5 5.7 3.2 4.2 4.5 4.3 4.1 4.4 5.5 4.8 4.3 3.6 4.9 4.8 4.6 4.9 3.4 2.5
##  [235] 5.0 3.5 5.4 3.5 4.2 5.6 4.3 4.4 4.9 4.9 5.0 4.6 4.8 3.3 4.1 4.0 4.2 4.6
##  [253] 4.2 4.8 4.8 4.4 2.8 4.6 4.0 4.7 3.0 2.7 5.2 5.2 3.2 2.7 3.7 3.9 5.2 3.6
##  [271] 3.9 3.3 4.7 4.3 2.9 4.9 6.8 4.3 4.4 5.0 5.5 4.0 4.8 4.6 4.1 3.2 3.7 4.5
##  [289] 3.1 4.0 3.4 3.8 4.6 4.9 3.7 4.0 5.4 4.0 4.5 3.7 4.5 6.4 4.2 5.0 5.5 5.3
##  [307] 4.3 4.5 2.4 5.0 4.9 4.1 5.8 4.5 4.0 4.1 4.4 7.3 4.6 4.1 3.4 5.4 4.3 4.7
##  [325] 5.4 5.8 3.2 4.0 4.9 5.7 5.6 3.5 4.4 3.3 4.2 3.6 5.7 4.8 5.2 5.7 3.9 4.7
##  [343] 4.4 4.0 5.6 3.1 4.5 5.7 4.5 2.5 5.3 5.3 3.8 4.3 3.4 4.3 5.2 4.0 4.7 4.6
##  [361] 3.6 3.4 4.6 5.6 6.2 4.5 5.3 4.8 3.5 5.2 6.1 4.6 3.1 4.0 6.0 5.3 4.5 5.3
##  [379] 4.7 3.6 6.2 3.2 4.1 5.3 4.7 4.5 3.4 4.3 4.6 6.7 4.2 4.1 4.3 4.7 4.1 4.4
##  [397] 5.6 5.2 5.1 4.5 5.3 4.0 5.5 5.3 2.4 4.7 4.3 3.7 5.3 5.1 4.2 3.6 5.2 3.9
##  [415] 2.3 5.4 5.5 3.3 4.3 6.0 3.5 4.3 4.7 5.6 5.1 2.7 4.4 5.4 3.1 5.7 5.5 4.2
##  [433] 5.3 5.8 3.9 5.0 4.2 3.1 4.8 4.5 3.9 5.3 4.3 2.6 4.3 3.3 4.3 5.7 6.1 3.8
##  [451] 5.9 3.6 5.2 6.1 4.8 4.2 4.6 3.8 3.5 4.9 6.4 6.4 4.6 3.7 5.2 3.8 5.7 3.5
##  [469] 4.3 3.5 6.4 4.5 5.4 4.0 4.2 5.3 2.8 4.8 2.4 4.0 3.8 4.5 4.7 4.0 4.9 4.7
##  [487] 3.4 4.1 4.9 3.4 4.8 4.7 4.9 5.5 5.2 5.0 3.1 6.2 4.1 5.0 3.0 5.4 2.5 5.1
##  [505] 3.9 4.4 6.2 5.7 4.3 5.6 5.3 3.9 3.7 1.9 3.6 3.9 5.3 3.2 5.0 4.0 5.3 4.3
##  [523] 3.5 4.5 2.4 4.6 4.3 5.1 5.1 4.8 4.5 3.6 4.0 4.9 4.1 4.0 5.8 5.7 6.0 3.5
##  [541] 5.1 4.2 4.8 6.3 4.6 3.5 4.4 4.3 6.0 3.6 4.2 3.5 4.6 3.4 3.2 6.8 5.0 5.1
##  [559] 4.2 4.5 5.3 5.1 5.2 4.6 5.5 4.1 4.3 5.9 3.8 4.2 4.1 5.7 3.7 4.0 5.5 5.1
##  [577] 4.9 4.3 4.5 5.7 4.6 3.1 3.6 6.6 1.7 3.9 3.5 4.9 5.0 5.0 4.4 6.0 4.3 3.3
##  [595] 3.9 4.9 5.7 4.2 3.9 3.8 3.0 3.2 3.9 4.5 3.6 3.9 4.5 4.7 6.1 6.0 3.8 3.8
##  [613] 2.8 5.8 4.0 3.1 4.6 5.2 3.7 5.4 4.5 4.4 5.2 3.4 5.8 4.2 5.8 4.4 3.6 5.4
##  [631] 4.6 3.5 5.6 4.9 5.4 5.1 3.9 4.0 5.1 5.2 4.5 2.9 4.8 5.1 5.3 4.0 4.1 5.2
##  [649] 4.5 5.0 3.7 4.9 3.8 6.1 4.6 2.9 4.1 4.0 3.7 4.7 4.2 5.8 2.3 3.9 3.4 4.9
##  [667] 3.5 3.1 4.2 6.3 3.1 3.1 3.9 4.5 3.3 3.7 5.0 4.6 5.2 3.9 5.6 5.0 6.0 3.5
##  [685] 4.0 5.2 1.8 4.5 4.9 4.0 4.5 4.3 4.2 3.4 4.5 4.1 4.1 4.8 3.9 4.6 4.9 4.6
##  [703] 4.3 3.8 5.1 5.0 3.8 4.8 4.0 4.0 4.6 4.9 6.0 4.5 4.7 6.0 4.4 4.4 3.6 5.7
##  [721] 4.2 4.3 5.5 3.0 5.5 4.3 4.7 6.6 5.1 4.8 4.9 4.4 3.4 4.9 5.0 5.8 3.6 4.9
##  [739] 5.7 3.0 5.4 3.3 4.3 5.2 4.9 4.2 5.1 3.8 4.2 4.0 4.1 3.0 4.7 3.7 3.5 5.0
##  [757] 4.7 6.8 5.1 5.9 3.6 5.4 5.5 3.5 4.9 4.7 4.6 5.3 4.4 6.0 4.5 3.6 4.1 3.0
##  [775] 4.2 3.8 5.5 5.0 3.6 3.7 5.3 5.1 4.0 5.5 4.0 2.3 5.7 4.5 4.4 4.9 4.4 3.6
##  [793] 3.4 6.8 4.5 2.9 4.4 5.4 4.0 3.4 5.4 3.7 4.7 4.9 3.4 2.9 5.4 3.8 5.1 4.7
##  [811] 4.5 2.8 5.4 4.6 5.3 6.0 5.1 5.2 7.7 2.7 4.2 3.5 4.5 2.6 4.6 3.5 4.4 4.4
##  [829] 1.7 2.5 4.7 6.3 5.3 4.6 5.3 5.3 3.7 3.9 4.8 4.3 5.3 3.0 3.6 5.1 5.2 4.9
##  [847] 4.2 3.6 5.7 4.5 5.7 5.1 4.0 3.9 5.6 5.2 3.6 1.6 4.9 4.4 3.8 4.8 5.8 4.5
##  [865] 4.6 3.9 4.8 5.3 4.2 5.3 5.0 4.7 4.8 5.5 3.4 4.9 4.4 4.8 5.3 4.9 5.8 3.6
##  [883] 4.2 5.1 4.4 4.4 4.6 4.4 4.4 3.5 5.3 3.2 5.1 5.2 5.6 4.8 2.0 5.3 5.5 3.4
##  [901] 4.9 4.2 4.4 4.2 4.5 4.3 5.3 4.4 6.6 4.8 3.8 4.1 3.3 4.3 4.5 4.7 3.7 4.4
##  [919] 5.5 5.4 5.1 4.4 3.6 4.8 5.1 4.5 6.3 3.7 3.4 5.4 4.2 5.7 4.2 4.3 5.6 5.0
##  [937] 3.1 3.1 5.0 3.2 4.8 5.4 3.7 4.5 4.8 5.1 3.6 4.3 4.7 4.3 4.3 3.9 4.1 5.4
##  [955] 2.4 4.6 4.1 4.8 4.7 3.4 5.0 4.8 5.0 4.2 5.0 4.5 4.8 4.7 5.9 5.1 3.8 5.2
##  [973] 3.3 4.2 4.7 4.4 5.0 4.7 4.9 6.1 4.8 5.9 3.9 4.2 3.2 5.2 4.5 5.3 6.0 5.8
##  [991] 5.7 2.7 4.4 5.0 4.2 6.3 3.6 3.8 6.2 2.2
## 
## $func.thetastar
## [1] -0.0099
## 
## $jack.boot.val
##  [1]  0.46045845  0.38405797  0.22425150  0.14071856  0.09592391 -0.05869565
##  [7] -0.22271468 -0.23218750 -0.34067797 -0.51440922
## 
## $jack.boot.se
## [1] 0.9129323
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
##    [1] 4.6 4.6 5.6 2.4 6.2 4.5 5.5 3.4 3.9 4.3 4.0 4.3 4.2 4.4 4.8 6.2 5.1 4.6
##   [19] 5.5 4.3 4.0 5.1 3.7 6.0 3.4 5.9 5.6 4.0 4.5 4.7 3.9 4.9 5.4 6.5 5.3 5.2
##   [37] 2.4 3.7 4.5 3.9 5.2 3.7 6.0 4.8 3.2 4.4 3.6 4.5 4.3 3.4 4.0 4.8 3.2 5.1
##   [55] 5.2 3.2 3.7 5.7 4.4 6.3 4.6 3.3 4.2 2.9 6.1 6.6 3.0 4.4 4.4 3.8 6.0 4.7
##   [73] 4.5 4.2 4.1 5.0 3.1 4.6 4.7 5.3 4.4 6.5 3.7 5.3 3.2 4.9 3.7 5.5 3.5 3.0
##   [91] 3.6 4.5 2.4 5.3 4.2 4.5 5.9 5.1 3.9 3.2 5.4 4.9 2.2 4.8 6.5 4.5 5.6 3.4
##  [109] 5.2 5.9 5.0 3.7 4.2 3.3 3.4 5.3 4.1 2.3 6.8 5.5 4.2 5.3 3.0 4.0 6.1 4.7
##  [127] 4.8 4.2 3.4 6.5 4.5 4.0 5.8 5.3 3.5 3.6 4.6 6.3 4.3 5.1 3.4 5.5 4.8 5.7
##  [145] 5.5 4.5 4.5 4.0 4.7 4.2 3.2 5.0 5.7 3.0 5.8 3.1 4.3 4.7 5.4 2.7 4.7 3.4
##  [163] 5.4 6.0 4.6 3.4 5.7 3.5 3.7 5.5 5.4 4.5 4.9 5.9 3.0 5.3 3.7 5.7 5.6 3.8
##  [181] 3.6 4.0 4.5 5.2 4.9 4.6 4.5 4.2 5.1 4.9 3.3 5.1 4.4 4.7 4.8 5.0 3.6 5.2
##  [199] 6.2 2.9 5.6 5.5 2.9 4.7 5.8 4.5 5.3 4.6 3.7 3.0 4.2 4.1 4.2 4.7 4.6 4.4
##  [217] 4.6 5.4 4.2 3.7 4.0 4.0 3.0 4.5 3.8 6.5 3.5 5.0 3.3 5.0 3.7 4.4 3.2 3.9
##  [235] 4.8 4.8 4.5 3.7 4.5 5.6 4.2 4.2 4.1 5.6 3.0 3.3 4.8 5.8 4.1 5.7 5.1 5.8
##  [253] 5.2 3.1 4.6 3.7 3.7 4.9 5.1 5.3 4.6 3.3 6.2 5.1 4.3 4.1 4.6 5.9 4.7 2.3
##  [271] 5.1 4.8 4.9 4.0 3.4 3.9 4.7 4.1 4.0 3.7 4.9 5.0 5.4 2.7 4.8 4.6 4.9 4.0
##  [289] 4.4 3.6 4.4 5.3 4.3 3.7 2.7 4.2 3.3 4.1 2.7 5.8 5.1 4.3 3.3 5.9 2.4 3.1
##  [307] 4.9 2.8 4.9 3.2 4.2 5.8 4.5 6.3 4.7 4.9 4.8 4.2 2.8 5.0 5.4 5.1 6.6 4.2
##  [325] 5.2 4.6 4.6 6.6 4.6 3.7 5.8 3.5 5.7 4.4 4.3 5.8 2.9 6.2 4.5 3.3 5.1 6.5
##  [343] 5.2 4.6 3.8 3.9 3.9 5.0 4.7 4.2 4.5 5.1 3.8 4.1 4.5 5.0 4.4 4.3 5.4 5.3
##  [361] 3.9 3.0 3.8 5.4 4.6 3.9 5.1 5.8 2.9 3.7 4.0 5.1 4.0 4.9 4.2 5.4 3.9 4.1
##  [379] 3.6 5.2 4.0 4.4 4.3 4.8 3.9 4.0 5.5 4.5 4.5 4.2 5.9 5.6 4.5 5.2 5.6 3.6
##  [397] 5.7 5.7 4.0 3.8 3.1 3.8 4.7 6.1 4.0 4.2 4.4 4.3 3.1 4.0 5.5 4.9 6.3 4.6
##  [415] 5.3 4.0 4.0 6.1 3.3 4.7 5.4 4.7 4.0 3.7 5.3 3.8 4.8 4.7 5.4 4.8 4.3 3.2
##  [433] 5.5 5.5 3.4 3.7 4.6 4.3 5.0 4.0 5.1 6.9 4.3 4.3 5.1 6.3 4.3 5.7 4.5 4.1
##  [451] 3.7 4.8 3.5 4.7 4.2 4.1 4.1 3.7 5.0 5.4 5.0 4.1 5.5 4.6 4.2 4.0 4.9 7.1
##  [469] 3.7 4.2 3.8 5.6 6.0 4.1 3.6 5.0 3.3 5.5 3.3 3.5 2.9 5.5 5.2 3.6 4.2 5.0
##  [487] 6.0 3.5 4.5 5.2 3.7 3.4 3.6 5.1 5.4 5.4 4.2 4.3 3.9 5.0 4.0 5.0 4.0 4.4
##  [505] 4.9 3.3 5.8 4.4 4.2 5.1 6.9 5.3 4.4 4.1 5.5 3.9 5.5 4.1 4.5 4.6 5.2 5.5
##  [523] 4.6 4.2 5.1 3.3 2.5 4.7 4.0 3.5 4.8 4.9 4.0 2.9 5.1 4.2 4.6 3.2 3.9 5.0
##  [541] 5.9 4.3 3.4 5.0 5.1 3.5 6.4 4.7 4.8 5.4 4.0 3.7 4.0 2.9 6.0 5.2 3.4 4.9
##  [559] 4.7 3.2 4.9 4.4 5.6 4.1 4.5 4.7 5.7 5.0 6.3 4.9 4.7 5.1 3.5 5.0 4.3 3.4
##  [577] 3.4 5.8 4.8 6.2 3.9 5.1 4.9 4.6 3.9 4.5 4.4 4.9 3.3 2.8 3.9 4.6 2.6 5.4
##  [595] 3.2 4.1 4.6 2.5 4.0 5.6 4.6 3.5 5.8 5.3 4.2 3.9 5.1 4.5 4.4 4.0 4.1 3.9
##  [613] 5.4 5.2 4.8 4.3 4.8 4.5 3.8 5.4 4.0 5.2 4.1 5.1 5.7 3.8 5.4 5.3 4.9 4.8
##  [631] 4.8 5.7 5.5 2.2 3.8 4.2 5.1 4.3 3.9 5.1 5.0 5.6 4.0 4.1 3.6 4.8 3.8 1.9
##  [649] 4.2 5.8 5.2 4.7 4.6 4.8 3.9 5.0 4.2 4.6 3.2 4.2 3.3 4.6 5.6 4.7 3.5 3.8
##  [667] 4.9 7.2 5.2 3.3 5.1 5.2 4.6 4.7 5.0 5.2 4.1 4.5 3.2 5.1 4.8 4.4 5.8 6.2
##  [685] 5.9 5.7 5.0 5.5 5.2 5.5 2.9 4.9 4.3 4.8 5.5 5.1 6.0 3.7 5.7 4.5 6.1 6.0
##  [703] 4.0 5.4 5.7 4.6 4.0 3.7 5.9 3.9 5.0 4.8 3.9 4.2 3.5 4.0 2.8 4.9 4.3 4.3
##  [721] 4.0 4.0 4.8 3.6 4.6 5.8 2.6 5.7 4.6 4.2 5.1 4.9 5.3 4.6 5.2 4.7 4.8 4.7
##  [739] 4.7 3.7 4.6 5.8 5.1 3.2 3.7 2.3 3.9 4.9 4.5 3.6 4.5 3.0 5.6 4.7 5.2 3.5
##  [757] 6.0 4.3 4.6 4.4 4.6 3.9 3.9 4.5 4.3 4.7 3.8 5.6 5.7 4.8 5.7 5.5 4.4 7.5
##  [775] 6.9 4.9 3.8 5.9 4.6 3.2 5.2 5.5 2.7 3.5 4.8 5.4 3.3 3.4 5.1 2.9 3.3 5.2
##  [793] 4.5 5.1 6.0 4.7 5.0 3.5 4.5 5.0 4.5 3.9 3.2 5.3 4.7 4.3 4.9 4.7 4.4 3.8
##  [811] 3.1 4.3 4.5 4.4 4.4 5.3 4.2 4.3 4.4 4.6 3.4 6.3 2.8 6.5 4.2 4.9 6.2 3.8
##  [829] 4.5 4.5 3.6 4.8 4.7 4.3 5.1 5.0 2.9 3.8 3.8 5.9 5.4 4.2 4.5 4.7 4.7 4.9
##  [847] 5.3 3.1 2.7 5.0 5.5 3.1 4.0 4.4 4.1 4.5 6.7 4.9 5.1 3.7 4.2 6.0 5.7 5.1
##  [865] 4.3 4.3 3.8 5.0 4.4 3.8 5.8 5.1 3.8 5.8 5.6 4.4 4.3 4.3 4.0 4.7 5.6 2.4
##  [883] 3.7 4.8 4.1 4.9 3.3 6.0 3.9 2.5 5.0 4.1 5.6 2.7 5.7 5.8 4.6 4.0 4.3 5.1
##  [901] 5.4 5.1 3.2 4.8 3.6 4.6 3.2 4.8 3.4 3.2 5.0 4.0 4.6 3.8 4.6 3.7 3.1 3.8
##  [919] 4.1 5.1 5.4 2.8 1.7 5.6 4.7 4.8 5.4 4.3 4.0 5.5 4.6 4.3 6.4 4.1 2.8 3.6
##  [937] 5.7 3.2 4.0 2.5 5.6 6.0 4.3 3.6 5.1 4.2 4.6 4.7 5.7 3.7 4.6 4.1 4.7 4.0
##  [955] 3.5 4.5 4.9 3.7 3.4 4.7 4.1 5.0 2.3 4.3 4.4 4.5 4.3 4.5 3.8 3.2 5.6 4.1
##  [973] 3.9 4.9 4.4 5.4 1.9 5.9 4.3 4.4 6.6 3.5 4.7 3.3 5.8 3.3 5.7 4.5 3.3 3.6
##  [991] 4.5 3.5 4.2 3.7 6.7 4.3 4.9 3.9 3.3 4.7
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.3 5.2 5.1 4.9 4.9 4.8 4.6 4.5
## 
## $jack.boot.se
## [1] 0.9954396
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
## [1] 0.489722
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
##     shape       rate  
##   4.023877   7.417965 
##  (1.730114) (3.397062)
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
## [1] 0.5438831 0.4638466 1.3933250 0.5209854 0.6468559 0.6579343
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
##    [1]  0.3028082573  0.5299044680  0.4874861295  0.0559813610  0.3460168933
##    [6]  0.1210867847  0.4432379284  1.0677792262  1.0218756771  0.8634091959
##   [11]  0.2927402227  0.8603274047 -0.5032097137  0.6724930443 -0.4843309625
##   [16]  0.0715085140  0.1873987262  0.2245396422  0.5665008056  0.5828763004
##   [21]  0.2637913841  0.1493433377  0.2528972057  0.3179287196  0.2593326756
##   [26]  0.6780595027  0.8403242174  0.1065022230 -0.0111300110  0.1948478378
##   [31]  0.3812315904  0.3841013286  0.2667157439  0.7767470935  0.2081805192
##   [36] -0.0632656772  0.1368323084  1.0196966678  0.6443648225 -0.0325070810
##   [41]  1.1532715040  0.8954796622  0.8991028473  0.3138965805  0.4733660001
##   [46] -0.0381477848  0.4095834399  1.1145713428  0.1411399635  0.1921419008
##   [51] -0.2740411055 -0.3032256270  0.0058940925  0.9496552612  0.3824368790
##   [56]  0.7285669563  0.0648638777  0.5743691045  0.1548703155 -0.0849257146
##   [61]  0.4892534358  0.2920132333 -0.1853204072 -0.0256892106  0.4493986092
##   [66]  0.5557850324  1.1713050664  0.7503413539 -0.2731783078  0.0773672580
##   [71]  0.4051596841 -0.0594664384  0.3924378538  0.6870035630  1.4237462491
##   [76]  1.4897976526 -0.0014808101  0.9291193117  0.4057525364  1.2839806719
##   [81]  1.1999644915  0.4337477442 -0.1367830306  0.7579960844 -0.0126502817
##   [86]  0.3529896181 -0.6713185133  1.0525711530  0.4371306473  1.3665870901
##   [91]  0.6596228801  0.0865860569  0.3322666916  0.3937958046  0.7323085747
##   [96]  0.0134757399  1.2975298432 -0.3390641189 -0.3014788920  0.7704445607
##  [101]  0.0780991677  0.2714072093  0.7310116074  1.0156272600  0.2229995793
##  [106] -0.1579561302 -0.2495111424  0.7523366413  0.4611738922  0.1087421371
##  [111] -0.3492347256  0.1219991849 -0.0740252893  1.8814755817 -0.0173262988
##  [116]  0.3267845697  0.5076990940 -0.3683019090  0.8707997537  1.3776764603
##  [121]  0.2455730503  0.5408418417 -0.0511651945  0.1822538596  0.1722232678
##  [126]  0.3217134953  0.5086309142 -0.1035188289  1.0874811537 -0.2666172615
##  [131]  0.7621322953 -0.3868233689  0.9555187771  1.2396070033  0.3342320252
##  [136]  0.1426171870  1.3105812905  0.6073005594  0.3068848115  0.0631157436
##  [141]  0.8747311195 -0.0775124186  1.7820966532  1.0993698108  0.7204995122
##  [146]  1.9543692352  0.7876348745  0.5449326415  0.2224569937  0.6487833171
##  [151]  1.0824277243  0.6741533532  0.9675123425  0.6459722517  0.7479934302
##  [156]  0.0480620203  0.4765490669  0.5734644568  0.4832166830  0.3454992926
##  [161]  0.7957841087 -0.0476743269  0.8597996356  0.1482767674 -0.3439196823
##  [166]  0.8518818211  0.5030820969  0.4978322792  0.9876303557  0.5030942708
##  [171]  1.0330692988  0.7008293436  1.3174104748  0.6033480775  0.7136765098
##  [176]  0.7543359002  0.7336150788  0.1373589934  0.7922497702  0.6900818397
##  [181]  0.8123314054 -0.1801509221  0.9456189240  0.7625859809  0.8846661004
##  [186]  0.0889470615  0.3999764894  0.4351857679  0.4761109057  0.5949786254
##  [191]  0.5384476062  0.0060295707  1.1825920640 -0.1947287567  0.3682294708
##  [196] -0.4822756711  0.3729139176 -0.0056376601  0.5702305891 -0.0867146417
##  [201]  0.6787168454  0.0600054202  0.4876438444  0.4408452754  0.1306048967
##  [206]  0.4672896237  0.3696725578  0.3048704641  0.3379192785  0.2361471988
##  [211]  0.4755045351  1.9258915703 -0.0203811446  0.3859633383  0.1228868812
##  [216]  0.7792082702  0.6811714587  0.7672422309  0.1376416913 -0.7656448300
##  [221] -0.0334567331 -0.0627277651  0.5314147809  0.7410884351  0.9937004801
##  [226]  0.1321020945  1.4081403564  0.4036265567  0.3895142920  0.7717332934
##  [231] -0.5179915748 -0.6057599781  0.5474774723  0.5669774164  0.3169588116
##  [236]  0.0648245075  0.2051938977  0.7706242582  0.5735818031  0.3601612253
##  [241]  0.3811426355  0.8659573337  0.5701465138  0.2400307664  0.5119374311
##  [246]  0.5304263907  1.2318073787  0.4442449437  1.5868403918  0.6217895808
##  [251]  1.1870957064  0.7898627864  0.2744271041 -0.7452456325  0.0424399243
##  [256] -0.3297675424  0.4060865849  0.3120949687  0.0677861771 -0.0898512968
##  [261]  0.4120791466  0.1497891390  0.2243898498  0.3573013188  0.8308778210
##  [266]  0.8731007440  0.4309902763 -0.0367028940  0.4709549424  0.6115604703
##  [271]  0.4386716330  0.6008485026 -0.2048007903  0.0801176977  0.8652981270
##  [276]  0.1076859822  0.9570870622  0.3839632996  0.4936553433  1.0594777977
##  [281]  0.0937960648 -0.0922424915  0.5330187286 -0.0647690595  0.7563252201
##  [286]  1.6943507987  0.1695770868 -0.0839071711  0.5842884802  0.7816499487
##  [291]  1.0110477479  0.7371956958  0.6353727037  0.1805135214  0.7830525201
##  [296]  1.0168513503  0.2810939505  0.6336784890  1.4495444498  0.1716436198
##  [301]  0.5248742563 -0.0400595979  0.1932921283 -0.1196489524  0.0556246590
##  [306]  0.4844110255  0.7209614492  0.3240560212  0.8868153357  0.5082008733
##  [311]  1.3011460263  0.3163260928  0.4737306575 -0.0803745774  0.5835254949
##  [316]  0.7327351127 -0.4202348741  0.3520515560 -0.0822960230  0.4578054777
##  [321]  0.1342045187  0.4806747158  0.5708026789  0.0576738014  0.2486960694
##  [326] -1.0413505494  0.0938556848 -0.0324244573 -0.3792680863  0.7619450209
##  [331]  1.0998793517 -0.1893002492  1.4499195478  0.4945649564  0.8766423655
##  [336]  0.4939211304  0.6454433537  0.7078656782  1.0218904145  0.2695845872
##  [341]  0.7157147288  1.0894413873  0.6603231923  0.2947900720  0.5694750790
##  [346]  0.7049303130  0.1067598880  0.7364510572  0.0679785096  0.4489896339
##  [351]  0.4754223082  0.6688415176 -0.1181487184  0.5594114987  0.8410774912
##  [356]  0.8832307940 -0.0229361817 -0.1357489751  0.3476614922  1.2942646298
##  [361]  0.4513300869  0.7404009668  0.1812084585  0.9744509351 -0.2492194762
##  [366] -0.0330398125  0.8593863375  0.1969058416  0.2051938977  0.5944997499
##  [371]  0.0377486539  0.5253009121 -0.3002965158 -1.0878489184  0.4726512603
##  [376]  0.1465145065  1.6693159265  0.6894852603  0.3033533903  0.8729553673
##  [381] -0.4202651368  0.5554310889  1.9533868977 -0.2151369554  0.4524695861
##  [386]  0.7809172264  0.3801537107  0.7905435786  0.2077906825 -0.0569732406
##  [391] -0.1761786426  1.3776000166  0.4502324338  0.6842380425  0.2863369222
##  [396]  0.7127469094  0.0709491128  0.2651366710  0.5531867093  0.4559456159
##  [401] -0.2891952517  0.6258090145  0.9613339285  0.5771913044  0.0378351372
##  [406] -0.0999405274  0.5187181770  1.4233598267  0.2665742542  0.2794589851
##  [411]  0.8941865962  1.0595943419  0.4331824992  0.5509520608  0.1594823481
##  [416] -0.2662983195  0.0827337952  1.0577767596  0.4672433273  1.0368114619
##  [421]  0.5988040017  0.6183927250  0.0334916940  0.3852069731  0.2874019548
##  [426]  1.2777066960  0.3156621673 -0.2378565498  1.7862021987 -0.5451364972
##  [431] -0.0896559301 -0.0561018754  1.4344638246  0.5187181770  0.7139183564
##  [436] -0.6312802391 -0.0333338137  1.4344638246  0.4197603527  0.8716593653
##  [441]  0.7050867410  1.4633317878 -0.2411044992 -0.4107696611  0.6578306368
##  [446] -0.7188486698  0.9341387380  0.7357004561  0.5129569376  0.3176314335
##  [451]  0.9941553809  0.2333176217 -0.0551281848  0.4485746934  0.1787418862
##  [456]  0.5247014461  0.3794838030  0.4124543849  1.2706741349  1.3761451724
##  [461]  0.2265492138  0.4566343050  0.2802740850  0.4997380595  0.3212051439
##  [466]  0.3593601093  1.2165887678  0.4782579871  0.3908153942  1.8474687975
##  [471]  1.0056745359  0.6175724488  0.3552142917  1.2161504572  0.0315931335
##  [476]  0.0763131642  0.2567238864  0.7892433279  0.0181255621  0.0347951412
##  [481]  1.8392076033  0.1548138587  0.2645596206 -0.8931087718  0.1778261926
##  [486]  0.8988968135  0.1088982241  0.8751913001  1.5297168144  0.7360739747
##  [491]  0.0748691145 -0.4263731962  0.6349299983  0.4217922625  0.2708855853
##  [496]  0.7423540954  0.1870753513 -0.3900388969  0.1026425829 -0.0410298366
##  [501]  0.6388861072  0.4820236944  0.6747858197  0.2133100355  0.4151276003
##  [506] -0.0828074936  0.1475249669  0.9200657468  0.3401887149  0.4200603650
##  [511]  0.6656986732  1.1464524280  0.1232828567  0.2658484200 -0.1807226425
##  [516]  0.4685252689  0.0825129896 -0.2864402718  0.9442988642 -0.0611713685
##  [521]  0.1594774886 -0.1188553178  0.1135271185  0.6083613222  0.9172763380
##  [526]  0.1053357618  1.1785061747  0.4051596841  0.7141178476 -0.0368032751
##  [531]  0.3400895717  0.3220296943  0.0228577194 -0.0340399675 -1.0214600910
##  [536] -0.0178116677  0.4981062384  0.3674209861 -0.1279098740  0.5715134601
##  [541]  1.1853659480  0.3052277253  0.6525650818  0.0567915032  0.9834343578
##  [546]  1.1477335108  1.0472789251  0.7223708024  0.8394187773  1.2416186588
##  [551]  0.3529351753  0.3268283076 -0.9005173247 -0.0039716476  0.6176482495
##  [556]  1.6842773729  0.4817860671  0.3454169615  0.9639614922  0.1946605963
##  [561]  0.6504313161 -0.1095262258  0.4712720425  0.5773454575  0.3787621391
##  [566]  0.1322801160  0.7968727836  0.1267421821  1.0557879740  0.2185154032
##  [571]  0.4819977840  0.5082804189  0.7968727836  0.5429237899  0.6058453438
##  [576]  0.2271110414 -0.0660400713  0.2094263814 -0.2996206891  0.4423146455
##  [581] -0.4142730560  0.0585932740  1.3441919755  0.3407937851  0.6502566926
##  [586] -0.1005517193  0.1866752208  0.2958853497 -0.4471460935  0.4620488945
##  [591]  0.5075002194  0.9388583975  0.6137180143  0.3115117448  0.8071768712
##  [596]  0.3396696685  0.1012176808  0.2383295171  1.1097706642  1.9762393015
##  [601]  0.5679471744  0.4813423022  0.2980336103  1.0066825368  0.3553591198
##  [606]  0.3978852787  0.6028285219  0.4612507918  0.5370184090  0.6557353190
##  [611]  0.9781552411 -0.0324050755  0.3551767559  0.6483057537  0.8861713265
##  [616] -0.6546583784 -0.0912440041 -0.2903229875 -0.2112902336 -0.0356106797
##  [621] -0.0510905634  1.1446463217  0.3123399635  0.6974968317 -0.0094532934
##  [626]  0.4856165143  2.6203453455  1.1996561077  0.3362971475  0.0337423274
##  [631]  0.8408276580 -0.3017896570  0.0749153989  0.7141178476  0.1374518052
##  [636]  0.1569884764  0.2005304716  0.7050178251  1.5362703183  0.3490473344
##  [641]  0.1242935848  0.9678230740  0.6524881185  0.5231079364  0.4730355462
##  [646]  0.5392475095  0.0445670632  0.5975120893  0.3628626928  0.3695017019
##  [651]  0.3510956246 -0.0388147792 -0.0009628088  0.6699356971  0.9165576192
##  [656]  0.7722479885  0.6376742589  0.5445159431  0.9198434440 -0.0972759285
##  [661]  0.8553808021  0.2187068769  0.7390633888  0.7483155737  0.6240216996
##  [666]  1.5801485967 -0.3422981117  1.0183135345  0.5199094837  0.7699108699
##  [671]  1.4028026119  1.7570440540  0.3129055380  0.6380464278  0.3628626928
##  [676]  0.0318546735 -0.1539855748  0.6566054162  0.4478647688  0.5461245772
##  [681]  0.8694636447  0.5030762263 -0.0874638148  0.6409440793  0.6062313678
##  [686]  0.4624155893  0.4877838182 -0.0828074936  0.5830625358  0.5681632844
##  [691] -1.3489041591  0.1511122861  0.6599110169  0.8186847000  0.7383019148
##  [696] -0.2597409859  1.0879477359  0.3776690677  0.6026158522  0.2697670341
##  [701] -1.2953940645  0.2593322619 -0.0394334543  0.8220406916 -0.0611459695
##  [706]  0.5719293588  0.3739971668  0.0703680688 -0.0303708203  0.1706372297
##  [711]  0.4597972935  0.5035795601  0.1938121527  0.8922655385  1.1173557289
##  [716]  0.2300121776  0.9907675298 -1.0449685136  0.8506286867  0.5523129650
##  [721] -0.0163280666  0.9242630535  0.9231134242  0.9873666976 -0.4985457012
##  [726]  0.7088440363  0.8656146183  0.6137180143  1.2574586900  0.4584118185
##  [731]  0.6387656264  0.2075325897  0.6469000752  0.6430189145  0.4493634266
##  [736]  1.3430469776  0.1329170980  1.3984921202  0.2008179729  0.4173784814
##  [741] -0.3883580967  0.4735891507  0.4613403945  0.7630896211  1.0635648178
##  [746]  0.5233715386  0.1109472924  0.5707301830  1.4059061864  0.7642564341
##  [751]  0.2062718865  0.2049844016  0.6983173473  0.0872476591  0.7678929340
##  [756]  0.0641014397  0.5605046814  1.0604604211  0.2618175766  0.5118880494
##  [761]  0.2566339686  0.8882929983  0.8440538930  0.3844341440  0.3470977200
##  [766]  0.4363030067  0.7704445607  0.6108412563  0.3653521692 -0.0086017611
##  [771]  0.4692725905  0.1263773658  0.5086309142  0.3612674205  0.8945796657
##  [776]  0.6930495001  0.2920262537  0.6966542707  0.5064394637  0.5951194217
##  [781]  0.3785129207  0.0302298647  1.0295711685  0.8772887368  0.8667164906
##  [786]  1.2361823357 -0.1802564311  0.1516822266  1.3588889896  0.0566365483
##  [791]  0.2911233721  0.4276620822  0.8971667599 -0.0974763005  0.4808768508
##  [796] -0.2419240384 -1.0418497376  0.2504690581  0.2261103631  0.6178430839
##  [801]  0.2509742937  0.7617082407  0.5329046044  0.5698773385  0.2824273265
##  [806] -0.0547654592 -0.2951195411  0.4687948045  1.0598637062  1.0140703314
##  [811]  0.8145285929  0.3386049679  0.3693219082 -0.2558351832 -0.3461442432
##  [816]  1.4284505140  0.3433119977  0.4231481209  1.0751734625  0.7409150966
##  [821]  0.2347184399  0.6283393448  0.4624754569  0.6387803669  0.2475734686
##  [826]  0.7708733976  0.0188294326  0.1557257838  0.3112810180  0.2167214376
##  [831]  0.5604599477  0.4301408728  0.5983456765  1.8170467467  0.0735602798
##  [836]  1.0263488516  0.4201649369  0.0706981303  0.9562389831 -0.8071349297
##  [841]  0.4742871693  0.3856268883  0.0186071923 -0.0896438639  0.4554815021
##  [846]  0.4929382609  0.4363155367  0.6288482850  0.8928311549  1.1004324235
##  [851]  0.4500035050 -0.1823970722 -0.2115871098  0.3090277396  0.0139876031
##  [856]  0.3388444812  0.6730901449  0.4685797511  0.8898793118  0.5622209047
##  [861]  1.1793004714  0.1195085517  0.6247195258 -0.0045363872  0.5231392276
##  [866]  0.3136330585  0.2110076812  0.5016326333  0.0303965028  0.3279182156
##  [871]  0.3785816872  0.2546237942  0.7772923604  0.0334891103  1.7427485837
##  [876]  1.0442994809  0.1217127829  0.1149039063  0.6348452722  0.2226398407
##  [881]  1.3768635313  0.7581878585  0.3311453408  0.2363139135  0.1698372489
##  [886]  0.4901042339  0.7363956004  0.3815749397  0.3745792857  0.6376133101
##  [891]  0.7361486556  1.6206257792  0.3437982940  0.4530882186  0.2880080131
##  [896]  0.6204543346  0.8274846008  0.4871497870  0.3599740926  0.6307401104
##  [901]  1.5048819386  0.5597966015  0.2548330875  0.7553019735  1.1269114217
##  [906]  0.6794513711  0.9994124874  0.9263354797  0.2374922319 -0.2528117021
##  [911] -0.3561563020  0.9066918267  0.5758145297  0.5118422026 -0.0423999173
##  [916] -0.0602830459  0.0035472769  0.9771891272  0.3405900027  0.5683070825
##  [921]  0.7767795943  0.3625419372  0.3751368329 -0.2415101979  1.3039752019
##  [926]  0.6823559490  0.2754887757  1.2046376113  0.1384482657  1.0373638620
##  [931] -0.9661834544  0.7634002011  0.1498410568  0.0703018264  0.1845276414
##  [936]  0.4815114222  0.0207643228  0.8384723020  0.8712098136 -0.2032602887
##  [941]  0.2660874710 -0.7187035262  0.4247206345  0.3772624165  0.3269876406
##  [946]  0.3209313189  0.9759901833  0.3105964377  0.2234806412 -0.0693614452
##  [951]  1.0387741769  0.1608713930  0.3448244192  0.0228630934  0.6265980974
##  [956]  0.2226398407  0.6686030817  0.1570138544 -0.1611242346  0.6324921188
##  [961]  0.7386180497 -1.3261042803 -0.1242729625  0.6786677293  0.4669362049
##  [966]  0.3545688803  1.7777975845 -0.0463674269  0.0359852799 -0.7710541645
##  [971]  0.6667814710 -0.4250501392  0.3724009317 -1.0313667954  0.1203470408
##  [976] -0.1413093542  1.6928263261  0.8851003597  0.7457112328 -0.2054629012
##  [981]  0.4363030067  0.1374518052  0.4356259584  0.8591670986  0.3201090691
##  [986]  0.7643454632  0.3409373110  0.5515501505  0.6237950229  0.2804117449
##  [991] -0.0136993697 -0.5592992616  0.4714203786  0.1785114633  0.0581115255
##  [996]  0.8546741692  0.2840966034  1.0327244302  0.9135010927  0.7835099778
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
##   0.54244783   0.26673849 
##  (0.08435012) (0.05964119)
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
## [1] -0.1397622 -1.5820903  0.1250512 -0.5859536  0.2109195 -0.2692124
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
## [1] 0.0159
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9188915
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
## t1*      4.5 0.01271271   0.9349207
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 4 5 6 7 
## 1 3 1 1 4
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
## [1] -6e-04
```

```r
se.boot
```

```
## [1] 0.9253842
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

