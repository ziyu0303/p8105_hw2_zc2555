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

## Question 1

*Mr.Trash Wheel Sheet clean up*

``` r
trash_df = read_excel("./Trash_data.xlsx", 1) %>%
   janitor::clean_names() %>%
   select(-c(x15, x16, x17)) %>%
   drop_na() 
trash_df$sports_balls = round(trash_df$sports_balls)
```

*Precipitation 2019 data cleaning*

``` r
precipitation_df_2019 = read_excel("./Trash_data.xlsx", 4, skip = 1) %>%
  drop_na()
precipitation_df_2019$Year = 2019
```

*Precipitation 2018 data cleaning*

``` r
precipitation_df_2018 = read_excel("./Trash_data.xlsx", 5, skip = 1) %>%
  drop_na()
precipitation_df_2018$Year = 2018
```

*merge 2019 and 2018 together*

``` r
precipitation_merge = rbind(precipitation_df_2018, precipitation_df_2019)
precipitation_merge$Month = month.name[precipitation_merge$Month]
```

-   The trash\_df dataset contains 344 rows and 14 columns. The median
    number of sports ball is 8

\*The precipitation\_df\_2018 dataset contains 12 rows and 3. The total
precipatation in 2018 is 70.33

\*The precipitation\_df\_2019 dataset contains 6 rows and 3. Because it
is missing a month 7 and after. Therefore, the recorded total
precipatation in 2018 is only 16.67. But that is not an accurate number.

## Question2

*import cvs files*

``` r
pols_month = read_csv(file = "./fivethirtyeight_datasets/pols-month.csv")
```

    ## Rows: 822 Columns: 9

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (8): prez_gop, gov_gop, sen_gop, rep_gop, prez_dem, gov_dem, sen_dem, r...
    ## date (1): mon

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pols_month_separated = separate(
  pols_month,
  mon,
  c("Year", "Month", "Date" ))

pols_month_separated$Month = month.name[as.integer(pols_month_separated$Month)]

pols_month_separated = pols_month_separated %>%
  mutate(president = case_when(
    prez_dem == 1 ~ 'dem',
    prez_dem == 0 ~ 'gop'
  )) %>%
  select(-c("prez_dem","prez_gop"))
```
