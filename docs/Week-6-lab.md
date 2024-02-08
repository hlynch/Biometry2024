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
##       [,1]      [,2]      [,3]       [,4]      [,5]       [,6]       [,7]
##  [1,]   NA 0.6300437 0.2772713 0.07966058 0.2576194 0.01775478 0.09806101
##  [2,]   NA        NA 0.5398729 0.19782502 0.5246370 0.05312674 0.22976254
##  [3,]   NA        NA        NA 0.49854085 0.9966036 0.17570841 0.54516083
##  [4,]   NA        NA        NA         NA 0.4766742 0.47533104 0.95622856
##  [5,]   NA        NA        NA         NA        NA 0.15627610 0.52551200
##  [6,]   NA        NA        NA         NA        NA         NA 0.45507310
##  [7,]   NA        NA        NA         NA        NA         NA         NA
##  [8,]   NA        NA        NA         NA        NA         NA         NA
##  [9,]   NA        NA        NA         NA        NA         NA         NA
## [10,]   NA        NA        NA         NA        NA         NA         NA
##            [,8]      [,9]       [,10]
##  [1,] 0.1109474 0.3188068 0.005087682
##  [2,] 0.2296270 0.5761209 0.017598455
##  [3,] 0.4963495 0.9921172 0.070725268
##  [4,] 0.9183915 0.5237756 0.235174062
##  [5,] 0.4805613 0.9949588 0.058101609
##  [6,] 0.6121414 0.2052035 0.640424724
##  [7,] 0.8827790 0.5666239 0.228485891
##  [8,]        NA 0.5141702 0.366860536
##  [9,]        NA        NA 0.092660802
## [10,]        NA        NA          NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 3
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
## 0.002064455
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
