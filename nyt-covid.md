NYT Covid cases
================
Your name
2020-05-16

  - [States](#states)
      - [Glimpse and summary](#glimpse-and-summary)
      - [Check the states](#check-the-states)
      - [Missing values](#missing-values)
  - [Counties](#counties)
      - [Glimpse and summary](#glimpse-and-summary-1)
      - [Check counties and states](#check-counties-and-states)
      - [Missing values](#missing-values-1)
  - [Visualizations](#visualizations)
      - [U.S. cases over time](#u.s.-cases-over-time)
      - [Which state has the most
        cases?](#which-state-has-the-most-cases)
      - [Which state has the most cases per
        capita?](#which-state-has-the-most-cases-per-capita)
      - [State daily cases](#state-daily-cases)
  - [Counties](#counties-1)
  - [Compare trends across states](#compare-trends-across-states)

``` r
# Libraries
library(tidyverse)

# Parameters
  # To get these URLs, I:
    # Step 1: Go here: https://github.com/nytimes/covid-19-data
    # Step 2: Click on the CSV file 
    # Step 3: Click "Raw" or "View raw"
    # Step 4: Copy the URL

  # URL for the county data
url_counties <- 
  "https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-counties.csv"
  # URl for the state data
url_states <- 
  "https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv"
file_population <- "data/populations.csv"
#===============================================================================

# For reading in the data--we'll get to this in a bit!

# col_types = 
#   cols(
#     date = col_date(format = ""),
#     county = col_character(),
#     state = col_character(),
#     fips = col_integer(),
#     cases = col_double(),
#     deaths = col_double()
#   )
counties <-
  url_counties %>% 
  read_csv(
    col_types =
      cols(
        date = col_date(format = ""),
        county = col_character(),
        state = col_character(),
        fips = col_integer(),
        cases = col_double(),
        deaths = col_double()
      )
  )

# col_types = 
#   cols(
#     date = col_date(format = ""),
#     state = col_character(),
#     fips = col_integer(),
#     cases = col_double(),
#     deaths = col_double()
#   )
states <-
  url_states %>% 
  read_csv(
    col_types = 
      cols(
        date = col_date(format = ""),
        state = col_character(),
        fips = col_integer(),
        cases = col_double(),
        deaths = col_double()
      )
  )

# col_types =
#   cols(
#     region = col_character(),
#     fips = col_integer(),
#     population = col_double()
#   )
population <-
  file_population %>% 
  read_csv(
    col_types =
      cols(
        region = col_character(),
        fips = col_integer(),
        population = col_double()
      )
  )
```

## States

### Glimpse and summary

### Check the states

``` r
n_distinct(states$state)
```

    ## [1] 55

``` r
unique(states$state)
```

    ##  [1] "Washington"               "Illinois"                
    ##  [3] "California"               "Arizona"                 
    ##  [5] "Massachusetts"            "Wisconsin"               
    ##  [7] "Texas"                    "Nebraska"                
    ##  [9] "Utah"                     "Oregon"                  
    ## [11] "Florida"                  "New York"                
    ## [13] "Rhode Island"             "Georgia"                 
    ## [15] "New Hampshire"            "North Carolina"          
    ## [17] "New Jersey"               "Colorado"                
    ## [19] "Maryland"                 "Nevada"                  
    ## [21] "Tennessee"                "Hawaii"                  
    ## [23] "Indiana"                  "Kentucky"                
    ## [25] "Minnesota"                "Oklahoma"                
    ## [27] "Pennsylvania"             "South Carolina"          
    ## [29] "District of Columbia"     "Kansas"                  
    ## [31] "Missouri"                 "Vermont"                 
    ## [33] "Virginia"                 "Connecticut"             
    ## [35] "Iowa"                     "Louisiana"               
    ## [37] "Ohio"                     "Michigan"                
    ## [39] "South Dakota"             "Arkansas"                
    ## [41] "Delaware"                 "Mississippi"             
    ## [43] "New Mexico"               "North Dakota"            
    ## [45] "Wyoming"                  "Alaska"                  
    ## [47] "Maine"                    "Alabama"                 
    ## [49] "Idaho"                    "Montana"                 
    ## [51] "Puerto Rico"              "Virgin Islands"          
    ## [53] "Guam"                     "West Virginia"           
    ## [55] "Northern Mariana Islands"

``` r
states %>% 
  filter(!state %in% state.name) %>% 
  count(state, sort = TRUE)
```

    ## # A tibble: 5 x 2
    ##   state                        n
    ##   <chr>                    <int>
    ## 1 District of Columbia        70
    ## 2 Puerto Rico                 64
    ## 3 Virgin Islands              63
    ## 4 Guam                        62
    ## 5 Northern Mariana Islands    49

### Missing values

``` r
states %>% 
  summarize_all(~sum(is.na(.)))
```

    ## # A tibble: 1 x 5
    ##    date state  fips cases deaths
    ##   <int> <int> <int> <int>  <int>
    ## 1     0     0     0     0      0

`cases` and `deaths` are the *cumulative* counts, so they should never
go down (see the
[documentation](https://github.com/nytimes/covid-19-data)). Is that
true?

``` r
states %>% 
  group_by(
    # TODO: ADD NAME OF VARIABLE
  ) %>% 
  filter(lead(cases, order_by = date) < cases)
```

    ## # A tibble: 2,112 x 5
    ##    date       state       fips cases deaths
    ##    <date>     <chr>      <int> <dbl>  <dbl>
    ##  1 2020-01-26 California     6     2      0
    ##  2 2020-01-27 California     6     2      0
    ##  3 2020-01-28 California     6     2      0
    ##  4 2020-01-29 California     6     2      0
    ##  5 2020-01-30 Illinois      17     2      0
    ##  6 2020-01-31 California     6     3      0
    ##  7 2020-01-31 Illinois      17     2      0
    ##  8 2020-02-01 California     6     3      0
    ##  9 2020-02-01 Illinois      17     2      0
    ## 10 2020-02-02 California     6     6      0
    ## # … with 2,102 more rows

``` r
states %>% 
  group_by(
    # TODO: ADD NAME OF VARIABLE
  ) %>% 
  filter(lead(deaths, order_by = date) < deaths)
```

    ## # A tibble: 1,596 x 5
    ##    date       state       fips cases deaths
    ##    <date>     <chr>      <int> <dbl>  <dbl>
    ##  1 2020-02-29 Washington    53    10      1
    ##  2 2020-03-01 Washington    53    17      3
    ##  3 2020-03-02 Washington    53    23      6
    ##  4 2020-03-03 Washington    53    32     10
    ##  5 2020-03-04 California     6    55      1
    ##  6 2020-03-04 Washington    53    47     11
    ##  7 2020-03-05 California     6    67      1
    ##  8 2020-03-05 Washington    53    75     11
    ##  9 2020-03-06 California     6    81      1
    ## 10 2020-03-06 Florida       12     7      2
    ## # … with 1,586 more rows

## Counties

### Glimpse and summary

``` r
glimpse(counties)
```

    ## Rows: 147,245
    ## Columns: 6
    ## $ date   <date> 2020-01-21, 2020-01-22, 2020-01-23, 2020-01-24, 2020-01-24, 2…
    ## $ county <chr> "Snohomish", "Snohomish", "Snohomish", "Cook", "Snohomish", "O…
    ## $ state  <chr> "Washington", "Washington", "Washington", "Illinois", "Washing…
    ## $ fips   <int> 53061, 53061, 53061, 17031, 53061, 6059, 17031, 53061, 4013, 6…
    ## $ cases  <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    ## $ deaths <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…

``` r
summary(counties)
```

    ##       date               county             state                fips      
    ##  Min.   :2020-01-21   Length:147245      Length:147245      Min.   : 1001  
    ##  1st Qu.:2020-04-06   Class :character   Class :character   1st Qu.:18053  
    ##  Median :2020-04-20   Mode  :character   Mode  :character   Median :29033  
    ##  Mean   :2020-04-18                                         Mean   :29957  
    ##  3rd Qu.:2020-05-03                                         3rd Qu.:45027  
    ##  Max.   :2020-05-15                                         Max.   :56043  
    ##                                                             NA's   :1657   
    ##      cases              deaths        
    ##  Min.   :     0.0   Min.   :    0.00  
    ##  1st Qu.:     3.0   1st Qu.:    0.00  
    ##  Median :    13.0   Median :    0.00  
    ##  Mean   :   271.4   Mean   :   14.62  
    ##  3rd Qu.:    61.0   3rd Qu.:    2.00  
    ##  Max.   :195472.0   Max.   :19972.00  
    ## 

### Check counties and states

``` r
n_distinct(counties$county)
```

    ## [1] 1726

``` r
n_distinct(counties$state)
```

    ## [1] 55

``` r
unique(counties$state)
```

    ##  [1] "Washington"               "Illinois"                
    ##  [3] "California"               "Arizona"                 
    ##  [5] "Massachusetts"            "Wisconsin"               
    ##  [7] "Texas"                    "Nebraska"                
    ##  [9] "Utah"                     "Oregon"                  
    ## [11] "Florida"                  "New York"                
    ## [13] "Rhode Island"             "Georgia"                 
    ## [15] "New Hampshire"            "North Carolina"          
    ## [17] "New Jersey"               "Colorado"                
    ## [19] "Maryland"                 "Nevada"                  
    ## [21] "Tennessee"                "Hawaii"                  
    ## [23] "Indiana"                  "Kentucky"                
    ## [25] "Minnesota"                "Oklahoma"                
    ## [27] "Pennsylvania"             "South Carolina"          
    ## [29] "District of Columbia"     "Kansas"                  
    ## [31] "Missouri"                 "Vermont"                 
    ## [33] "Virginia"                 "Connecticut"             
    ## [35] "Iowa"                     "Louisiana"               
    ## [37] "Ohio"                     "Michigan"                
    ## [39] "South Dakota"             "Arkansas"                
    ## [41] "Delaware"                 "Mississippi"             
    ## [43] "New Mexico"               "North Dakota"            
    ## [45] "Wyoming"                  "Alaska"                  
    ## [47] "Maine"                    "Alabama"                 
    ## [49] "Idaho"                    "Montana"                 
    ## [51] "Puerto Rico"              "Virgin Islands"          
    ## [53] "Guam"                     "West Virginia"           
    ## [55] "Northern Mariana Islands"

### Missing values

``` r
counties %>% 
  summarize_all(~sum(is.na(.)))
```

    ## # A tibble: 1 x 6
    ##    date county state  fips cases deaths
    ##   <int>  <int> <int> <int> <int>  <int>
    ## 1     0      0     0  1657     0      0

``` r
# TODO: investigate further
```

## Visualizations

### U.S. cases over time

``` r
us <-
  states %>% 
  group_by(date) %>% 
  summarize(cases = sum(cases))
```

    ## `summarise()` ungrouping (override with `.groups` argument)

``` r
# us %>% 
#   ggplot(aes(TODO: Add x and y variables)) +
#   geom_line()
```

``` r
us_daily <-
  us %>% 
  mutate(new_cases = cases - lag(cases, order_by = date)) 

us_daily %>% 
  drop_na(new_cases) %>% 
  filter(new_cases > 0) %>% # TODO: filter to just March through May
  ggplot(aes(date, new_cases)) +
  geom_col() 
```

![](nyt-covid_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

### Which state has the most cases?

``` r
states_cumulative <-
  states %>% 
  group_by(fips, state) %>% 
  summarize(cases = sum(cases, na.rm = TRUE)) %>% 
  ungroup()
```

    ## `summarise()` regrouping by 'fips' (override with `.groups` argument)

``` r
# This plot is not very useful! Remove to comment to make it better
states_cumulative %>% 
  # mutate(state = fct_reorder(state, cases)) %>% 
  ggplot(aes(cases, state)) +
  geom_point()
```

![](nyt-covid_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

### Which state has the most cases per capita?

``` r
states_cumulative %>% 
  left_join(population, by = "fips") # %>% 
```

    ## # A tibble: 55 x 5
    ##     fips state                  cases region               population
    ##    <int> <chr>                  <dbl> <chr>                     <dbl>
    ##  1     1 Alabama               271116 Alabama                 4903185
    ##  2     2 Alaska                 14704 Alaska                   731545
    ##  3     4 Arizona               292111 Arizona                 7278717
    ##  4     5 Arkansas              114726 Arkansas                3017804
    ##  5     6 California           1885915 California             39512223
    ##  6     8 Colorado              560913 Colorado                5758736
    ##  7     9 Connecticut           965972 Connecticut             3565287
    ##  8    10 Delaware              160596 Delaware                 973764
    ##  9    11 District of Columbia  161289 District of Columbia     705749
    ## 10    12 Florida              1296507 Florida                21477737
    ## # … with 45 more rows

``` r
  # filter(!is.na(population)) %>% 
  # mutate(
  #   cases_per_capita = population / cases,
  #   state = fct_reorder(state, cases_per_capita)
  # ) %>% 
  # ggplot(aes(cases_per_capita, state)) +
  # geom_point()
```

### State daily cases

``` r
states %>% 
  group_by(state) %>% 
  mutate(new_cases = cases - lag(cases, order_by = date)) %>% 
  ungroup() # %>% 
```

    ## # A tibble: 4,084 x 6
    ##    date       state       fips cases deaths new_cases
    ##    <date>     <chr>      <int> <dbl>  <dbl>     <dbl>
    ##  1 2020-01-21 Washington    53     1      0        NA
    ##  2 2020-01-22 Washington    53     1      0         0
    ##  3 2020-01-23 Washington    53     1      0         0
    ##  4 2020-01-24 Illinois      17     1      0        NA
    ##  5 2020-01-24 Washington    53     1      0         0
    ##  6 2020-01-25 California     6     1      0        NA
    ##  7 2020-01-25 Illinois      17     1      0         0
    ##  8 2020-01-25 Washington    53     1      0         0
    ##  9 2020-01-26 Arizona        4     1      0        NA
    ## 10 2020-01-26 California     6     2      0         1
    ## # … with 4,074 more rows

``` r
  # drop_na(new_cases) %>% 
  # filter(state == "TODO: CHOOSE A STATE") %>% 
  # ggplot(aes(date, new_cases)) +
  # geom_col()
```

## Counties

``` r
counties_cumulative <-
  counties %>% 
  drop_na(fips) %>% 
  group_by(county, state, fips) %>% 
  summarize(cases = sum(cases, na.rm = TRUE)) %>% 
  ungroup()
```

    ## `summarise()` regrouping by 'county', 'state' (override with `.groups` argument)

``` r
counties_cumulative %>% 
  top_n(n = 15, wt = cases) %>% 
  mutate(
    county = str_c(county, ", ", state) %>% fct_reorder(cases)
  ) 
```

    ## # A tibble: 15 x 4
    ##    county                     state          fips   cases
    ##    <fct>                      <chr>         <int>   <dbl>
    ##  1 Bergen, New Jersey         New Jersey    34003  588060
    ##  2 Cook, Illinois             Illinois      17031 1329345
    ##  3 Essex, New Jersey          New Jersey    34013  493091
    ##  4 Hudson, New Jersey         New Jersey    34017  518008
    ##  5 Los Angeles, California    California     6037  829987
    ##  6 Miami-Dade, Florida        Florida       12086  453063
    ##  7 Middlesex, Massachusetts   Massachusetts 25017  481463
    ##  8 Nassau, New York           New York      36059 1373748
    ##  9 Philadelphia, Pennsylvania Pennsylvania  42101  501162
    ## 10 Rockland, New York         New York      36087  446773
    ## 11 Suffolk, Massachusetts     Massachusetts 25025  434166
    ## 12 Suffolk, New York          New York      36103 1261594
    ## 13 Union, New Jersey          New Jersey    34039  442661
    ## 14 Wayne, Michigan            Michigan      26163  645806
    ## 15 Westchester, New York      New York      36119 1156763

``` r
# TODO: plot cases for top N counties
```

## Compare trends across states

``` r
# TODO: Add or change the states
# TODO: Try a log scale
states %>% 
  filter(state %in% c("California", "Georgia")) %>% 
  ggplot(aes(date, cases, color = state)) +
  geom_line() 
```

![](nyt-covid_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->
