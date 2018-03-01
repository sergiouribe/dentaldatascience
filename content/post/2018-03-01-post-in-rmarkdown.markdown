---
title: Post in rmarkdown
author: Sergio Uribe
date: '2018-03-01'
slug: post-in-rmarkdown
categories:
  - rmarkdown
tags:
  - rmarkdown
---


# Exploratory data analysis of medical data

```r
library(pubh)
```

```
## Loading required package: lattice
```

```
## Loading required package: latticeExtra
```

```
## Loading required package: RColorBrewer
```

```r
library(tidyverse)
```

```
## ── Attaching packages ──────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
## ✔ tidyr   0.7.2     ✔ stringr 1.2.0
## ✔ readr   1.1.1     ✔ forcats 0.2.0
```

```
## ── Conflicts ─────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter()  masks stats::filter()
## ✖ dplyr::lag()     masks stats::lag()
## ✖ ggplot2::layer() masks latticeExtra::layer()
```
## Dataset

```r
data(Hodgkin)
```

## Exploring the raw dataset

```r
head(Hodgkin)
```

```
##    CD4  CD8   Group
## 1  396  836 Hodgkin
## 2  568  978 Hodgkin
## 3 1212 1678 Hodgkin
## 4  171  212 Hodgkin
## 5  554  670 Hodgkin
## 6 1104 1335 Hodgkin
```
## Summary tables

```r
Hodgkin %>% 
  gather(key = "Cell", value = value, CD4:CD8) %>% 
  group_by(Group, Cell) %>% 
  summarise(Mean = mean(value)) %>% 
  spread(Cell, Mean)
```

```
## # A tibble: 2 x 3
## # Groups:   Group [2]
##   Group         CD4   CD8
## * <fct>       <dbl> <dbl>
## 1 Non-Hodgkin   522   260
## 2 Hodgkin       823   614
```
## Visualizations

### Histogram


```r
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = `CD4/CD8 ratio`, fill = Group)) +
  geom_histogram(bins = 9, alpha = 0.5) + 
  theme_minimal() +
  scale_fill_grey() 
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

### Boxplot with summary table

```r
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  group_by(Group) %>% 
  summarise(N = n(), 
            "Min." = min(`CD4/CD8 ratio`), 
            "Max." = max(`CD4/CD8 ratio`), 
            Mean = mean(`CD4/CD8 ratio`), 
            Median = median(`CD4/CD8 ratio`), 
            SD = sd(`CD4/CD8 ratio`)) %>% 
  mutate_if(is.numeric, funs(round(., 2)))
```

```
## # A tibble: 2 x 7
##   Group           N  Min.  Max.  Mean Median    SD
##   <fct>       <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl>
## 1 Non-Hodgkin  20.0 1.10   3.49  2.12   2.15 0.730
## 2 Hodgkin      20.0 0.470  3.82  1.50   1.19 0.910
```



```r
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = Group, y = `CD4/CD8 ratio`)) + 
  geom_boxplot() +
  theme_minimal()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

### Boxplot with jitter

```r
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = Group, y = `CD4/CD8 ratio`)) + 
  geom_boxplot() +
  geom_dotplot(binaxis = "y",
               stackdir = "center", 
               alpha = 0.3) +
  theme_minimal() 
```

```
## `stat_bindot()` using `bins = 30`. Pick better value with `binwidth`.
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)

### Most complete
require library(ggsignif)

```r
install.packages("ggsignif")
```

```
## Installing package into '/home/suribe/R/x86_64-pc-linux-gnu-library/3.4'
## (as 'lib' is unspecified)
```

```
## Error in contrib.url(repos, type): trying to use CRAN without setting a mirror
```

```r
library(ggsignif)
```


```r
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = Group, y = `CD4/CD8 ratio`)) + 
  geom_boxplot() +
  geom_dotplot(binaxis = "y",
               stackdir = "center", 
               alpha = 0.3) +
  geom_signif(comparisons = list(c("Non-Hodgkin", "Hodgkin")), 
              map_signif_level=TRUE) +
  theme_minimal() +
  labs(
    caption = "\n * p>0.05; ** p<0.01; *** p<0.001 "
  )
```

```
## `stat_bindot()` using `bins = 30`. Pick better value with `binwidth`.
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

