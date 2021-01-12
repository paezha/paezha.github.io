---
title: "Activity 14: Area Data VI"
output: html_notebook
---

# Activity 14: Area Data VI

## Practice questions

Answer the following questions:

1. Describe and discuss the possible sources of autocorrelation in the residuals of a model.
2. List possible corrective/remedial actions when residual autocorrelation is detected.
3. Under which situations is a Spatial Error Model an adequate modeling strategy? 

## Learning objectives

In this activity, you will:

1. Explore a dataset with area data using visualization as appropriate.
2. Discuss a process that might explain any pattern observed from the data.
3. Conduct a modeling exercise using appropriate techniques. Justify your modeling decisions.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 5. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* This R markdown notebook.

* A dataset of your choice.

It is good practice to clear the workspace to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity (load other packages as appropriate). 

```r
library(geog4ga3)
library(sf)
library(spatstat)
library(spdep)
library(tidyverse)
```

Choose one of the following datasets.

### New York leukemia data


```r
data("nyleukemia")
```

A `SpatialPolygonsDataFrame` that contains the following variables:

* AREANAME name of census tract
* AREAKEY unique FIPS code for each tract
* POP8 population size (1980 U.S. Census)
* TRACTCAS number of cases of leukemia (1978-1982)
* PROPCAS proportion of cases per tract
* PCTOWNHOME percentage of people in each tract owning their own home
* PCTAGE65P percentage of people in each tract aged 65 or more
* Z transformed proportions
* AVGIDIST average distance between centroid and TCE sites
* PEXPOSURE "exposure potential": inverse distance between each census tract centroid and the nearest TCE site, IDIST, transformed via log(100*IDIST)

This can be converted to a simple features object as follows:

```r
nyleukemia.sf <- st_as_sf(nyleukemia)
```

### Pennsylvania lung cancer


```r
data("pennlc")
```

A `SpatialPolygonsDataFrame` that contains the following variables:

* county: Name of the county
* cases: Number of cases of lung cancer
* population: Population by county
* rate: Lung cancer rate by county
* smoking: Smoking rate by county
* cancer_ rate: Lung cancer rate by county (%)
* smoking_rate: Smoking rate by county  (%)

This can be converted to a simple features object as follows:

```r
pennlc.sf <- st_as_sf(pennlc)
```

## Activity

1. Partner with a fellow student to analyze the chosen dataset.

2. Visualize/explore the dataset using appropriate tools.

3. Analyze your dataset by means of regression modeling. Which should be the dependent variable in your dataset? Why?

4. Discuss the results of your analysis, including possible limitations, and possible ways to improve it (e.g., what additional variables would you like to use?)
