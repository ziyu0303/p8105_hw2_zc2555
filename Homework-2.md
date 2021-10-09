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
library(dplyr)
```

## Question 1

*Mr.Trash Wheel Sheet clean up*

``` r
trash_df = read_excel("./Trash_data.xlsx", 1) %>%
   janitor::clean_names() %>%
   select(-c(x15, x16, x17)) %>%
   drop_na() %>%
   mutate(sports_balls = round(sports_balls, digits = 0)) 
```

*Precipitation 2019 data cleaning*

``` r
precipitation_df_2019 = read_excel("./Trash_data.xlsx", 6, skip = 1) %>%
  drop_na()  %>%
add_column(Year = 2019)
```

*Precipitation 2018 data cleaning*

``` r
precipitation_df_2018 = read_excel("./Trash_data.xlsx", 7, skip = 1) %>%
  drop_na() %>%
  add_column(Year = 2018)
```

*merge 2019 and 2018 together*

``` r
precipitation_merge = rbind(precipitation_df_2018, precipitation_df_2019)
```

*The following paragraph describe the details about 3 datasets*

-   The trash\_df dataset contains 453 rows and 14 columns. The median
    number of sports ball is 9

-   The precipitation\_df\_2018 dataset contains 12 rows and 3 columns.
    The total precipitation in 2018 is 70.33

-   The precipitation\_df\_2019 dataset contains 12 rows and 3 columns.
    The recorded total precipitation in 2018 is only 33.95.

## Question2

*import cvs files* *clean pols\_month.csv*

``` r
pols_month = read_csv(file = "./fivethirtyeight_datasets/pols-month.csv")
pols_month_separated = separate(
  pols_month,
  mon,
  c("Year", "Month", "Date" )) %>%
  select(-"Date")
pols_month_separated =
  pols_month_separated %>% mutate(Month = month.name[as.integer(Month)],
                                  Year = as.integer(Year)) 
  
pols_month_separated = pols_month_separated %>%
  mutate(president = case_when(
    prez_dem == 1 ~ 'dem',
    prez_dem == 0 ~ 'gop'
  )) %>%
  select(-c("prez_dem","prez_gop"))
```

*clean snp.csv*

``` r
snp_df = read_csv(file = "./fivethirtyeight_datasets/snp.csv")
snp_df_separated = separate(
  snp_df,
  date,
  c("Month", "Date", "Year")) %>%
  select(-"Date")
snp_df_separated =
  snp_df_separated %>% mutate(Month = month.name[as.integer(Month)],
                              Year = as.integer(Year),
                              Year = ifelse(Year<= 21, Year+2000, Year+1900)
                              )
  
snp_df_separated = snp_df_separated %>% select (Year, Month, close)
```

*import and clean unemployment.csv*

``` r
unemployment_df = read_csv(file = "./fivethirtyeight_datasets/unemployment.csv")

unemployment_tidy_df= 
  pivot_longer(
    unemployment_df,
    Jan:Dec,
    names_to = "Month",
    values_to = "rate"
  ) %>% mutate(Year = as.integer(Year))
```

*merging 3 datasets into one*

``` r
merged_df=left_join(pols_month_separated, snp_df_separated, by=c("Year", "Month"))

merged_all=left_join(merged_df, unemployment_tidy_df,by=c("Year", "Month"))
```

*The following paragraph describe the details about 3 datasets*

-   After cleaning an tidying the dataset, it contains 787 obs and 3
    variables. Year month and close, Close will tell closing values of
    the S&P stock index on the associated date.The year range from
    1950-2015.

-   The cleaned version of unemployment data has 816 obs and 3
    variables. The varible rate will be the rate of umemployment in that
    month of the year.

-   After cleaning the pols\_month datadet, the pols\_month\_separated
    dataset contains 822 obs and 9 variables. The president variable is
    created based on Prez\_Dem and Prez\_gop, it can tell people which
    the gov is democrative or republican at anytime. The range of year
    is 1947 to 2015.

## Question 3

*cleaning up the data*

``` r
name_df = read_csv(file = "./Popular_Baby_Names.csv") %>%
  janitor::clean_names() %>%
  mutate(
  childs_first_name = str_to_sentence(childs_first_name),
  ethnicity = str_to_sentence(ethnicity),
  ethnicity = recode ( ethnicity,
                       'Asian and paci' = "Asian and pacific islander",
                       'Black non hisp' = "Black non hispanic",
                       'White non hisp' = "White non hispanic") )%>%
  distinct()
```

*table 1, the name of olivia*

``` r
table_olivia = name_df %>% 
  filter(childs_first_name=='Olivia') %>%
  select(c(year_of_birth,rank,ethnicity)) %>%
  pivot_wider(
    names_from = year_of_birth,
    values_from = rank
  )

table_olivia = table_olivia[,c('ethnicity', '2011', '2012', '2013', '2014', '2015', '2016')]

table_olivia
```

    ## # A tibble: 4 × 7
    ##   ethnicity                  `2011` `2012` `2013` `2014` `2015` `2016`
    ##   <chr>                       <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
    ## 1 Asian and pacific islander      4      3      3      1      1      1
    ## 2 Black non hispanic             10      8      6      8      4      8
    ## 3 Hispanic                       18     22     22     16     16     13
    ## 4 White non hispanic              2      4      1      1      1      1

``` r
table_male = name_df %>% 
  filter(gender=='MALE' & rank == '1') %>%
  select(c(year_of_birth,childs_first_name,ethnicity)) %>%
  pivot_wider(
    names_from = year_of_birth,
    values_from = childs_first_name
  )

table_male = table_male[,c('ethnicity', '2011', '2012', '2013', '2014', '2015', '2016')]

table_male
```

    ## # A tibble: 4 × 7
    ##   ethnicity                  `2011`  `2012` `2013` `2014` `2015` `2016`
    ##   <chr>                      <chr>   <chr>  <chr>  <chr>  <chr>  <chr> 
    ## 1 Asian and pacific islander Ethan   Ryan   Jayden Jayden Jayden Ethan 
    ## 2 Black non hispanic         Jayden  Jayden Ethan  Ethan  Noah   Noah  
    ## 3 Hispanic                   Jayden  Jayden Jayden Liam   Liam   Liam  
    ## 4 White non hispanic         Michael Joseph David  Joseph David  Joseph

``` r
df_plot = name_df %>%
  filter(gender=='MALE'&ethnicity=='White non hispanic'&year_of_birth=='2016')

ggplot(df_plot, aes(x=rank, y=count))+geom_point()
```

![](Homework-2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->
