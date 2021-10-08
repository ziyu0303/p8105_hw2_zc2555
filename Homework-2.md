p8105\_hw2\_zc2555
================
Ziyu Chen
10/7/2021

## R set up

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(readxl)
```

## Question 1, Mr.Trash Wheel Sheet clean up

``` r
trash_df=read_excel("./Trash_data.xlsx", 1) %>%
   janitor::clean_names() %>%
   select(-c(x15, x16, x17)) %>%
   drop_na() 
trash_df$sports_balls = round(trash_df$sports_balls)
```
