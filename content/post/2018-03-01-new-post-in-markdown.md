---
title: New post in markdown
author: Sergio Uribe
date: '2018-03-01'
slug: new-post-in-markdown
categories:
  - md
tags:
  - md
subtitle: ''
---

Packages
install.packages("pubh")
install.packages("tidyverse")
install.packages("pubh")

# Exploratory data analysis of medical data
```{r, eval=FALSE}

library(pubh)
library(tidyverse)
```
## Dataset
```{r, eval=FALSE}
data(Hodgkin)
```

## Exploring the raw dataset
```{r}
head(Hodgkin)
```
## Summary tables
```{r}
Hodgkin %>% 
  gather(key = "Cell", value = value, CD4:CD8) %>% 
  group_by(Group, Cell) %>% 
  summarise(Mean = mean(value)) %>% 
  spread(Cell, Mean)
```
## Visualizations

### Histogram

```{r}
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = `CD4/CD8 ratio`, fill = Group)) +
  geom_histogram(bins = 9, alpha = 0.5) + 
  theme_minimal() +
  scale_fill_grey() 
```

### Boxplot with summary table
```{r}
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


```{r}
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = Group, y = `CD4/CD8 ratio`)) + 
  geom_boxplot() +
  theme_minimal()
```

### Boxplot with jitter
```{r}
Hodgkin %>% 
  mutate("CD4/CD8 ratio" = CD4 / CD8) %>% 
  ggplot(aes(x = Group, y = `CD4/CD8 ratio`)) + 
  geom_boxplot() +
  geom_dotplot(binaxis = "y",
               stackdir = "center", 
               alpha = 0.3) +
  theme_minimal() 
```


### Most complete
require library(ggsignif)
```{r}

library(ggsignif)
```

```{r}
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