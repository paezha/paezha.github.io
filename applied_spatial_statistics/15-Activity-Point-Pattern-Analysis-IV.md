---
title: "Activity 7: Point Pattern Analysis IV"
output: html_notebook
---

# Activity 7: Point Pattern Analysis IV

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. What does the $\hat{G}$-function measure?
2. What does the $\hat{F}$-function measure?
3. How do these two functions relate to one another?
4. Describe the intuition behind the $\hat{K}$-function. 
5. How does the $\hat{K}$-function capture patterns at multiple scales?

## Learning objectives

In this activity, you will:

1. Explore a dataset using single scale distance-based techniques.
2. Explore the characteristics of a point pattern at multiple scales.
3. Discuss ways to evaluate how confident you are that a pattern is random.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 5. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in `R` to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity. In addition to `tidyverse`, you will need `spatstat`, a package designed for the analysis of point patterns (you can learn about `spatstat` [here](https://cran.r-project.org/web/packages/spatstat/vignettes/getstart.pdf) and [here](http://spatstat.org/resources/spatstatJSSpaper.pdf)):

```r
library(tidyverse)
library(spatstat)
library(maptools) # Needed to convert `SpatialPolygons` into `owin`-class object
library(sf)
library(geog4ga3)
```

For this activity, you will use the same datasets that you used in Activity 6, including the geospatial files for Toronto's city boundary:

```r
data("Toronto")
```

Convert the `sf` object to an `owin` object (via `SpatialPolygons`, hence `as(x, "Spatial")`:

```r
Toronto.owin <- as.owin(as(Toronto, "Spatial")) # Requires `maptools` package
```

Next, load the data that you will use in this activity. Each dataframe is converted into a `ppp` object using the `as.ppp` function, again after extracting the coordinates of the events from the `sf` object:

```r
data("Fast_Food")
Fast_Food.ppp <- as.ppp(st_coordinates(Fast_Food), W = Toronto.owin)
# Add the classes of fast food to the ppp object:
marks(Fast_Food.ppp) <- Fast_Food$Class

data("Gas_Stands")
Gas_Stands.ppp <- as.ppp(st_coordinates(Gas_Stands), W = Toronto.owin)

data("Paez_Mart")
Paez_Mart.ppp <- as.ppp(st_coordinates(Paez_Mart), W = Toronto.owin)
```

Now that you have the datasets in the appropriate format, you are ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Plot the empirical $\hat{F}$-function for all fast food establishments (pooled) and then for each type of establishment separately (i.e, "Chicken", "Hamburger", "Pizza", "Sub").

2. (*)Plot the empirical $\hat{K}$-function for all fast food establishments (pooled) and then for each type of establishment (i.e, "Chicken", "Hamburger", "Pizza", "Sub").

3. Discuss your results with a fellow student. Is there evidence of clustering/regularity?

4. What can you say about patterns at multiple-scales based on the graphs above?

5. How confident are you to make a decision whether the patterns are not random? What could you do to assess your confidence in making a decision whether the patterns are random? Explain.
