---
title: "Activity 10: Area Data II"
output: html_notebook
---

# Activity 10: Area Data II

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. List and describe two criteria to define proximity in area data analysis.
2. What is a spatial weights matrix?
3. Why do spatial weight matrices have zeros in the main diagonal?
4. How is a spatial weights matrix row-standardized?
4. Write the spatial weights matrices for the sample systems in Figures \@ref{fig:simple-areal-system-i} and \@ref{fig:simple-areal-system-ii}. Explain the criteria used to do so.

<div class="figure">
<img src="Area-Data-II-Activity-Figure-1.jpg" alt="\label{fig:simple-areal-system-i}Sample areal system 1" width="480" />
<p class="caption">(\#fig:simple-areal-system-i)\label{fig:simple-areal-system-i}Sample areal system 1</p>
</div>


<div class="figure">
<img src="Area-Data-II-Activity-Figure-2.jpg" alt="\label{fig:simple-areal-system-ii}Sample areal system 2" width="480" />
<p class="caption">(\#fig:simple-areal-system-ii)\label{fig:simple-areal-system-ii}Sample areal system 2</p>
</div>

## Learning objectives

In this activity, you will:

1. Create spatial weights matrices.
2. Calculate the spatial moving average of a variable.
2. Create scatterplots of a variable and its spatial moving average.
3. Think about ways to decide whether a landscape is random when working with area data.

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

Load the libraries you will use in this activity. 

In addition to `tidyverse`, you will need `sf`, a package that implements simple features in R (you can learn about `sf` [here](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)) and `spdep`, a package that implements several spatial statistical methods (you can learn more about it [here](https://cran.r-project.org/web/packages/spdep/index.html)):

```r
library(tidyverse)
library(spdep)
library(sf)
library(geog4ga3)
library(plotly)
```

In the practice that preceded this activity, you learned about the area data and visualization techniques for area data.

Begin by loading the data that you will use in this activity:

```r
data(Hamilton_CT)
```

This is a `sf` object with census tracts and selected demographic variables for the Hamilton CMA in Canada.

You can obtain new (calculated) variables as follows. For instance, to obtain the proportion of residents who are between 20 and 34 years old, and between 35 and 49:

```r
Hamilton_CT <- Hamilton_CT %>%
  mutate(Prop20to34 = (AGE_20_TO_24 + AGE_25_TO_29 + AGE_30_TO_34)/POPULATION,
         Prop35to49 = (AGE_35_TO_39 + AGE_40_TO_44 + AGE_45_TO_49)/POPULATION)
```

You can also convert the `sf` object into a `SpatialPolygonsDataFrame` object for use with the `spdedp` package:

```r
Hamilton_CT.sp <- as(Hamilton_CT, "Spatial")
```

You are now ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Create a spatial weights matrix for the census tracts in the Hamilton CMA. Use adjacency as your criterion for proximity.

2. (*)Calculate the spatial moving average for the following two variables: 1) proportion of the population who are 20 to 34 years old; and 2) proportion of the population who are 65 and older.

3. (*)Append the spatial moving averages to your dataframe.

4. (*)Choose one age group and create a scatterplot of the proportion of population in that group versus its spatial moving average. (Hint: if you create the scatterplot using `ggplot2` you can add the 45 degree line by means of `geom_abline(slope = 1, intercept = 0)`).

5. Show your scatterplot of the population versus its spatial moving average to a fellow student. What is the meaning of the 45 degree line in this plot?

6. Create a null-landscape by scrambling the values of your variable. For instance, you can use the variable `prop20to34` to generate a null landscape as follows:


```r
Hamilton_CT$Null_1 <- sample(Hamilton_CT$Prop20to34)
```

7. Calculate the spatial moving average of your null landscape, and create a scatterplot just like you did for your variable. How is this scatterplot different?
