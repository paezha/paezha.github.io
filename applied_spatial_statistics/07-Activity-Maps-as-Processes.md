---
title: "Activity 3: Maps as Processes"
output: html_notebook
---

# Activity 3: Maps as Processes

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice Questions

Answer the following questions:

1. What is a Geographic Information System?
2. What distinguishes a statistical map from other types of mapping techniques?
3. What is a null landscape?

## Learning Objectives

In this activity, you will:

1. Simulate landscapes using various types of processes.
2. Discuss the difference between random and non-random landscapes.
3. Think about ways to decide whether a landscape is random.

## Suggested Reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 4. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the worspace.

Load the libraries you will use in this activity:

```r
library(tidyverse)
```

In the practice that preceded this activity, you learned how to simulate null landscapes and spatial processes. 

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to organize data, create a plot, and so on in support of analysis and interpretation. These tasks are indicated by a star (*).

1. (*)Simulate and plot a landscape using a random, stochastic, or deterministic process. It is your choice whether to simulate a point pattern or a continuous variable. Identify the key parameters that make a landscape more or less random. Repeat several times changing those parameters.

2. Recreate any one of the maps you created and share the map with a fellow student. Ask them to guess whether the map is random or non-random.

3. Repeat step 2 several times (depending on time, between two and four times).

4. Propose one or more ways to decide whether a landscape is random, and explain your reasoning. The approach does not need to be the same for point patterns and continuous variables!
