---
title: "R Tips"
author: "Kevin Putschko"
date: "August 28, 2018"
output: 
  html_document:
    keep_md: TRUE
---

### tibble::enframe() & tibble::deframe



This is a wonderful little tip to use when working with named vectors or lists, and you want to quickly convert them to a dataframe format.  

We'll begin with **tibble::enframe()**.


```r
vector_1 <- 
  c(name_1 = "a", 
    name_2 = "b",
    name_3 = "c")

vector_1
```

```
## name_1 name_2 name_3 
##    "a"    "b"    "c"
```


```r
vector_1 %>% enframe()
```

```
## # A tibble: 3 x 2
##   name   value
##   <chr>  <chr>
## 1 name_1 a    
## 2 name_2 b    
## 3 name_3 c
```



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


```r
run_time_1 <- 20
run_time_2 <- 10

condition_3 <- runif(0, 1, n = 1) 
if (condition_3 > 0.95) {run_time_3 <- 60}

run_time_4 <- 30
run_time_5 <- 40

ls(pattern = "run_time_") %>% 
  mget(envir = .GlobalEnv) %>% 
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

