---
title: "Activity 16: Spatially Continuous Data II"
output: html_notebook
---

# Activity 16: Spatially Continuous Data II

## Practice questions

Answer the following questions:

1. What is a confidence interval?
2. How does a confidence interval vary with the level of significance?
3. Residuals of trend surface analysis are always spatially independent, true or false.
4. Estimates of the prediction error $\hat{\epsilon}_p$ can be obtained from trend surface analysis, true or false. Explain.
5. In your own words describe the concepts of accuracy and precision in spatial interpolation.

## Learning objectives

In this activity, you will:

1. Use trend surface analysis to interpolate a field.
2. Calculate the degree of uncertainty.
3. Think about the role of residual autocorrelation in interpolation.

## Suggested reading

- Bailey TC and Gatrell AC [-@Bailey1995] Interactive Spatial Data Analysis, Chapters 5 and 6. Longman: Essex.
- Bivand RS, Pebesma E, and Gomez-Rubio V [-@Bivand2008] Applied Spatial Data Analysis with R, Chapter 8. Springer: New York.
- Brunsdon C and Comber L [-@Brunsdon2015R] An Introduction to R for Spatial Analysis and Mapping, Chapter 6, Sections 6.7 and 6.8. Sage: Los Angeles.
- Isaaks EH and Srivastava RM  [-@Isaaks1989applied] An Introduction to Applied Geostatistics, **CHAPTERS**. Oxford University Press: Oxford.
- O'Sullivan D and Unwin D [-@Osullivan2010] Geographic Information Analysis, 2nd Edition, Chapters 9 and 10. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity (load other packages as appropriate). 

```r
library(tidyverse)
library(spatstat)
library(spdep)
library(geog4ga3)
```

Load the data that you will use in this activity:

```r
data("aquifer")
```

The data is a set of piezometric head (watertable pressure) observations of the Wolfcamp Aquifer in Texas (https://en.wikipedia.org/wiki/Hydraulic_head). Measures of pressure can be used to infer the flow of underground water, since water flows from high to low pressure areas.

These data were collected to evaluate potential flow of contamination related to a high level toxic waste repository in Texas. The Deaf Smith county site in Texas was one of three potential sites proposed for this repository. Beneath the site is a deep brine aquifer known as the Wolfcamp aquifer that may serve as a conduit of contamination leaking from the repository.

The data set consists of 85 georeferenced measurements of piezometric head. Possible applications of interpolation are to determine sites at risk and to quantify uncertainty of the interpolated surface, to evaluate the best locations for monitoring stations.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Estimate a trend surface for the dataset experimenting with different polynomials.

2. (*)Create an interpolation grid, and use the function `predict` to interpolate the field using your chosen model. Plot the interpolated field using a method of your choice (e.g., `ggplot2`, `plot_ly()` for 3D plotting, etc.)

3. Which polynomial in your experiments provides the best fit (hint: consider the coefficient of multiple determination $R^2$ and the standard error, in addition to the significance of the parameters). Justify your choice of a polynomial.

3. Inspect the confidence intervals of your chosen model (these are an output of `predict`).

4. Inspect the residuals of the model. Are they spatially random? If not, what would be the implications for spatial interpolation?
