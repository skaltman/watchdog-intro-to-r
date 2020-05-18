NYT Covid cases
================
Your name
2020-05-17

  - [States](#states)
      - [Summary](#summary)
      - [Check for problems](#check-for-problems)
      - [Missing values](#missing-values)
  - [Counties](#counties)
      - [Glimpse and summary](#glimpse-and-summary)
      - [Check for problems](#check-for-problems-1)
      - [Missing values](#missing-values-1)
  - [Visualizations](#visualizations)
      - [U.S.](#u.s.)
      - [States](#states-1)
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
  read_csv() 

counties
```

    ## # A tibble: 150,169 x 6
    ##    date       county      state      fips  cases deaths
    ##    <date>     <chr>       <chr>      <chr> <dbl>  <dbl>
    ##  1 2020-01-21 Snohomish   Washington 53061     1      0
    ##  2 2020-01-22 Snohomish   Washington 53061     1      0
    ##  3 2020-01-23 Snohomish   Washington 53061     1      0
    ##  4 2020-01-24 Cook        Illinois   17031     1      0
    ##  5 2020-01-24 Snohomish   Washington 53061     1      0
    ##  6 2020-01-25 Orange      California 06059     1      0
    ##  7 2020-01-25 Cook        Illinois   17031     1      0
    ##  8 2020-01-25 Snohomish   Washington 53061     1      0
    ##  9 2020-01-26 Maricopa    Arizona    04013     1      0
    ## 10 2020-01-26 Los Angeles California 06037     1      0
    ## # … with 150,159 more rows

``` r
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

### Summary

``` r
summary(states)
```

    ##       date               state                fips          cases       
    ##  Min.   :2020-01-21   Length:4139        Min.   : 1.0   Min.   :     1  
    ##  1st Qu.:2020-03-21   Class :character   1st Qu.:17.0   1st Qu.:    69  
    ##  Median :2020-04-09   Mode  :character   Median :31.0   Median :  1146  
    ##  Mean   :2020-04-07                      Mean   :31.5   Mean   : 10010  
    ##  3rd Qu.:2020-04-28                      3rd Qu.:46.0   3rd Qu.:  6644  
    ##  Max.   :2020-05-16                      Max.   :78.0   Max.   :353136  
    ##      deaths       
    ##  Min.   :    0.0  
    ##  1st Qu.:    1.0  
    ##  Median :   27.0  
    ##  Mean   :  541.6  
    ##  3rd Qu.:  235.5  
    ##  Max.   :27953.0

### Check for problems

### Missing values

## Counties

### Glimpse and summary

``` r
glimpse(counties)
```

    ## Rows: 150,169
    ## Columns: 6
    ## $ date   <date> 2020-01-21, 2020-01-22, 2020-01-23, 2020-01-24, 2020-01-24, 2…
    ## $ county <chr> "Snohomish", "Snohomish", "Snohomish", "Cook", "Snohomish", "O…
    ## $ state  <chr> "Washington", "Washington", "Washington", "Illinois", "Washing…
    ## $ fips   <chr> "53061", "53061", "53061", "17031", "53061", "06059", "17031",…
    ## $ cases  <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    ## $ deaths <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…

``` r
summary(counties)
```

    ##       date               county             state               fips          
    ##  Min.   :2020-01-21   Length:150169      Length:150169      Length:150169     
    ##  1st Qu.:2020-04-06   Class :character   Class :character   Class :character  
    ##  Median :2020-04-20   Mode  :character   Mode  :character   Mode  :character  
    ##  Mean   :2020-04-19                                                           
    ##  3rd Qu.:2020-05-04                                                           
    ##  Max.   :2020-05-16                                                           
    ##      cases              deaths        
    ##  Min.   :     0.0   Min.   :    0.00  
    ##  1st Qu.:     3.0   1st Qu.:    0.00  
    ##  Median :    14.0   Median :    0.00  
    ##  Mean   :   275.9   Mean   :   14.93  
    ##  3rd Qu.:    63.0   3rd Qu.:    2.00  
    ##  Max.   :196481.0   Max.   :20071.00

### Check for problems

``` r
n_distinct(counties$county)
```

    ## [1] 1727

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

## Visualizations

### U.S.

``` r
us <-
  states %>% 
  group_by(date) %>% 
  summarize(cases = sum(cases))
```

    ## `summarise()` ungrouping (override with `.groups` argument)

``` r
us_daily <-
  us %>% 
  mutate(new_cases = cases - lag(cases, order_by = date)) 
```

### States

``` r
states_cumulative <-
  states %>% 
  group_by(fips, state) %>% 
  summarize(cases = sum(cases, na.rm = TRUE)) %>% 
  ungroup()
```

    ## `summarise()` regrouping by 'fips' (override with `.groups` argument)

### Counties

``` r
counties_cumulative <-
  counties %>% 
  drop_na(fips) %>% 
  group_by(county, state, fips) %>% 
  summarize(cases = sum(cases, na.rm = TRUE)) %>% 
  ungroup()
```

    ## `summarise()` regrouping by 'county', 'state' (override with `.groups` argument)

## Compare trends across states
