---
title: "11 Area Data II"
output: html_notebook
---

# Area Data II

*NOTE*: You can download the source files for this book from [here](https://github.com/paezha/Spatial-Statistics-Course). The source files are in the format of R Notebooks. Notebooks are pretty neat, because the allow you execute code within the notebook, so that you can work interactively with the notes.

In last chapter and activity, you learned about _area data_ and practiced some visualization techniques for spatial data of this type, specifically choropleth maps and cartograms. You also thought about rules to decide whether a mapped variable displayed a spatially random distribution of values. 

If you wish to work interactively with this chapter you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

## Learning Objectives

In this practice, you will learn about:

1. The concept of proximity for area data.
2. How to formalize the concept of proximity: spatial weights matrices.
3. How to create spatial weights matrices in R.
4. The use of spatial moving averages.
5. Other criteria for coding proximity.

## Suggested Readings

- Bailey TC and Gatrell AC (1995) Interactive Spatial Data Analysis, Chapter 7. Longman: Essex.
- Bivand RS, Pebesma E, and Gomez-Rubio V (2008) Applied Spatial Data Analysis with R, Chapter 9. Springer: New York.
- Brunsdon C and Comber L (2015) An Introduction to R for Spatial Analysis and Mapping, Chapter 7. Sage: Los Angeles.
- O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 7. John Wiley & Sons: New Jersey.

## Preliminaries

As usual, it is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the worspace.

Load the libraries you will use in this activity:

```r
library(tidyverse)
library(sf)
library(plotly)
library(spdep)
library(geog4ga3)
```

Read the data to be used in this chapter. The data is an object of class `sf` (simple feature) with the census tracts of Hamilton CMA in Canada, and a selection of demographic variables:

```r
data(Hamilton_CT)
```

You can quickly verify the contents of the dataframe by means of `summary`:

```r
summary(Hamilton_CT)
```

```
##        ID               AREA             TRACT             POPULATION   
##  Min.   : 919807   Min.   :  0.3154   Length:188         Min.   :    5  
##  1st Qu.: 927964   1st Qu.:  0.8552   Class :character   1st Qu.: 2639  
##  Median : 948130   Median :  1.4157   Mode  :character   Median : 3595  
##  Mean   : 948710   Mean   :  7.4578                      Mean   : 3835  
##  3rd Qu.: 959722   3rd Qu.:  2.7775                      3rd Qu.: 4692  
##  Max.   :1115750   Max.   :138.4466                      Max.   :11675  
##   POP_DENSITY         AGE_LESS_20      AGE_20_TO_24    AGE_25_TO_29  
##  Min.   :    2.591   Min.   :   0.0   Min.   :  0.0   Min.   :  0.0  
##  1st Qu.: 1438.007   1st Qu.: 528.8   1st Qu.:168.8   1st Qu.:135.0  
##  Median : 2689.737   Median : 750.0   Median :225.0   Median :215.0  
##  Mean   : 2853.078   Mean   : 899.3   Mean   :253.9   Mean   :232.8  
##  3rd Qu.: 3783.889   3rd Qu.:1110.0   3rd Qu.:311.2   3rd Qu.:296.2  
##  Max.   :14234.286   Max.   :3285.0   Max.   :835.0   Max.   :915.0  
##   AGE_30_TO_34     AGE_35_TO_39     AGE_40_TO_44     AGE_45_TO_49  
##  Min.   :   0.0   Min.   :   0.0   Min.   :   0.0   Min.   :  0.0  
##  1st Qu.: 135.0   1st Qu.: 145.0   1st Qu.: 170.0   1st Qu.:203.8  
##  Median : 195.0   Median : 200.0   Median : 230.0   Median :282.5  
##  Mean   : 228.2   Mean   : 239.6   Mean   : 268.7   Mean   :310.6  
##  3rd Qu.: 281.2   3rd Qu.: 280.0   3rd Qu.: 325.0   3rd Qu.:385.0  
##  Max.   :1320.0   Max.   :1200.0   Max.   :1105.0   Max.   :880.0  
##   AGE_50_TO_54    AGE_55_TO_59    AGE_60_TO_64  AGE_65_TO_69  
##  Min.   :  0.0   Min.   :  0.0   Min.   :  0   Min.   :  0.0  
##  1st Qu.:203.8   1st Qu.:175.0   1st Qu.:140   1st Qu.:115.0  
##  Median :280.0   Median :240.0   Median :220   Median :157.5  
##  Mean   :300.3   Mean   :257.7   Mean   :229   Mean   :174.2  
##  3rd Qu.:375.0   3rd Qu.:325.0   3rd Qu.:295   3rd Qu.:221.2  
##  Max.   :740.0   Max.   :625.0   Max.   :540   Max.   :625.0  
##   AGE_70_TO_74    AGE_75_TO_79     AGE_80_TO_84     AGE_MORE_85    
##  Min.   :  0.0   Min.   :  0.00   Min.   :  0.00   Min.   :  0.00  
##  1st Qu.: 90.0   1st Qu.: 68.75   1st Qu.: 50.00   1st Qu.: 35.00  
##  Median :130.0   Median :100.00   Median : 77.50   Median : 70.00  
##  Mean   :139.7   Mean   :118.32   Mean   : 95.05   Mean   : 87.71  
##  3rd Qu.:180.0   3rd Qu.:160.00   3rd Qu.:120.00   3rd Qu.:105.00  
##  Max.   :540.0   Max.   :575.00   Max.   :420.00   Max.   :400.00  
##           geometry  
##  POLYGON      :188  
##  epsg:26917   :  0  
##  +proj=utm ...:  0  
##                     
##                     
## 
```

## Proximity in Area Data

In the earlier part of the text, when working with point data, the spatial relationships among events (their proximity) were more or less unambiguously given by their relative location, or more precisely by their distance. Hence, we had quadrat-based techniques (relative location with respect to a grid) and distance-based techniques (event-to-event and point-to-event).

In the case of area data, spatial proximity can be represented in more ways, given the characteristics of areas. In particular, an area contains an infinite number of points, and measuring distance between two areas leads to many possible results, depending on which pairs of points within two zones are used to measure the distance. 

Consider the simple systems shown in Figure \@ref{fig:simple-zoning-system}. Which of zones $A_2$, $A_3$, and $A_4$ is more _proximate_ to $A_1$?

<div class="figure">
<img src="Area-Data-II-Figure-1.jpg" alt="\label{fig:simple-zoning-system}Simple zoning system" width="480" />
<p class="caption">(\#fig:simple-zoning-system)\label{fig:simple-zoning-system}Simple zoning system</p>
</div>

If points are selected in such a way that they are on the overlapping edges of two contiguous areas, the distance between the two areas clearly is zero, and they must be proximate.

This criterion to define proximity is called _adjacency_. Adjacency means that two zones share a common edge. This is conventionally called the _rook_ criterion, after chess, in which the piece called the rook can move only orthogonally. The rook criterion, however, would dictate that zones $A_2$ and $A_6$ are not proximate, despite being closer than $A_2$ and $A_3$.

When this criterion is expanded to allow contact at a single point between zones (say, the corner between $A_2$ and $A_6$), the adjacency criterion is called _queen_, again, for the chess piece that moves both orthogonally and diagonally.

If we accept adjacency as a reasonable way of expressing relationships of proximity between areas, what we need is a way of coding relationships of adjacency in a way that is convenient and amenable to manipulation for data analysis.

One of the most widely used tools to code proximity in area data is the _spatial weights matrix_.

## Spatial Weights Matrices

A spatial weights matrix is an arrangement of values (or weights) for all pairs of zones in a system. For instance, in a zoning system such as shown in Figure 1, with 6 zones, there will be $6 \times 6$ such weights. The weights are organized by rows, in such a way that each zone has a corresponding row of weights. For example, zone $A_1$ in Figure 1 has weights:
$$
w_{1\cdot} = [w_{11}, w_{12}, w_{13}, w_{14}, w_{15}, w_{16}]
$$

The values of the weights depend on the adjacency criterion adopted. The simplest coding scheme is when we assign a value of 1 to pairs of zones that are adjacent, and a value of 0 to pairs of zones that are not. 

Lets formalize the two criteria mentioned above:

* Rook criterion

$$
w_{ij}=\bigg\{\begin{array}{l l}
1\text{ if } A_i \text{ and } A_j \text{ share an edge}\\
0\text{ otherwise}\\
\end{array}
$$

* Queen criterion

$$
w_{ij}=\bigg\{\begin{array}{l l}
1\text{ if } A_i \text{ and } A_j \text{ share an edge or a vertex}\\
0\text{ otherwise}\\
\end{array}
$$

If queen adjacency is used, the weights for zone $A_6$ are as follows:
$$
w_{6\cdot} = [0, 1, 0, 1, 1, 0].
$$

As you can see, the adjacent areas from the perspective of $A_6$ are $A_4$ and $A_5$ (by virtue of sharing an edge), and $A_2$ (by virtue of sharing a vertex). These three areas receive weights of 1. On the other hand, $A_1$ and $A_3$ are not adjacent, and therefore receive a weight of zero. **Notice how the weight $w_{66}$ is set to zero**. By convention, an area is not its own neighbor!

The set of weights above define the _neighborhood_ of $A_6$.

The spatial weights matrix for the whole system in Figure 1 is as follows:
$$
\textbf{W}=\left (\begin{array}{c c c c c c}
0 & 1 & 1 & 1 & 0 & 0\\
1 & 0 & 0 & 1 & 1 & 1\\
1 & 0 & 0 & 1 & 0 & 0\\
1 & 1 & 1 & 0 & 1 & 1\\
0 & 1 & 0 & 1 & 0 & 1\\
0 & 1 & 0 & 1 & 1 & 0\\
\end{array} \right).
$$

Compare the matrix to the zoning system. The spatial weights matrix has the following properties:

1. The main diagonal elements are all zeros (no area is its own neighbor).
2. Each zone has a row of weights in the matrix: row number one corresponds to $A_1$, row number two corresponds to $A_2$, and so on.
3. Likewise, each zone has a _column_ of weights.
4. The sum of all values in a row gives the _total_ number of neighbors for an area. That is:
$$
\text{The total number of neighbors of } A_i \text{ is given by: }\sum_{j=1}^{n}{w_{ij}}
$$

The spatial weights matrix is often processed to obtain a _row-standardized_ spatial weights matrix. This procedure consists of dividing all weights by the sum of their corresponding row (i.e., by the total number of neighbors), as follows:
$$
w_{ij}^{st}=\frac{w_{ij}}{\sum_{j=1}^n{w_{ij}}}
$$

The row-standardized weights matrix for the system in Figure 1 is:
$$
\textbf{W}^{st}=\left (\begin{array}{c c c c c c}
0 & 1/3 & 1/3 & 1/3 & 0 & 0\\
1/4 & 0 & 0 & 1/4 & 1/4 & 1/4\\
1/2 & 0 & 0 & 1/2 & 0 & 0\\
1/5 & 1/5 & 1/5 & 0 & 1/5 & 1/5\\
0 & 1/3 & 0 & 1/3 & 0 & 1/3\\
0 & 1/3 & 0 & 1/3 & 1/3 & 0\\
\end{array} \right).
$$

The row-standardized spatial weights matrix has the following properties:

1. Each weight now represents the proportion of a neighbor out of the total of neighbors. For instance, since the total of neighbors of $A_1$ is 3, each neighbor contributes 1/3 to that total.

2. The sum of all weights over a row equals 1, or 100% of all neighbors for that zone.

## Creating Spatial Weights Matrices in R

Coding spatial weights matrices by hand is a tedious and error-prone process. Fortunately, functions to generate them exist in R. The package `spdep`, for instance, has a number of useful utilities for working with spatial weights matrices.

The first step to create a spatial weights matrix is to find the neighbors (i.e., areas adjacent to) for each area. The function `poly2nb` is used for this. The input argument is a `SpatialPolygonDataFrame`. This means that our `sf` object needs to be converted into a `SpatialPolygonDataFrame`:

```r
Hamilton_CT.sp <- as(Hamilton_CT, "Spatial")
```

The following finds the neighbors (note that the default adjacency criterion is queen):

```r
Hamilton_CT.nb <- poly2nb(pl = Hamilton_CT.sp, queen = TRUE)
```

The value (output) of the function is an object of class `nb`:

```r
class(Hamilton_CT.nb)
```

```
## [1] "nb"
```

The function `summary` applied to an object of this class gives some useful information about the neighbors, including the number of areas in this system (188), the total number of neighbors (1180), and the percentage of neighbors out of all pairs of areas (3.34%). Other information includes the distribution of neighbors (3 zones have two neighbors, 8 zones have three neighbors, 22 zones have four neighbors, and so on): 

```r
summary(Hamilton_CT.nb)
```

```
## Neighbour list object:
## Number of regions: 188 
## Number of nonzero links: 1180 
## Percentage nonzero weights: 3.338615 
## Average number of links: 6.276596 
## Link number distribution:
## 
##  2  3  4  5  6  7  8  9 10 11 12 14 
##  3  8 22 32 35 45 30  6  1  1  4  1 
## 3 least connected regions:
## 174 175 188 with 2 links
## 1 most connected region:
## 33 with 14 links
```

The `nb` object is a list that contains the neighbors for each area. For instance, the neighbors of census tract 5370001.01 (the first tract in the dataframe) are the following tracts:

```r
Hamilton_CT$TRACT[Hamilton_CT.nb[[1]]]
```

```
## [1] "5370120.02" "5370122.01" "5370122.02" "5370124.00" "5370142.01"
## [6] "5370133.01" "5370130.03"
```

The list of neighbors can be converted into a list of entries in a spatial weights matrix W by means of the function `nb2list2` (for "neighbors to W in list form"):

```r
Hamilton_CT.w <- nb2listw(Hamilton_CT.nb)
```

We can visualize the neighbors (adjacent) areas:

```r
plot(Hamilton_CT.sp, border = "gray")
plot(Hamilton_CT.nb, coordinates(Hamilton_CT.sp), col = "red", add = TRUE)
```

<img src="20-Area-Data-II_files/figure-html/unnamed-chunk-11-1.png" width="672" />

## Spatial Moving Averages

The spatial weights matrix, and in particular its row-standardized version, is useful to calculate a spatial statistic, the _spatial moving average_.

The spatial moving average is a variation on the mean statistic. Recall that the mean is calculated as the sum of all relevant values divided by the number of values summed. In the case of spatial data, the mean is what we would call a _global_ statistics, since it is calculated using all data for a region:
$$
\overline{x}=\frac{1}{n}\sum_{j=1}^{n}{x_j}
$$
where $\overline{x}$ (read x-bar) is the mean of all values of x.

A spatial moving average is calculated in the same way, but for each area, and based only on the values of proximate areas:
$$
\overline{x_i}=\frac{1}{n_i}\sum_{j\in N(i)}{x_j}
$$
where $n_i$ is the number of neighbors of $A_i$, and the sum is only for $x_j$ that are in the neighborhood of i ($j\in N(i)$ is read "j in the neighborhood of i").

Lets illustrate this by making reference again to Figure 1.

Consider $A_1$. The spatial weights matrix indicates that the neighborhood of $A_1$ consists of three areas: $A_2$, $A_3$, and $A_4$. Therefore $n_1=3$, and $j\in N(1)$ are 2, 3, and 4.

The spatial moving average of $A_1$ for a variable $x$ would then be calculated as:
$$
\overline{x_1}=\frac{x_2 + x_3 + x_4}{3}
$$

Notice that another way of writing the spatial moving average expression is as follows, since membership in the neighborhood of $i$ is implicit in the definition of $w_{ij}$! Since $w_{ij}$ takes values of zero and one, the effect is to turn on and off the values of $x$ depending on whether they are for areas adjacent to $i$:
$$
\overline{x_i}=\frac{1}{n_i}\sum_{j=1}^n{w_{ij}x_j}
$$

This means that the spatial moving average of $A_1$ for a variable $x$ on this system can also be calculated using the spatial weights matrix as:
$$
\overline{x_1}=\frac{w_{11}x_1 + w_{12}x_2 + w_{13}x_3 + w_{14}x_4 + w_{15}x_5 + w_{12}x_6}{3}
$$

Substituting the spatial weights:
$$
\overline{x_1}=\frac{0x_1 + 1x_2 + 1x_3 + 1x_4 + 0x_5 + 0x_6}{3} = \frac{x_2 + x_3 + x_4}{3}
$$

In other words, the spatial weights can be used directly in the calculation of spatial moving averages.

Further, notice that:
$$
n_i=\sum_{j=1}^{n}w_{ij}
$$
which is simply the total number of neighbors of $A_i$, and the value we used to row-standardize the spatial weights.

Since the row-standardized weights have already been divided by the number of neighbors, we can use them to express the spatial moving average as follows:
$$
\overline{x_i}=\sum_{j=1}^{n}{w_{ij}^{st}x_j}
$$

Continuing the example, if we use the row-standardized weights, the spatial moving average at $A_1$ is:
$$
\overline{x_i}=0x_1 + \frac{1}{3}x_2 + \frac{1}{3}x_3 + \frac{1}{3}x_4 + 0x_5 + 0x_6
$$
which is the same as:
$$
\overline{x_i}=\frac{x_2 + x_3 + x_4}{3}
$$

Consider the following map of Hamilton's population density:

```r
map <- ggplot(data = Hamilton_CT) +
  geom_sf(aes(fill = cut_number(Hamilton_CT$POP_DENSITY, 5), 
                   POP_DENSITY = round(POP_DENSITY),
                   TRACT = TRACT), 
               color = "black") +
  geom_sf(data = subset(Hamilton_CT, TRACT == "5370142.02"), 
               aes(POP_DENSITY = round(POP_DENSITY),
                   TRACT = TRACT), 
               color = "red",
               weight = 3, fill = NA) +
  geom_sf(data = subset(Hamilton_CT, TRACT == "5370144.01"), 
               aes(POP_DENSITY = round(POP_DENSITY),
                   TRACT = TRACT), 
               color = "green",
               weight = 3, fill = NA) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Pop Density") +
  coord_sf()
ggplotly(map, tooltip = c("TRACT", "POP_DENSIT"))
```

<!--html_preserve--><div id="htmlwidget-01750ad200598a1ac2b4" style="width:672px;height:480px;" class="plotly html-widget"></div>

Manually calculate the spatial moving average for tract 5370142.02 (with the red boundary) and tract (with the green boundary). **Tip**: hover over the tracts to see their population densities.


```r
(32 + 109 + 48)/3
```

```
## [1] 63
```

```r
(48 + 55 + 125)/3
```

```
## [1] 76
```

Spatial moving averages can be calculated in a straighforward way by means of the function `lag.listw` function of the `spdep` package. This function uses a spatial weights matrix and automatically selects the row-standardized weights.

Lets calculate the spatial moving average of population density:

```r
POP_DENSITY.sma <- lag.listw(x = Hamilton_CT.w, Hamilton_CT$POP_DENSITY)
```

Lets now plot the spatial moving average of population density. First we add this variable to our tidy dataframe:

```r
Hamilton_CT <- left_join(Hamilton_CT, data.frame(TRACT = Hamilton_CT$TRACT, POP_DENSITY.sma))
```

```
## Joining, by = "TRACT"
```

```
## Warning: Column `TRACT` joining character vector and factor, coercing into
## character vector
```

And plot:

```r
map.sma <- ggplot() +
  geom_sf(data = Hamilton_CT,
          aes(fill = cut_number(Hamilton_CT$POP_DENSITY.sma, 5),
              POP_DENSITY.sma = round(POP_DENSITY.sma),
              TRACT = TRACT),
          color = "black") +
  geom_sf(data = subset(Hamilton_CT, TRACT == "5370142.02"), 
          aes(POP_DENSITY.sma = round(POP_DENSITY.sma),
              TRACT = TRACT), 
          color = "red",
          weight = 3, fill = NA) +
  geom_sf(data = subset(Hamilton_CT, TRACT == "5370144.01"), 
          aes(POP_DENSITY.sma = round(POP_DENSITY.sma),
              TRACT = TRACT), 
          color = "green",
          weight = 3, fill = NA) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Pop Density SMA") +
  coord_sf()
ggplotly(map.sma, tooltip = c("TRACT", "POP_DENSIT.sma"))
```

<!--html_preserve--><div id="htmlwidget-e959e7e2b293edab8742" style="width:672px;height:480px;" class="plotly html-widget"></div>

Verify that your manual calculations for the two tracts above are correct. What differences do you notice between the map of population density and the map of spatial moving averages of population density?

## Other Criteria for Coding Proximity

Adjacenty is not the only criterion for coding proximity.

Occasionally, the distance between areas is calculated by using the _centroids_ of the areas as their representative points. A centroid is simply the mean of the coordinates of the edges of an area, and in this way represent the "centre of gravity" of the area.

The inter-centroid distance allows us to define additional criteria for proximity, including neighbors within a certain distance threshold, and k-nearest neighbors.

* Distance-based criterion

$$
w_{ij}=\bigg\{\begin{array}{l l}
1\text{ if inter-centroid distance } d_{ij}\leq \delta\\
0\text{ otherwise}\\
\end{array}
$$
where $\delta$ is a distance threshold.

Distance-based nearest neighbors can be obtained in R by means of the function `dnearneigh`.

First we need to obtain the coordinates of the centroids of the areas. These are the first two columns of the output of `st_coordinates` function:

```r
CT_centroids <- coordinates(Hamilton_CT.sp)
```

A nearest neighbors object `nb` is produced as follows (selecting a distance threshold between 0 and 5 km):

```r
Hamilton_CT.dnb <- dnearneigh(CT_centroids, d1 = 0, d2 = 5000)
```

We can visualize the neighbors (adjacent) areas:

```r
plot(Hamilton_CT.sp, border = "gray")
plot(Hamilton_CT.dnb, CT_centroids, col = "red", add = TRUE)
```

<img src="20-Area-Data-II_files/figure-html/unnamed-chunk-19-1.png" width="672" />

Try changing the distance threshold to see how different neighborhoods are defined.

* $k$-nearest neighbors

$$
w_{ij}=\bigg\{\begin{array}{l l}
1\text{ if } A_j \text{ is one of k-nearest neighbors of } A_i\\
0\text{ otherwise}\\
\end{array}
$$

A potential disadvantage of using a distance-based criterion is that for zoning systems with areas of vastly different sizes, small areas will end up having many neighbors, whereas large areas will have few or none.

The criterion of $k$-nearest neighbors allows for some adaptation to the size of the areas. Under this criterion, all areas have the exact same number of neighbors, but the geographical extent of the neighborhood may (and likely will) change.

In R, $k$-nearest neighbors can be obtained by means of the function `knearneigh`, and the arguments include the value of $k$:

```r
Hamilton_CT.knb <- knn2nb(knearneigh(CT_centroids, k = 3))
```

We can again visualize the neighbors ("adjacent") areas:

```r
plot(Hamilton_CT.sp, border = "gray")
plot(Hamilton_CT.knb, CT_centroids, col = "red", add = TRUE)
```

<img src="20-Area-Data-II_files/figure-html/unnamed-chunk-21-1.png" width="672" />

Try changing the value of `k` to see how the neighborhoods change.