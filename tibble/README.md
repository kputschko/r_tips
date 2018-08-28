---
title: "R Tips"
author: "Kevin Putschko"
date: "August 28, 2018"
output: 
  html_document:
    keep_md: TRUE
---

#### tibble::enframe()



This is a wonderful little function to use when working with named vectors or lists, and you want to convert them to a dataframe format.  

Let's begin by defining a named vector.  In my experience, this could represent a mapping of column names from a raw text file to meaningful names.

```r
vector_1 <- 
  c(column_1 = "Person", 
    column_2 = "Place",
    column_3 = "Thing")

vector_1 
```

```
## column_1 column_2 column_3 
## "Person"  "Place"  "Thing"
```

Then we can use **tibble::enframe()** to quickly "transpose" the vector into a table containing the *name* and the *value*.


```r
vector_1 %>% enframe()
```

```
## # A tibble: 3 x 2
##   name     value 
##   <chr>    <chr> 
## 1 column_1 Person
## 2 column_2 Place 
## 3 column_3 Thing
```

This will work with lists as well.  


```r
list_1 <- 
  list(table = tibble(a = 1:5, b = 21:25),
       string = "words, words, words",
       vector = letters[1:10])

list_1
```

```
## $table
## # A tibble: 5 x 2
##       a     b
##   <int> <int>
## 1     1    21
## 2     2    22
## 3     3    23
## 4     4    24
## 5     5    25
## 
## $string
## [1] "words, words, words"
## 
## $vector
##  [1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j"
```

After using **tibble::enframe()**, the resulting table contains the name of each list element, and now the value is in the list-table format showing the class and length of the element.


```r
list_1 %>% enframe()
```

```
## # A tibble: 3 x 2
##   name   value           
##   <chr>  <list>          
## 1 table  <tibble [5 x 2]>
## 2 string <chr [1]>       
## 3 vector <chr [10]>
```

#### Use Case

I like to use **tibble::enframe()** when collecting objects that are created conditionally.  For example, catching the run times of certain processes.  In the following demonstration, we gather the run times of 5 procedures, where the 3rd procedure only runs in certain cases.  


```r
run_time_1 <- 20
run_time_2 <- 10

condition <- 0 
if (condition == 1) {run_time_3 <- 60}

run_time_4 <- 30
run_time_5 <- 40
```

We use *ls()* and *mget()* to gather the objects into a list.


```r
env_list <- 
  ls(pattern = "run_time_") %>% 
  mget(envir = .GlobalEnv)
```

Finally, we use **tibble::enframe()** and *tidyr::unnest()* to store these values in a table. 


```r
env_list %>% 
  enframe() %>% 
  unnest()
```

```
## # A tibble: 4 x 2
##   name       value
##   <chr>      <dbl>
## 1 run_time_1    20
## 2 run_time_2    10
## 3 run_time_4    30
## 4 run_time_5    40
```

This is a way to dynamically collect these values, without having to hard code when run_time_3 should be *NULL* or not.
