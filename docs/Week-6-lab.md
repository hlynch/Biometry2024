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
##       [,1]     [,2]      [,3]      [,4]      [,5]      [,6]      [,7]      [,8]
##  [1,]   NA 0.769333 0.6618872 0.9949783 0.7863336 0.4823525 0.7285962 0.4292093
##  [2,]   NA       NA 0.4788619 0.7596792 0.5895425 0.7746553 0.5659574 0.3196034
##  [3,]   NA       NA        NA 0.6136179 0.9051722 0.1312784 0.9580917 0.5904535
##  [4,]   NA       NA        NA        NA 0.7638558 0.4339384 0.7114550 0.3855389
##  [5,]   NA       NA        NA        NA        NA 0.3045006 0.8991057 0.5929531
##  [6,]   NA       NA        NA        NA        NA        NA 0.3585654 0.1065991
##  [7,]   NA       NA        NA        NA        NA        NA        NA 0.7621317
##  [8,]   NA       NA        NA        NA        NA        NA        NA        NA
##  [9,]   NA       NA        NA        NA        NA        NA        NA        NA
## [10,]   NA       NA        NA        NA        NA        NA        NA        NA
##            [,9]     [,10]
##  [1,] 0.7156801 0.9432806
##  [2,] 0.5302224 0.8448216
##  [3,] 0.9832822 0.6618486
##  [4,] 0.6855168 0.9443346
##  [5,] 0.9319302 0.7594281
##  [6,] 0.2386519 0.6142363
##  [7,] 0.9508674 0.7067945
##  [8,] 0.6366813 0.4539185
##  [9,]        NA 0.7002570
## [10,]        NA        NA
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
## 0.001900625
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
