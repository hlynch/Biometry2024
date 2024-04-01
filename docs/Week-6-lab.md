Week 6 Lab
=============
  
Today we will do a short exercise to illustrate the permutation method of dealing with multiple comparisons.

First, we will simulate 10 groups of data (say, heights) from the *same* distribution using the normal distribution and put the data for each group in a list for easy access:


```r
data.all<-list()
for (i in 1:10)
  {
    data.all[[i]]<-rnorm(5)  #Note the double brackets for a list
  }
```

Now we can compare each group pairwise using a t.test.


```r
p.values<-matrix(ncol=10,nrow=10)
for (i in 1:9)
  {
    for (j in (i+1):10)
      {
        p.values[i,j]<-t.test(data.all[[i]],data.all[[j]])$p.value 
      }
  }
p.values
```

```
##       [,1]       [,2]      [,3]       [,4]       [,5]       [,6]        [,7]
##  [1,]   NA 0.06368416 0.5838991 0.98661149 0.71020561 0.80142955 0.411223410
##  [2,]   NA         NA 0.1998734 0.04681936 0.05871088 0.05717086 0.008488879
##  [3,]   NA         NA        NA 0.56756431 0.41004976 0.69884641 0.191583175
##  [4,]   NA         NA        NA         NA 0.68351075 0.79247038 0.339998589
##  [5,]   NA         NA        NA         NA         NA 0.54198588 0.790097835
##  [6,]   NA         NA        NA         NA         NA         NA 0.191032672
##  [7,]   NA         NA        NA         NA         NA         NA          NA
##  [8,]   NA         NA        NA         NA         NA         NA          NA
##  [9,]   NA         NA        NA         NA         NA         NA          NA
## [10,]   NA         NA        NA         NA         NA         NA          NA
##             [,8]        [,9]      [,10]
##  [1,] 0.49355952 0.395523139 0.69709846
##  [2,] 0.10054760 0.008454453 0.02125135
##  [3,] 0.99025089 0.183158943 0.35009801
##  [4,] 0.44721912 0.314594947 0.65052915
##  [5,] 0.34135945 0.792145357 0.94027986
##  [6,] 0.59165670 0.161926176 0.45374938
##  [7,] 0.06047482 0.981255523 0.61678331
##  [8,]         NA 0.041445929 0.20695639
##  [9,]         NA          NA 0.59820494
## [10,]         NA          NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 5
```

We could correct this using the Bonferonni method:


```r
k<-45
new.threshold.B<-0.05/k
new.threshold.B
```

```
## [1] 0.001111111
```

```r
false.positives.B<-sum(p.values<new.threshold.B,na.rm=T)
false.positives.B
```

```
## [1] 0
```

We could correct this using the Dunn-Sidak method:


```r
k<-45
new.threshold.DS<-1-((1-0.05)^(1/k))
new.threshold.DS
```

```
## [1] 0.001139202
```

```r
false.positives.DS<-sum(p.values<new.threshold.DS,na.rm=T)
false.positives.DS
```

```
## [1] 0
```

We could correct this using the randomization method. This requires simulating data under the null hypothesis to generate a null distribution of p-values.



```r
p.values.all<-c()
min.p.values.all<-c()
for (k in 1:1000)
  {
    data.null<-list()
    for (i in 1:10)
      {
        data.null[[i]]<-rnorm(10)  #Note the double brackets for a list
      }
    p.values.null<-matrix(ncol=10,nrow=10)
    for (i in 1:9)
      {
        for (j in (i+1):10)
          {
            p.values.null[i,j]<-t.test(data.null[[i]],data.null[[j]])$p.value 
          }
      }
    p.values.all<-c(p.values.all,c(p.values.null)[!is.na(c(p.values.null))])
    min.p.values.all<-c(min.p.values.all,min(c(p.values.null)[!is.na(c(p.values.null))]))
  }
new.threshold.R<-quantile(min.p.values.all,probs=c(0.05))
new.threshold.R
```

```
##          5% 
## 0.001793704
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
