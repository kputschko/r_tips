---
title: "R Tips"
author: "Kevin Putschko"
date: "September 13, 2018"
output: 
  html_document:
    keep_md: TRUE
---



### stringr::str_c()

This will be a quick tip.  The function **stringr::str_c()** is used to join multiple strings into a single string.  

Let's take a look.  This is convienent when we are manually joining strings together.  


```r
str_c("This", "will", "be", "joined", sep = " ")
```

```
## [1] "This will be joined"
```

And when we are working with strings stored in a vector or a list, we can use the *collapse* parameter to get the job done.


```r
words <- c("This", "will", "be", "joined")

words %>% str_c(collapse = " ")
```

```
## [1] "This will be joined"
```


### Use Case

In practice, this is of course useful for joining strings.  But a powerful utility is when we are working with dataframes.  

For example, in the following table we have the order of events for users.


```r
user_events <- 
  tibble(user = c(1, 1, 1, 2, 2, 2, 3, 3, 3),
         event = c("A", "C", "B", "C", "A", "B", "B", "A", "C"))

user_events
```

```
## # A tibble: 9 x 2
##    user event
##   <dbl> <chr>
## 1     1 A    
## 2     1 C    
## 3     1 B    
## 4     2 C    
## 5     2 A    
## 6     2 B    
## 7     3 B    
## 8     3 A    
## 9     3 C
```

With the **dplyr::summarise()** function, we can get this done easily.  With **stringr::str_c()**, we see the result is one row for each user, and the order is collapsed into a single string that allows for easy viewing.


```r
user_events %>% 
  group_by(user) %>% 
  summarise(order = str_c(event, collapse = ", "))
```

```
## # A tibble: 3 x 2
##    user order  
##   <dbl> <chr>  
## 1     1 A, C, B
## 2     2 C, A, B
## 3     3 B, A, C
```

