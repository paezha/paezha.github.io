---
title: "Activity 9: Area Data I"
output: html_notebook
---

# Activity 9: Area Data I

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. What is a key difference between area data and point data?
2. What is a choropleth map?
3. What is a cartogram?
4. What are the advantages and disadvantages of these mapping techniques?

## Learning objectives

In this activity, you will:

1. Create choroplet maps using census data.
2. Think about possible underlying process that could explain the pattern.
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

In addition to `tidyverse`, you will need `sf`, a package that implements simple features in R (you can learn more about this package [here](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)):

```r
library(tidyverse)
library(sf)
library(cartogram)
library(geog4ga3)
```

In the practice that preceded this activity, you learned about the area data and visualization techniques for area data.

Begin by loading the data that you will use in this activity:

```r
data("Hamilton_CT")
```

This is an `sf` object with census tracts and selected demographic variables for the Hamilton CMA in Canada.

You can obtain new (calculated) variables as follows. For instance, to obtain the proportion of residents who are between 20 and 34 years old, and between 35 and 49:

```r
Hamilton_CT <- Hamilton_CT %>%
  mutate(Prop20to34 = (AGE_20_TO_24 + 
                         AGE_25_TO_29 + 
                         AGE_30_TO_34)/POPULATION, 
         Prop35to49 = (AGE_35_TO_39 + 
                         AGE_40_TO_44 + 
                         AGE_45_TO_49)/POPULATION)
```

You are ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Create choropleth maps for the proportion of the population who are 20 to 34 years old, 35 to 49 years old, 50 to 65 years old, and 65 and older.

2. (*)Create cartograms for the proportion of the population who are 20 to 34 years old, 35 to 49 years old, 50 to 65 years old, and 65 and older.

3. (*)Change the scheme and colors of your maps to obtain maps with 2 classes/colors, 5 classes/colors, and 10 classes/colors. You can check different color palettes [in the documentation of `ggplot2`](https://ggplot2.tidyverse.org/reference/scale_brewer.html). Which scheme is more informative? What colors looked better to you?

4. Show your maps to a fellow student. What patterns do you notice in the distribution of population by age in Hamilton? Do you think the distribution of the population by age is random, or not random?

5. Devise a rule to decide whether the pattern observed in a choropleth map is random.
