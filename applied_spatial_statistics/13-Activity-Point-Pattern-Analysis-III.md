---
title: "Activity 6: Point Pattern Analysis III"
output: html_notebook
---

# Activity 6: Point Pattern Analysis III

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. List and explain two limitations of quadrat analysis.
2. What is clustering? What could explain a clustering in a set of events?
3. What is regularity? What could explain it?
4. Describe the concept of nearest neighbors.
5. What is a cumulative distribution function?

## Learning objectives

In this activity, you will:

1. Explore a dataset using distance-based approaches.
2. Compare the characteristics of different types of patterns.
3. Discuss ways to evaluate how confident you are that a pattern is random.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 5. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity. In addition to `tidyverse`, you will need `spatstat`, a package designed for the analysis of point patterns (you can learn about `spatstat` [here](https://cran.r-project.org/web/packages/spatstat/vignettes/getstart.pdf) and [here](http://spatstat.org/resources/spatstatJSSpaper.pdf)):

```r
library(tidyverse)
library(spatstat)
library(maptools) # Needed to convert `SpatialPolygons` into `owin` object
library(sf)
library(geog4ga3)
```

In the practice that preceded this activity, you learned about the concepts of intensity and density, about quadrats, and also how to create density maps. For this practice, you will use the data that you first encountered in Activity 4, that is, the business locations in Toronto.

Begin by reading the geospatial files, namely the city boundary of Toronto. You need the `sf` object, which will be converted into a `spatstat` window object:

```r
data("Toronto")
```

Convert the `sf` object to an `owin` object (via `SpatialPolygons`, hence `as(x, "Spatial")`:

```r
Toronto.owin <- as.owin(as(Toronto, "Spatial")) # Requires `maptools` package
```

Next the data that you will use in this activity needs to be loaded. Each dataframe is converted into a `ppp` object using the `as.ppp` function, again after extracting the coordinates of the events from the `sf` object:

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

If you inspect your workspace, you will see that the following `ppp` objects are there:

* `Fast_Food.ppp`
* `Gas_Stands.ppp`
* `Paez_Mart.ppp`

These are locations of fast food restaurants and gas stands in Toronto (data are from 2008). Paez Mart on the other hand is a project to cover Toronto with convenience stores. The points are the planned locations of the stores. 

You can check the contents of `ppp` objects by means of `summary`:

```r
summary(Fast_Food.ppp)
```

```
## Marked planar point pattern:  614 points
## Average intensity 9.681378e-07 points per square unit
## 
## Coordinates are given to 1 decimal place
## i.e. rounded to the nearest multiple of 0.1 units
## 
## Multitype:
##           frequency proportion    intensity
## Chicken          82  0.1335505 1.292953e-07
## Hamburger       209  0.3403909 3.295453e-07
## Pizza           164  0.2671010 2.585906e-07
## Sub             159  0.2589577 2.507067e-07
## 
## Window: polygonal boundary
## 10 separate polygons (no holes)
##             vertices        area relative.area
## polygon 1       4185 630935000.0      9.95e-01
## polygon 2        600   2536260.0      4.00e-03
## polygon 3        193    237206.0      3.74e-04
## polygon 4         28     26539.7      4.18e-05
## polygon 5         52    142793.0      2.25e-04
## polygon 6         67    158439.0      2.50e-04
## polygon 7         41     83470.2      1.32e-04
## polygon 8         30     42934.1      6.77e-05
## polygon 9         36     33866.6      5.34e-05
## polygon 10         8     11069.2      1.75e-05
## enclosing rectangle: [609550.5, 651611.8] x [4826375, 4857439] units
##                      (42060 x 31060 units)
## Window area = 634207000 square units
## Fraction of frame area: 0.485
```

Now that you have the data that you need in the right format, you are ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Calculate the event-to-event distances to nearest neighbors using the function `nndist()`. Do this for all fast food establishments (pooled) and then for each type of establishment (i.e, "Chicken", "Hamburger", "Pizza", "Sub").

2. (*)Create Stienen diagrams using the distance vectors obtained in Step 1.

3. (*)Plot the empirical G-function for all fast food establishments (pooled) and then for each type of establishment (i.e, "Chicken", "Hamburger", "Pizza", "Sub").

4. Discuss the diagrams that you created in Question 2 with a fellow student.

5. Is there evidence of clustering/regularity? 

6. How confident are you to make a decision whether the patterns are not random? What could you do to assess your confidence in making a decision whether the patterns are random? Explain.
