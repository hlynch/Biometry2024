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
##       [,1]      [,2]      [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.5618149 0.6692382 0.8512373 0.7422529 0.04374654 0.9940384
##  [2,]   NA        NA 0.8436381 0.7865472 0.3567847 0.12753008 0.5129537
##  [3,]   NA        NA        NA 0.8971070 0.4216289 0.07487748 0.6180316
##  [4,]   NA        NA        NA        NA 0.6643249 0.14355623 0.8373013
##  [5,]   NA        NA        NA        NA        NA 0.01955801 0.7057112
##  [6,]   NA        NA        NA        NA        NA         NA 0.0287631
##  [7,]   NA        NA        NA        NA        NA         NA        NA
##  [8,]   NA        NA        NA        NA        NA         NA        NA
##  [9,]   NA        NA        NA        NA        NA         NA        NA
## [10,]   NA        NA        NA        NA        NA         NA        NA
##             [,8]       [,9]      [,10]
##  [1,] 0.45282040 0.99995163 0.36311160
##  [2,] 0.18784519 0.63117024 0.86354382
##  [3,] 0.20254513 0.73197958 0.64809598
##  [4,] 0.47471995 0.86835452 0.66738517
##  [5,] 0.64588866 0.79624913 0.15505686
##  [6,] 0.00946519 0.08915205 0.09080976
##  [7,] 0.35573076 0.99544998 0.25317258
##  [8,]         NA 0.57330415 0.02760211
##  [9,]         NA         NA 0.49530505
## [10,]         NA         NA         NA
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
## 0.002049023
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
