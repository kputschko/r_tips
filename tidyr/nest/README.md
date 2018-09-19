---
title: "R Tips"
author: "Kevin Putschko"
date: "September 19, 2018"
output: 
  html_document:
    keep_md: TRUE
---



### tidyr::nest()

The function **tidyr::nest()** has become an incredible tool in my toolbox.  The description file suggests "nest() creates a list of data frames containing all the nested variables".  Great.  But what does this mean?

It might be helpful to envision **tidyr::nest()** as a helpful way to work with lists, and lists within lists.  Specifically, this function takes the input data and creates subsets of data based on the supplied columns, and stores the result as a list-table.




```r
mtcars %>% as_tibble()
```

```
## # A tibble: 32 x 11
##      mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb
##  * <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
##  1  21       6  160    110  3.9   2.62  16.5     0     1     4     4
##  2  21       6  160    110  3.9   2.88  17.0     0     1     4     4
##  3  22.8     4  108     93  3.85  2.32  18.6     1     1     4     1
##  4  21.4     6  258    110  3.08  3.22  19.4     1     0     3     1
##  5  18.7     8  360    175  3.15  3.44  17.0     0     0     3     2
##  6  18.1     6  225    105  2.76  3.46  20.2     1     0     3     1
##  7  14.3     8  360    245  3.21  3.57  15.8     0     0     3     4
##  8  24.4     4  147.    62  3.69  3.19  20       1     0     4     2
##  9  22.8     4  141.    95  3.92  3.15  22.9     1     0     4     2
## 10  19.2     6  168.   123  3.92  3.44  18.3     1     0     4     4
## # ... with 22 more rows
```

When providing a single column, we see the result isn't very useful.  The data has been subset into a table (aka "tibble") with 1 row and 1 column.


```r
mtcars %>% as_tibble() %>% nest(gear)
```

```
## # A tibble: 32 x 11
##      mpg   cyl  disp    hp  drat    wt  qsec    vs    am  carb data       
##    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <list>     
##  1  21       6  160    110  3.9   2.62  16.5     0     1     4 <tibble [1~
##  2  21       6  160    110  3.9   2.88  17.0     0     1     4 <tibble [1~
##  3  22.8     4  108     93  3.85  2.32  18.6     1     1     1 <tibble [1~
##  4  21.4     6  258    110  3.08  3.22  19.4     1     0     1 <tibble [1~
##  5  18.7     8  360    175  3.15  3.44  17.0     0     0     2 <tibble [1~
##  6  18.1     6  225    105  2.76  3.46  20.2     1     0     1 <tibble [1~
##  7  14.3     8  360    245  3.21  3.57  15.8     0     0     4 <tibble [1~
##  8  24.4     4  147.    62  3.69  3.19  20       1     0     2 <tibble [1~
##  9  22.8     4  141.    95  3.92  3.15  22.9     1     0     2 <tibble [1~
## 10  19.2     6  168.   123  3.92  3.44  18.3     1     0     4 <tibble [1~
## # ... with 22 more rows
```

This is because when we specify just *gear*, we are suggesting that all other columns will be used as identifiers, while *gear* is the only data that will be subset.

So, what if we list all columns except *gear*?


```r
mtcars %>% as_tibble() %>% nest(mpg, cyl, disp, hp, drat, wt, qsec, vs, am, carb)
```

```
## # A tibble: 3 x 2
##    gear data              
##   <dbl> <list>            
## 1     4 <tibble [12 x 10]>
## 2     3 <tibble [15 x 10]>
## 3     5 <tibble [5 x 10]>
```

Ah! That's better.  Now we see that the full dataframe has been subset into three distinct tables, with one for each level of gear.  But, that took a lot of typing to make work.

Here are some alternative forms of using this function.  I personally prefer the **dplyr::group_by()** method, as it is clear we are creating a nested dataframe for each grouping value of *gear*.


```r
mtcars %>% as_tibble() %>% nest(-gear)
```

```
## # A tibble: 3 x 2
##    gear data              
##   <dbl> <list>            
## 1     4 <tibble [12 x 10]>
## 2     3 <tibble [15 x 10]>
## 3     5 <tibble [5 x 10]>
```

```r
mtcars %>% as_tibble() %>% group_by(gear) %>% nest()
```

```
## # A tibble: 3 x 2
##    gear data              
##   <dbl> <list>            
## 1     4 <tibble [12 x 10]>
## 2     3 <tibble [15 x 10]>
## 3     5 <tibble [5 x 10]>
```


### Use Case

This is pretty slick, right?  But how is this useful?  My favorite way to use **tidyr::nest()** is with **purrr::map()**, which allows me to apply a single function to every element of a list.

What if I wanted to build a linear model on unique subsets of a dataset?  I could use **tidyr::nest()** to create the subsets, and **purrr::map()** to apply the **lm()** function to each subset. 


```r
iris %>% as_tibble()
```

```
## # A tibble: 150 x 5
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##           <dbl>       <dbl>        <dbl>       <dbl> <fct>  
##  1          5.1         3.5          1.4         0.2 setosa 
##  2          4.9         3            1.4         0.2 setosa 
##  3          4.7         3.2          1.3         0.2 setosa 
##  4          4.6         3.1          1.5         0.2 setosa 
##  5          5           3.6          1.4         0.2 setosa 
##  6          5.4         3.9          1.7         0.4 setosa 
##  7          4.6         3.4          1.4         0.3 setosa 
##  8          5           3.4          1.5         0.2 setosa 
##  9          4.4         2.9          1.4         0.2 setosa 
## 10          4.9         3.1          1.5         0.1 setosa 
## # ... with 140 more rows
```

```r
iris_nest <- iris %>% as_tibble() %>% group_by(Species) %>% nest()

iris_nest
```

```
## # A tibble: 3 x 2
##   Species    data             
##   <fct>      <list>           
## 1 setosa     <tibble [50 x 4]>
## 2 versicolor <tibble [50 x 4]>
## 3 virginica  <tibble [50 x 4]>
```

Now that we've got the unique subsets in a convient form, let's apply the model function.


```r
iris_model <- 
  iris_nest %>% 
  mutate(model = map(.x = data, 
                     .f = ~ lm(data = ., Sepal.Length ~ Sepal.Width + Petal.Length)))

iris_model
```

```
## # A tibble: 3 x 3
##   Species    data              model   
##   <fct>      <list>            <list>  
## 1 setosa     <tibble [50 x 4]> <S3: lm>
## 2 versicolor <tibble [50 x 4]> <S3: lm>
## 3 virginica  <tibble [50 x 4]> <S3: lm>
```

And here we have a unique model for each unique subset of data based on the *Species* nesting!

We can check out the results of a model like so


```r
iris_model$model[[1]]
```

```
## 
## Call:
## lm(formula = Sepal.Length ~ Sepal.Width + Petal.Length, data = .)
## 
## Coefficients:
##  (Intercept)   Sepal.Width  Petal.Length  
##       2.3037        0.6674        0.2834
```

And that's **tidyr::nest()**!
