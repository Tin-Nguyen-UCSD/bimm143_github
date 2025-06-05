# Class 7: Machine Learning 1
Tin Nguyen

- [Clustering](#clustering)
  - [K-means](#k-means)
  - [Hierarchical clustering](#hierarchical-clustering)
- [Principal Component Analysis
  (PCA)](#principal-component-analysis-pca)
  - [Data import](#data-import)
  - [PCA to the rescue](#pca-to-the-rescue)
  - [interpreting PCA Results.](#interpreting-pca-results)

Today we will explore unsupervised machine learning methods starting
with clustering and dimensionality reduction.

## Clustering

To start let’s make up some data to cluster where we know what the
answer should be. The ‘rnorm()’ function will help us here.

``` r
hist( rnorm(10000, mean = 3) )
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-1-1.png)

Return 30 numbers centered on -3

``` r
tmp <- c( rnorm(30, mean = -3), rnorm(30, mean = +3) )

x <- cbind(x = tmp, y = rev(tmp))
```

Make a plot of ‘x’

``` r
plot(x)
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-3-1.png)

### K-means

The main function in “base” R for K-means clustering is called
‘kmeans()’:

``` r
km <- kmeans(x, centers = 2)
km 
```

    K-means clustering with 2 clusters of sizes 30, 30

    Cluster means:
              x         y
    1  3.303260 -2.912757
    2 -2.912757  3.303260

    Clustering vector:
     [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1 1
    [39] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

    Within cluster sum of squares by cluster:
    [1] 38.59219 38.59219
     (between_SS / total_SS =  93.8 %)

    Available components:

    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

The ‘kmeans()’ function return a “list” with 9 components. You can see
the named components of any list with the ‘attributes()’ function.

``` r
attributes(km)
```

    $names
    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

    $class
    [1] "kmeans"

> Q. How many points are in each cluster ?

``` r
km$size
```

    [1] 30 30

> Q. Cluster assignment/membership vector?

``` r
km$cluster
```

     [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1 1
    [39] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

> Q. Cluster centers?

``` r
km$centers
```

              x         y
    1  3.303260 -2.912757
    2 -2.912757  3.303260

> Q. Make a plot of our ‘kmeans()’ results showing cluster assignment
> using different colors for each cluster/group of points and cluster
> centers in blue.

``` r
plot(x, col = km$cluster )
points(km$centers, col = "blue", pch = 2, cex = 2)
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-9-1.png)

> Q, Run ‘kmmeans()’ again on ‘x’ and this cluster into 4 groups/
> clusters and plot the same results figure as above.

``` r
km2 <- kmeans(x, centers = 4)
plot(x, col = km2$cluster )
points(km2$centers, col = "blue", pch = 2, cex = 2)
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-10-1.png)

> **key-point**: K-means clustering is super popular but can be
> miss-used. One big limitation is that it can impose a clustering
> pattern on your data even if clear natural goruping don’t exist -
> i.e. it does what you tell it to do in terms of ‘centers’.

### Hierarchical clustering

The main function in “base” R for herarchical clustering is called
‘hclust()’.

You can’t just pass our dataset as is into ‘hclust()’ you must give
“distacne matrix” as input. WE can get this from ‘dist()’ function in R.

``` r
d <- dist(x)
hc <- hclust(d)
hc 
```


    Call:
    hclust(d = d)

    Cluster method   : complete 
    Distance         : euclidean 
    Number of objects: 60 

The results of ‘hclust()’ don’t have a useful ‘print()’ method but do
have a special ‘plot()’ method.

``` r
plot(hc)
abline(h=8, col="red")
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-12-1.png)

To get our main cluster assignment (membership vector) we need to “cut”
the tree at the big goalposts…

``` r
grps <- cutree(hc, h = 8)
```

``` r
table(grps)
```

    grps
     1  2 
    30 30 

``` r
plot(x, col = grps)
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-15-1.png)

Hierarchical Clustering is distinct in that the dendrogram (tree figure)
can reveal the potential grouping in your data (unlike K-means)

## Principal Component Analysis (PCA)

PCA is a common and highly useful dimensionality reuction technique used
in many fields - particularly bioinformatics.

Here we will analyze some data from the UK on food consumption.

### Data import

``` r
url <- "https://tinyurl.com/UK-foods"
x <- read.csv(url)

head(x)
```

                   X England Wales Scotland N.Ireland
    1         Cheese     105   103      103        66
    2  Carcass_meat      245   227      242       267
    3    Other_meat      685   803      750       586
    4           Fish     147   160      122        93
    5 Fats_and_oils      193   235      184       209
    6         Sugars     156   175      147       139

``` r
x <- read.csv(url, row.names = 1)

head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

``` r
barplot(as.matrix(x), beside=F, col=rainbow(nrow(x)))
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-18-1.png)

One conventional plot that can be useful is called a “paris” plot.

``` r
pairs(x, col=rainbow(nrow(x)), pch=16)
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-19-1.png)

### PCA to the rescue

The main function in base R for PCA ‘prcomp()’.

``` r
pca <- prcomp( t(x) )

summary(pca)
```

    Importance of components:
                                PC1      PC2      PC3       PC4
    Standard deviation     324.1502 212.7478 73.87622 2.921e-14
    Proportion of Variance   0.6744   0.2905  0.03503 0.000e+00
    Cumulative Proportion    0.6744   0.9650  1.00000 1.000e+00

### interpreting PCA Results.

The ‘prcomp()’ function returns a list object of our results with five
attritubtes/components

``` r
attributes(pca)
```

    $names
    [1] "sdev"     "rotation" "center"   "scale"    "x"       

    $class
    [1] "prcomp"

The two main “results” in here are ‘pca$x' and 'pca$rotation’. The first
of these (‘pca\$x’) contains the scores of the data on the new PC axis -
we use thse to make our “PCA plot”.

``` r
pca$x 
```

                     PC1         PC2        PC3           PC4
    England   -144.99315   -2.532999 105.768945 -9.152022e-15
    Wales     -240.52915 -224.646925 -56.475555  5.560040e-13
    Scotland   -91.86934  286.081786 -44.415495 -6.638419e-13
    N.Ireland  477.39164  -58.901862  -4.877895  1.329771e-13

``` r
library(ggplot2)
library(ggrepel)

# Make a plot of pca$x with PC1 vs PC2

ggplot(pca$x) + 
  aes(PC1, PC2, label=rownames(pca$x)) +
  geom_point() +
  #geom_label()+
  geom_text_repel()
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-23-1.png)

PCA picks find the principle directions that describe the most different
between the countries. It found that in the most principle direction
(PC1) Ireland is the most different from other country.

The second major result is contained in the ‘pca\$rotatoin’ object or
component. :et’s plot this to see what PCA is picking up…

``` r
ggplot(pca$rotation) +
  aes(PC1, rownames(pca$rotation)) + 
  geom_col()
```

![](Class-07--Machine-Learning-1_files/figure-commonmark/unnamed-chunk-24-1.png)

In the most principle direction PC1, this graph of rotation shows
factors that contribute to the different in the countries. This shows
that Soft drinks and Freshfruit consumption is siginificantly higher in
Irelands compares to other countries. And other countries consume more
frsh fruit and alcoholic dirnks than Ireland.
