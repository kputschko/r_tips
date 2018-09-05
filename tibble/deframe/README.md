---
title: "R Tips"
author: "Kevin Putschko"
date: "September 5, 2018"
output: 
  html_document:
    keep_md: TRUE
---



### tibble::deframe()

We talked about **tibble::enframe()**, now we'll talk about its complement, **tibble::deframe()**. The help page tells us the function "converts two-column data frames to a named vector or list, using the first column as name and the second column as value."  

But what is this function actually doing?  It converts a two-column table to a list, assuming the first column contains the names and the second column contains the values of each list element.

For example, let's define a list-table.


```r
list_table <- 
  tibble(names  = c("dataframe", "strings", "numbers"),
         values = c(list(mtcars[1:5, ]), list(letters), list(exp(1:10)))
  )

list_table 
```

```
## # A tibble: 3 x 2
##   names     values               
##   <chr>     <list>               
## 1 dataframe <data.frame [5 x 11]>
## 2 strings   <chr [26]>           
## 3 numbers   <dbl [10]>
```

We see this is a convient way of storing and viewing any number of lists.  But to get this into an actual list form, we use **tibble::deframe()**.


```r
list_table %>% deframe()
```

```
## $dataframe
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## 
## $strings
##  [1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q"
## [18] "r" "s" "t" "u" "v" "w" "x" "y" "z"
## 
## $numbers
##  [1]     2.718282     7.389056    20.085537    54.598150   148.413159
##  [6]   403.428793  1096.633158  2980.957987  8103.083928 22026.465795
```



### Use Case

Let's see how this works in practice.  For example, if we have a data dictionary table with at least two columns: Full Name and Short Name.


```r
dictionary <- 
  tibble(full  = c("MilePerGallon", "Cylinders", "Displacement"),
         short = c("mpg", "cyl", "disp"))

dictionary
```

```
## # A tibble: 3 x 2
##   full          short
##   <chr>         <chr>
## 1 MilePerGallon mpg  
## 2 Cylinders     cyl  
## 3 Displacement  disp
```

If we are going to quickly change all the short names to the full names, we can use **tibble::deframe()**.  Using **tibble::deframe()** on dictionary, we see the result is a named vector.


```r
dict_deframe <- dictionary %>% deframe()

dict_deframe
```

```
## MilePerGallon     Cylinders  Displacement 
##         "mpg"         "cyl"        "disp"
```

This next part is a bit advanced, but we are going to use *tidyverse* non-standard evaluation (NSE) to change the names of the columns in the *mtcars* dataframe.


```r
mtcars[1:5] %>% glimpse()
```

```
## Observations: 32
## Variables: 5
## $ mpg  <dbl> 21.0, 21.0, 22.8, 21.4, 18.7, 18.1, 14.3, 24.4, 22.8, 19....
## $ cyl  <dbl> 6, 6, 4, 6, 8, 6, 8, 4, 4, 6, 6, 8, 8, 8, 8, 8, 8, 4, 4, ...
## $ disp <dbl> 160.0, 160.0, 108.0, 258.0, 360.0, 225.0, 360.0, 146.7, 1...
## $ hp   <dbl> 110, 110, 93, 110, 175, 105, 245, 62, 95, 123, 123, 180, ...
## $ drat <dbl> 3.90, 3.90, 3.85, 3.08, 3.15, 2.76, 3.21, 3.69, 3.92, 3.9...
```

```r
# dplyr::rename_() - This method is depreciated in favour of the rlang package methods
mtcars[1:5] %>% rename_(.dots = dict_deframe) %>% glimpse()
```

```
## Observations: 32
## Variables: 5
## $ MilePerGallon <dbl> 21.0, 21.0, 22.8, 21.4, 18.7, 18.1, 14.3, 24.4, ...
## $ Cylinders     <dbl> 6, 6, 4, 6, 8, 6, 8, 4, 4, 6, 6, 8, 8, 8, 8, 8, ...
## $ Displacement  <dbl> 160.0, 160.0, 108.0, 258.0, 360.0, 225.0, 360.0,...
## $ hp            <dbl> 110, 110, 93, 110, 175, 105, 245, 62, 95, 123, 1...
## $ drat          <dbl> 3.90, 3.90, 3.85, 3.08, 3.15, 2.76, 3.21, 3.69, ...
```

```r
# dplyr::rename() with rlang::syms()
mtcars[1:5] %>% rename(!!! syms(dict_deframe)) %>% glimpse()
```

```
## Observations: 32
## Variables: 5
## $ MilePerGallon <dbl> 21.0, 21.0, 22.8, 21.4, 18.7, 18.1, 14.3, 24.4, ...
## $ Cylinders     <dbl> 6, 6, 4, 6, 8, 6, 8, 4, 4, 6, 6, 8, 8, 8, 8, 8, ...
## $ Displacement  <dbl> 160.0, 160.0, 108.0, 258.0, 360.0, 225.0, 360.0,...
## $ hp            <dbl> 110, 110, 93, 110, 175, 105, 245, 62, 95, 123, 1...
## $ drat          <dbl> 3.90, 3.90, 3.85, 3.08, 3.15, 2.76, 3.21, 3.69, ...
```
