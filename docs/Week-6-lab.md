Week 6 Lab
=============
  
Today we will do a short exercise to illustrate the permutation method of dealing with multiple comparisons.

First, we will simulate 10 groups of data (say, heights) from the *same* distribution using the normal distribution and put the data for each group in a list for easy access:


```r
data.all<-list()
for (i in 1:10)
  {
    data.all[[i]]<-rnorm(10)  #Note the double brackets for a list
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
##       [,1]      [,2]       [,3]      [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.3400056 0.06284468 0.2664553 0.09991967 0.3410185 0.8330361
##  [2,]   NA        NA 0.56667205 0.9260031 0.73473968 0.9282525 0.4663074
##  [3,]   NA        NA         NA 0.6160360 0.71210008 0.4461896 0.1316385
##  [4,]   NA        NA         NA        NA 0.80511291 0.8444051 0.3875258
##  [5,]   NA        NA         NA        NA         NA 0.6145204 0.1966716
##  [6,]   NA        NA         NA        NA         NA        NA 0.4840416
##  [7,]   NA        NA         NA        NA         NA        NA        NA
##  [8,]   NA        NA         NA        NA         NA        NA        NA
##  [9,]   NA        NA         NA        NA         NA        NA        NA
## [10,]   NA        NA         NA        NA         NA        NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.4652575 0.5591215 0.5827966
##  [2,] 0.7223686 0.6573985 0.7657372
##  [3,] 0.2399079 0.2190199 0.3932909
##  [4,] 0.6276188 0.5671182 0.6931232
##  [5,] 0.3620616 0.3253321 0.5133966
##  [6,] 0.7725341 0.6983508 0.8136920
##  [7,] 0.6398794 0.7324407 0.7200278
##  [8,]        NA 0.9051178 0.9912420
##  [9,]        NA        NA 0.9353946
## [10,]        NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 0
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
## 0.001924442
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
