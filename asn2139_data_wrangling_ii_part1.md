reading data from the web
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.0

    ## ── Conflicts ───────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(rvest)
```

    ## Loading required package: xml2

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     pluck

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

\#\#Scrape a table

I want the first table from

read in the html

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html=read_html(url)
```

extract the table(s)

``` r
table_marj=
drug_use_html %>% 
  html_nodes(css="table") %>% 
  first() %>% 
  html_table() %>% 
  slice(-1) %>% 
  as_tibble()
```

## Star Wars Movie info

I want the data from \[here\]
(<https://www.imdb.com/list/ls070150896/>).

``` r
url="https://www.imdb.com/list/ls070150896/"

swm_html=read_html(url)
```

Grab elements I want

``` r
title_vec = 
  swm_html %>%
  html_nodes(".lister-item-header a") %>%
  html_text()
  
gross_rev_vec=
  swm_html %>% 
  html_nodes(css=".text-small:nth-child(7) span:nth-child(5)") %>% 
  html_text()

runtime_vec=
   swm_html %>% 
  html_nodes(css=".runtime") %>% 
  html_text()
  
swm_df=
  tibble(
    tile=title_vec,
    gross_rev=gross_rev_vec,
    runtime=runtime_vec)
```

## Get some water data

This is coming from an API

``` r
nyc_water=
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% 
  content("parsed")
```

    ## 
    ## ── Column specification ───────────────────────────────────────────────────────────────────────────
    ## cols(
    ##   year = col_double(),
    ##   new_york_city_population = col_double(),
    ##   nyc_consumption_million_gallons_per_day = col_double(),
    ##   per_capita_gallons_per_person_per_day = col_double()
    ## )

``` r
nyc_water=
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble()
```

\#BRFSS

Same process, different data

``` r
brfss_2010=
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query=list("$limit"=5000)) %>% 
  content("parsed")
```

    ## 
    ## ── Column specification ───────────────────────────────────────────────────────────────────────────
    ## cols(
    ##   .default = col_character(),
    ##   year = col_double(),
    ##   sample_size = col_double(),
    ##   data_value = col_double(),
    ##   confidence_limit_low = col_double(),
    ##   confidence_limit_high = col_double(),
    ##   display_order = col_double(),
    ##   locationid = col_logical()
    ## )
    ## ℹ Use `spec()` for the full column specifications.

## Pokemon Data

``` r
pokemon_data= 
  GET("http://pokeapi.co/api/v2/pokemon/1") %>%
  content()

pokemon_data$name
```

    ## [1] "bulbasaur"
