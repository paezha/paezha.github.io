---
title: "Activity 13: Area Data V"
output: html_notebook
---

# Activity 13: Area Data V

## Practice questions

Answer the following questions:

1. Explain the main assumptions for linear regression models.
2. How is Moran's $I$ used as a diagnostic in regression analysis?
3. Residual spatial autocorrelation is symptomatic of what issues in regression analysis?
4. What does it mean for a model to be linear in the coefficients?
5. What is the purpose of transforming variables for regression analysis?

## Learning objectives

In this activity, you will:

1. Explore a spatial dataset.
2. Conduct linear regression analysis.
3. Conduct diagnostics for residual spatial autocorrelation.
4. Propose ways to improve your analysis.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 7. John Wiley & Sons: New Jersey. 

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity. In addition to `tidyverse`, you will need `sf` and `geog4ga3`:

```r
library(tidyverse)
library(sf)
library(spdep)
library(geog4ga3)
```

Begin by loading the data files you will use in this activity:

```r
data("HamiltonDAs")
data("trips_by_mode")
data("travel_time_car")
```

`HamiltonDAs` are the Dissemination Areas for Hamilton CMA, which coincide with the Traffic Analysis Zones (TAZ) of the Transportation Tomorrow Survey of 2011. The dataframe `trips_by_mode` includes the number of trips by mode of transportation by TAZ (equivalently DA), as well as other useful information from the 2011 census for Hamilton CMA. Finally, the dataframe `travel_time_car` includes the travel distance/time from TAZ/DA centroids to Jackson Square in downtown Hamilton.

The data for this activity were retrieved from the 2011 Transportation Tomorrow Survey [TTS](http://www.transportationtomorrow.on.ca/), the periodic travel survey of the Greater Toronto and Hamilton Area, as well as data from the 2011 Canadian Census [Census Program](http://www12.statcan.gc.ca/census-recensement/index-eng.cfm).

Before beginning the activity, join the information on trips and travel time to the `sf` object. Note that to complete the join, the identifier (in this case `GTA06`) must be in the same format in both data frames:

```r
travel_time_car$GTA06 <- factor(travel_time_car$GTA06)

# Travel time
HamiltonDAs <- left_join(HamiltonDAs, travel_time_car, by = "GTA06")
# Trips by mode
HamiltonDAs <- left_join(HamiltonDAs, trips_by_mode, by = "GTA06")
```

The analysis will be based on travel by car in the Hamilton CMA. Calculate the proportion of trips by car by TAZ:

```r
HamiltonDAs <- mutate(HamiltonDAs, Auto_driver.prop = Auto_driver / (Auto_driver + Cycle + Walk))
```

Note that the proportion of people who traveled by car as passengers are not included in the denominator of the proportion! This is because every trip as a passenger is already included in trips with one driver.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Examine your dataframe. What variables are included? Are there any missing values?

2. (*)Map the variable `Auto_driver.prop`, and use Moran's I to test for spatial autocorrelation. 

3. (*)Estimate regression model using the variables `Pop_Density` and travel time in `minutes`.

4. What does the analysis of autocorrelation in point 2* tell you about `Auto_driver.prop`? Would you say that autocorrelation in this variable is a sign that autocorrelation will be an issue in regression analysis? Why or why not?

5. Discuss the model you estimated in point 3. Next, examine its residuals. Would you say that they are spatially random/independent?

6. Propose ways to improve your model.
