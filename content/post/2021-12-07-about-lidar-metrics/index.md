---
title:       "About Lidar Metrics"
description: "brief overview of Lidar metrics and how they can be used"
date:        2021-11-29
author:      "Ivo Jung"
image:       "img/ungabunga.jpg" 
categories: ["aGIS"]
tags: ["task"]
toc: true
output:
  blogdown::html_page:
    keep_md: yes
weight: 100
---

## Introduction

There are different metrics that can be calculated using Lidar Data. These metrics (only some)
are presented and discussed down below. Also how useful are different approaches 
to calculate these metrics and how/ for which topic can these metrics be used?
Can these metrics be used to estimate humidity or temperature?


Overall the metrics are some kind of attributes calculated from the Lidar 
data. Usually metrics are calculated at the basis of the point height (z-axis).

the *lidr* package offers some convenient functions to calculate basic metrics at
different levels:

  1. cloud metrics
  2. tree metrics
  3. grid metric
  4. voxel metrics
  5. point metrics
  
However, there are numerous existing methods to calculate different metrics. 
Still, there are standard sets of metrics that are used for different research
questions.
To calculate the metrics with the functions above, there is a formula needed. A
common formula is the average height of points for the point cloud relative to 
lidar surface. This can be calculated using fun =  ~mean() provided by the lidR 
package. Similar approaches  can be used for standard parameters like the 
standard deviation etc. of the points. Multiple formulas can be stored in one 
function, which can be called, when calculating the metrics to calculate 
multiple metrics using different formulas.

Following down below there is an exemplary workflow/calculation of some of \ these metrics and which information they provide




**1. Load the data of the research area**


```r
las <- list.files(envrmt$path_lvl0,
                  pattern = glob2rx("*.las"),
                  full.names = TRUE)
Lfile <- lidR::readLAS(las[1])
Lfile
```

```
## class        : LAS (v1.3 format 1)
## memory       : 2 Gb 
## extent       : 477500, 478217.5, 5631730, 5632500 (xmin, xmax, ymin, ymax)
## coord. ref.  : NA 
## area         : 0.55 kunits²
## points       : 26.35 million points
## density      : 47.7 points/units²
```

If all available points are included, the metrics at point *cloud level* can be
calculated. Using the function of the lidR-package .stdmetrics_z returns numerous
metrics including metrics which contain information of the verticle structure of
the research area. Using the function .stdmetrics_z only calculates these metrics
and can be used to safe computing power and isolate results. The .stdmetrics 
function can also be modified to isolate other information like intensity or 
percentage (just refer to the lidR manual or use ?.stdmetrics).



```r
clmetrics <- lidR::cloud_metrics(Lfile, func = lidR::.stdmetrics_z)
head(clmetrics)
```

```
## $zmax
## [1] 371.824
## 
## $zmean
## [1] 285.3682
## 
## $zsd
## [1] 23.40896
## 
## $zskew
## [1] 0.7478282
## 
## $zkurt
## [1] 3.411944
## 
## $zentropy
## [1] 0.760378
```

The return shows that the maximum height in the research area is around 380m, 
with a mean height of 285m. 

To calculate metrics at grid level grid_metrics can be used. It is also possible
to calculate single parameters using functions like mean(Z) or sd(Z) or self- written functions.


```r
# metrics at the grid level (res refers to the resolution of the cell, 1 = 10m & 50 = 50m)
grid <- lidR::grid_metrics(Lfile, ~mean(Z), res = 1)
lidR::plot(grid)
```

<img src="index_files/figure-html/grid metrics 1-1.png" width="672" />


```r
# use higher res to reduce computation time
grid_m <- lidR::grid_metrics(Lfile, .stdmetrics, res = 10) 
lidR::plot(grid_m)
```

<img src="index_files/figure-html/grid metrics 2-1.png" width="672" />

Grid metrics can also be used to calculate the density:


```r
dns <- lidR::grid_metrics(Lfile, ~length(Z)/1, res = 1)
lidR::plot(dns, main = "Point density [1m² cells]")
```

<img src="index_files/figure-html/grid metrics 3-1.png" width="672" />

## Discussion

Overall these metrics can be used for numerous applications also for microclimate prediction, especially when 
relationships are taken into account (like fauna/flora and canopy height etc.).
But this has to be done sensitive to the research area.  Density metrics are especially useful to predict microclimate parameters.

## References

Jean-Romain Roussel and David Auty (2021). Airborne LiDAR Data Manipulation and
  Visualization for Forestry Applications. R package version 3.2.2.
  https://cran.r-project.org/package=lidR
  
Jean-Romain Roussel, Tristan R.H. Goodbody and Piotr Tompalski (2015).The lidR package
  https://jean-romain.github.io/lidRbook/index.html

Roussel, J.R., Auty, D., Coops, N. C., Tompalski, P., Goodbody, T. R. H., Sánchez
  Meador, A., Bourdon, J.F., De Boissieu, F., Achim, A. (2020). lidR : An R package for
  analysis of Airborne Laser Scanning (ALS) data. Remote Sensing of Environment, 251
  (August), 112061. <doi:10.1016/j.rse.2020.112061>.

