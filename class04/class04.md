# class04
Tin Nguyen

``` r
# My first R script
x<-1:50
plot(x, sin(x))
```

![](class04_files/figure-commonmark/unnamed-chunk-1-1.png)

``` r
plot(x, sin(x), typ="l", col="blue", lwd=3, xlab="Silly x axis", ylab = "Sensible y axis")
```

![](class04_files/figure-commonmark/unnamed-chunk-1-2.png)

``` r
x<-seq(from = 1, to = 50, by=0.05)
plot(x, sin(x), typ="l", col="blue", lwd=3, xlab="Silly x axis", ylab = "Sensible y axis")
```

![](class04_files/figure-commonmark/unnamed-chunk-1-3.png)
