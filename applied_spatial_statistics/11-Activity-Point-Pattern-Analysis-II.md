---
title: "Activity 5: Point Pattern Analysis II"
output: html_notebook
---

# Activity 5: Point Pattern Analysis II

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. How does the quadrat-based test of independence respond to a small number of quadrats?
2. How does the quadrat-based test of independence respond to a large number of quadrats?
3. What are the limitations of quadrat analysis?
4. What is a kernel function?
5. How does the bandwidth affect a kernel function?

## Learning objectives

In this activity, you will:

1. Explore a dataset using quadrats and kernel density.
2. Experiment with different parameters (number/size of kernels and bandwidths).
3. Discuss the impacts of selecting different parameters.
4. Hypothesize about the underlying spatial process based on your analysis.

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

Note that `ls()` lists all objects currently on the worspace.

Load the libraries you will use in this activity. In addition to `tidyverse`, you will need `spatstat`, a package designed for the analysis of point patterns (you can learn about `spatstat` [here](https://cran.r-project.org/web/packages/spatstat/vignettes/getstart.pdf) and [here](http://spatstat.org/resources/spatstatJSSpaper.pdf)):

```r
library(tidyverse)
library(spatstat)
library(geog4ga3)
```

In the practice that preceded this activity, you learned about the concepts of intensity and density, about quadrats, and also how to create density maps. 
Begin by loading the data that you will use in this activity:

```r
data("bear_df")
```

This dataset was sourced from the Scandinavia Bear Project, a Swedish-Noruegian collaboration that aims to study the ecology of brown bears, to provide decision makers with evidence to support bear management, and to provide information regarding bears to the public. You can learn more about this project [here](http://bearproject.info/about-the-project/).

The project involves tagging bears with GPS units, so that their movements can be tracked.

The dataset includes coordinates of one bear's movement over a period of several weeksin 2004. The dataset was originally taken from the `adehabitatLT` package but was somewhat simplified for this activity. Instead of full date and time information, the point pattern is marked more simply as "Day Time" and "Night Time", to distinguish between diurnal and nocturnal activity of the bear.

Summarize the contents of this dataframe:

```r
summary(bear_df)
```

```
##        x                y                  marks    
##  Min.   :515743   Min.   :6812138   Day Time  :502  
##  1st Qu.:518995   1st Qu.:6813396   Night Time:498  
##  Median :519526   Median :6816724                   
##  Mean   :519321   Mean   :6816474                   
##  3rd Qu.:519983   3rd Qu.:6818111                   
##  Max.   :522999   Max.   :6821440
```

The Min. and Max. of `x` and `y` give us an idea of the region covered by this dataset. We can use these values to approximate a window for the region (as an experiment, you could try changing these values to create regions of different sizes):

```r
W <- owin(xrange = c(515000, 523500), yrange = c(6812000, 6822000))
```

Next, we can convert the dataframe into a `ppp`-class object suitable for analysis using the package `spatstat`:

```r
bear.ppp <- as.ppp(bear_df, W = W)
```

You can check the contents of the `ppp` object by means of `summary`:

```r
summary(bear.ppp)
```

```
## Marked planar point pattern:  1000 points
## Average intensity 1.176471e-05 points per square unit
## 
## Coordinates are given to 1 decimal place
## i.e. rounded to the nearest multiple of 0.1 units
## 
## Multitype:
##            frequency proportion    intensity
## Day Time         502      0.502 5.905882e-06
## Night Time       498      0.498 5.858824e-06
## 
## Window: rectangle = [515000, 523500] x [6812000, 6822000] units
##                     (8500 x 10000 units)
## Window area = 8.5e+07 square units
```

Now that you have loaded the dataframe and converted to a `ppp` object, you are ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Analyze the point pattern for the movements of the bear using quadrat and kernel density methods. Experiment with different quadrat sizes and kernel bandwidths. 

2. Explain your choice of parameters (quadrat sizes and kernel bandwidths) to a fellow student.

3. Decide whether these patterns are random, and support your decision.

4. Do you see differences in the activity patterns of the bear by time of day? What could explain those differences, if any? 

5. Discuss the limitations of your conclusions, and of quadrat/kernel (density-based) approaches more generally.
