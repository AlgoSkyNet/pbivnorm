---
output:
  md_document:
    variant: markdown_github
---

<!-- README.md is generated from README.Rmd. Please edit that file -->



# pbivnorm

[![Travis-CI Build Status](https://travis-ci.org/brentonk/pbivnorm.png?branch=master)](https://travis-ci.org/brentonk/pbivnorm)

`pbivnorm` is an R package containing a vectorized function to compute the cumulative distribution function of the bivariate normal distribution.  It is based on [the `mnormt` package](http://cran.r-project.org/web/packages/mnormt/index.html) by [Adelchi Azzalini](http://azzalini.stat.unipd.it/index-en.html), which uses [Fortran code](http://www.math.wsu.edu/faculty/genz/software/fort77/mvtdstpack.f) by [Alan Genz](http://www.math.wsu.edu/faculty/genz/homepage) to compute integrals of multivariate normal densities.

A call to `pbivnorm()` produces identical output to a corresponding set of
calls to `mnormt::pmnorm()`, but at lower computational cost due to
vectorization (i.e., looping in Fortran rather than in R).


```r
library("pbivnorm")
library("mnormt")
library("MASS")
library("microbenchmark")

## Simulate data
set.seed(9497)
mu <- rep(0, 2)
Sigma <- matrix(c(1, 0.5, 0.5, 1), 2, 2)
X <- mvrnorm(100, mu = mu, Sigma = Sigma)

## Confirm equality of results
all.equal(pbivnorm(X, rho = 0.5),
          apply(X, 1, pmnorm, mean = mu, varcov = Sigma))
#> [1] TRUE

## Compare speed
microbenchmark(
    pbivnorm = pbivnorm(X, rho = 0.5),
    pmnorm = apply(X, 1, pmnorm, mean = mu, varcov = Sigma)
)
#> Unit: microseconds
#>      expr      min       lq    mean median     uq      max neval
#>  pbivnorm   92.165   95.625  109.82  110.2  113.5   240.37   100
#>    pmnorm 6546.772 6841.168 7365.95 7010.8 7706.7 10202.06   100
```
