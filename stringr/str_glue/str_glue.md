---
title: "R Tips"
author: "Kevin Putschko"
date: "August 22, 2018"
output: 
  html_document:
    keep_md: TRUE
---

### stringr::str_glue()

How can we easily include column names as values in a table?



As of 2018 February, the **stringr** package has a *str_glue* function to accomplish this task.  This function allows for "expressions enclosed by braces will be evaluated as R code".  


```r
mtcars %>% head()
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```


```r
mtcars %>% mutate(Label = str_glue("Miles Per Gallon: {mpg} - Cylinders: {cyl}")) %>% head()
```

```
##    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## 1 21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## 2 21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## 3 22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## 4 21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## 5 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## 6 18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
##                                   Label
## 1   Miles Per Gallon: 21 - Cylinders: 6
## 2   Miles Per Gallon: 21 - Cylinders: 6
## 3 Miles Per Gallon: 22.8 - Cylinders: 4
## 4 Miles Per Gallon: 21.4 - Cylinders: 6
## 5 Miles Per Gallon: 18.7 - Cylinders: 8
## 6 Miles Per Gallon: 18.1 - Cylinders: 6
```

See details here: <https://stringr.tidyverse.org/reference/str_glue.html>
