data wrangling 2
================
Ling
10/19/2021

# homework

-   teamwork on HW

# data wrangling 2-1

## reading data from the web

-   data from online sources (i.e.,“scrape”)
    -   webpage=html+CSS
        -   html: content
        -   CSS: styling
    -   CSS selectors & Selector Gadget
        -   specify an appropriate CSS selector
        -   then extract data from html
        -   Selector Gadget: most common tool for finding the right CSS
            selector on a page (chrome extension)
    -   `rvest`
        -   `read_html()`
        -   `html_elements`
        -   `html_text`…:
    -   APIs: Application Programming Interfaces
        -   communicate w/ software
        -   `httr`: constructing HTTP requests
        -   JSON: JavaScript Object Notation
            -   download directly is NOT reproducible
            -   JSON is
            -   `jsonlite` parse the JSON files
        -   some R packages are wrappers for APIS
            -   i.e., `rnoaa`, `rtweet`

## practice

-   extracting tables
    -   National Survey on Drug Use and Health

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"
drug_use_html = read_html(url) #get the original html

drug_use = drug_use_html %>% #organizing in new dataframe
  html_table() %>% # extract all the tables that exist (total 15 in this html)
  first() %>% # need data organization, only get the 1st table
  slice(-1) # remove the notes in the 1st row
```

-   CSS selector using star war data

``` r
swm_html = 
  read_html("https://www.imdb.com/list/ls070150896/")

title_vec = 
  swm_html %>%
  html_elements(".lister-item-header a") %>%
  html_text()

gross_rev_vec = 
  swm_html %>%
  html_elements(".text-small:nth-child(7) span:nth-child(5)") %>%
  html_text()

runtime_vec = 
  swm_html %>%
  html_elements(".runtime") %>%
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    rev = gross_rev_vec,
    runtime = runtime_vec)
```

-   API
    -   data: NYC
    -   data: BRFSS
    -   data: pokemon API

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% # download a csv
  content("parsed") # get the parsed data structure in the csv
```

    ## Rows: 42 Columns: 4

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# another way to import data using JSON

nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% # all the contents is character
  jsonlite::fromJSON() %>% # get the dataframe
  as_tibble() # change it into a tibble

# another example: BRFSS
brfss_smart2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>% # restrict the full dataset, the default limit is 1000
  content("parsed")
```

    ## Rows: 5000 Columns: 23

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# practice: NYC opendata-restaurant

# pokemon API:

poke = 
  GET("http://pokeapi.co/api/v2/pokemon/1") %>%
  content()

poke$name
```

    ## [1] "bulbasaur"
