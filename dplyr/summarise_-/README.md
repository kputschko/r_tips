---
title: "R Tips"
author: "Kevin Putschko"
date: "October 1, 2018"
output: 
  html_document:
    keep_md: TRUE
---



### dplyr::summarise_*

When we want to create conditional summaries, we can use the **dplyr::summarise_*** functions.  These include:

- *summarise_all*: Applies the summary function on all columns
- *summarise_at*: Applies the summary function on specified columns
- *summarise_if*: Applies the summary function if the columns meets a given condition

Let's see how these work in action with the *mtcars* dataframe.


```
## Observations: 32
## Variables: 12
## $ rowname <chr> "Mazda RX4", "Mazda RX4 Wag", "Datsun 710", "Hornet 4 ...
## $ mpg     <dbl> 21.0, 21.0, 22.8, 21.4, 18.7, 18.1, 14.3, 24.4, 22.8, ...
## $ cyl     <dbl> 6, 6, 4, 6, 8, 6, 8, 4, 4, 6, 6, 8, 8, 8, 8, 8, 8, 4, ...
## $ disp    <dbl> 160.0, 160.0, 108.0, 258.0, 360.0, 225.0, 360.0, 146.7...
## $ hp      <dbl> 110, 110, 93, 110, 175, 105, 245, 62, 95, 123, 123, 18...
## $ drat    <dbl> 3.90, 3.90, 3.85, 3.08, 3.15, 2.76, 3.21, 3.69, 3.92, ...
## $ wt      <dbl> 2.620, 2.875, 2.320, 3.215, 3.440, 3.460, 3.570, 3.190...
## $ qsec    <dbl> 16.46, 17.02, 18.61, 19.44, 17.02, 20.22, 15.84, 20.00...
## $ vs      <dbl> 0, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 1, ...
## $ am      <dbl> 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, ...
## $ gear    <dbl> 4, 4, 4, 3, 3, 3, 3, 4, 4, 4, 4, 3, 3, 3, 3, 3, 3, 4, ...
## $ carb    <dbl> 4, 4, 1, 1, 2, 1, 4, 2, 2, 4, 4, 3, 3, 3, 4, 4, 4, 1, ...
```

```
##                rowname  mpg cyl  disp  hp drat    wt  qsec vs am gear carb
## 1            Mazda RX4 21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
## 2        Mazda RX4 Wag 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
## 3           Datsun 710 22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
## 4       Hornet 4 Drive 21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
## 5    Hornet Sportabout 18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2
## 6              Valiant 18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1
## 7           Duster 360 14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4
## 8            Merc 240D 24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
## 9             Merc 230 22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
## 10            Merc 280 19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
## 11           Merc 280C 17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
## 12          Merc 450SE 16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3
## 13          Merc 450SL 17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3
## 14         Merc 450SLC 15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3
## 15  Cadillac Fleetwood 10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4
## 16 Lincoln Continental 10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4
## 17   Chrysler Imperial 14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4
## 18            Fiat 128 32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
## 19         Honda Civic 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
## 20      Toyota Corolla 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
## 21       Toyota Corona 21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
## 22    Dodge Challenger 15.5   8 318.0 150 2.76 3.520 16.87  0  0    3    2
## 23         AMC Javelin 15.2   8 304.0 150 3.15 3.435 17.30  0  0    3    2
## 24          Camaro Z28 13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4
## 25    Pontiac Firebird 19.2   8 400.0 175 3.08 3.845 17.05  0  0    3    2
## 26           Fiat X1-9 27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
## 27       Porsche 914-2 26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
## 28        Lotus Europa 30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
## 29      Ford Pantera L 15.8   8 351.0 264 4.22 3.170 14.50  0  1    5    4
## 30        Ferrari Dino 19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6
## 31       Maserati Bora 15.0   8 301.0 335 3.54 3.570 14.60  0  1    5    8
## 32          Volvo 142E 21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2
```

We see that we have both categorical and numeric columns.  For example, we could count the number of distinct values for all columns.  Traditionally, we could do this one column at a time. As you can see, this can easily get tedious with one row of code for each column.


```r
ex_mtcars %>% 
  summarise(n_mpg = n_distinct(mpg),
            n_cyl = n_distinct(cyl),
            n_disp = n_distinct(disp),
            n_hp = n_distinct(hp))
```

```
##   n_mpg n_cyl n_disp n_hp
## 1    25     3     27   22
```

The function **dplyr::summarise_all** makes turns this into a single line of code.


```r
ex_mtcars %>% summarise_all(n_distinct)
```

```
##   rowname mpg cyl disp hp drat wt qsec vs am gear carb
## 1      32  25   3   27 22   22 29   30  2  2    3    6
```

If we want different functions for different columns, we can use **dplyr::summarise_at**.


```r
cols_categorical <- c("rowname", "cyl", "vs", "am", "gear", "carb")
cols_numeric <- c("mpg", "disp", "hp")

ex_mtcars %>% summarise_at(cols_categorical, n_distinct)
```

```
##   rowname cyl vs am gear carb
## 1      32   3  2  2    3    6
```

```r
ex_mtcars %>% summarise_at(cols_numeric, mean)
```

```
##        mpg     disp       hp
## 1 20.09062 230.7219 146.6875
```

This also allows for a list of summary functions to applied to specified columns.


```r
funs_numeric <- c("mean", "sd", "length")

ex_mtcars %>% 
  group_by(cyl) %>% 
  summarise_at("mpg", funs_numeric)
```

```
## # A tibble: 3 x 4
##     cyl  mean    sd length
##   <dbl> <dbl> <dbl>  <int>
## 1     4  26.7  4.51     11
## 2     6  19.7  1.45      7
## 3     8  15.1  2.56     14
```

And in the cases when we are aware of column names in advance, we can use **dplyr::summarise_if**.


```r
ex_mtcars %>% summarise_if(is.character, n_distinct)
```

```
##   rowname
## 1      32
```

```r
ex_mtcars %>% summarise_if(is.numeric, mean)
```

```
##        mpg    cyl     disp       hp     drat      wt     qsec     vs
## 1 20.09062 6.1875 230.7219 146.6875 3.596563 3.21725 17.84875 0.4375
##        am   gear   carb
## 1 0.40625 3.6875 2.8125
```


