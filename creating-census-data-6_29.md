Census Data
================
Ally Rakus
6/25/2020

Creating my Census API Key

``` r
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ─────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(tidycensus)

census_api_key("d432da892f8a6ce576e962e7170b27cf7ef13f8d", overwrite = TRUE, install = TRUE) 
```

    ## Your original .Renviron will be backed up and stored in your R HOME directory if needed.

    ## Your API key has been stored in your .Renviron and can be accessed by Sys.getenv("CENSUS_API_KEY"). 
    ## To use now, restart R or run `readRenviron("~/.Renviron")`

    ## [1] "d432da892f8a6ce576e962e7170b27cf7ef13f8d"

Looking at the Census Variables

``` r
v18 <- load_variables(2018, "acs5", cache = TRUE)
v18
```

    ## # A tibble: 26,996 x 3
    ##    name       label                           concept                           
    ##    <chr>      <chr>                           <chr>                             
    ##  1 B00001_001 Estimate!!Total                 UNWEIGHTED SAMPLE COUNT OF THE PO…
    ##  2 B00002_001 Estimate!!Total                 UNWEIGHTED SAMPLE HOUSING UNITS   
    ##  3 B01001_001 Estimate!!Total                 SEX BY AGE                        
    ##  4 B01001_002 Estimate!!Total!!Male           SEX BY AGE                        
    ##  5 B01001_003 Estimate!!Total!!Male!!Under 5… SEX BY AGE                        
    ##  6 B01001_004 Estimate!!Total!!Male!!5 to 9 … SEX BY AGE                        
    ##  7 B01001_005 Estimate!!Total!!Male!!10 to 1… SEX BY AGE                        
    ##  8 B01001_006 Estimate!!Total!!Male!!15 to 1… SEX BY AGE                        
    ##  9 B01001_007 Estimate!!Total!!Male!!18 and … SEX BY AGE                        
    ## 10 B01001_008 Estimate!!Total!!Male!!20 years SEX BY AGE                        
    ## # … with 26,986 more rows

Call for the variables I need: income, poverty rate, educational
attainment, unemployment, race/ethnicity

1.  Calling for income and poverty rate

<!-- end list -->

``` r
bmore_census_inc_pov <- get_acs(
  geography = "tract", 
  state = "MD",
  county = c("510"),
  variables = c(
    "hh_inc_med" = "B19013_001", # median household inc
    "pov_pct" = "C17002_001" # poverty rate
  ), 
  survey = "acs5",
  output = "wide",
  geometry = FALSE)
```

    ## Getting data from the 2014-2018 5-year ACS

    ## Warning: `funs()` is deprecated as of dplyr 0.8.0.
    ## Please use a list of either functions or lambdas: 
    ## 
    ##   # Simple named list: 
    ##   list(mean = mean, median = median)
    ## 
    ##   # Auto named with `tibble::lst()`: 
    ##   tibble::lst(mean, median)
    ## 
    ##   # Using lambdas
    ##   list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.

``` r
bmore_census_inc_pov
```

    ## # A tibble: 200 x 6
    ##    GEOID     NAME                      hh_inc_medE hh_inc_medM pov_pctE pov_pctM
    ##    <chr>     <chr>                           <dbl>       <dbl>    <dbl>    <dbl>
    ##  1 24510110… Census Tract 1102, Balti…       45406        7669     5495      411
    ##  2 24510020… Census Tract 203, Baltim…      108516       20890     3877      509
    ##  3 24510150… Census Tract 1504, Balti…       35479        8331     3341      527
    ##  4 24510010… Census Tract 102, Baltim…      107617       13833     2860      208
    ##  5 24510060… Census Tract 604, Baltim…       55368        8905     1371      161
    ##  6 24510160… Census Tract 1604, Balti…       26552        9316     2323      354
    ##  7 24510160… Census Tract 1607, Balti…       36346        3640     4324      554
    ##  8 24510170… Census Tract 1702, Balti…       13643        3533     2971      442
    ##  9 24510180… Census Tract 1801, Balti…       14583        5847     2025      204
    ## 10 24510180… Census Tract 1803, Balti…       37273       34737     1513      236
    ## # … with 190 more rows

2.  Calling for the unemployment table then manipulating that data

<!-- end list -->

``` r
bmore_census_unemploy <- get_acs(
  geography = "tract", 
  state = "MD",
  county = c("510"),
  table = "B23001",
  survey = "acs5",
  output = "wide",
  geometry = FALSE)
```

    ## Getting data from the 2014-2018 5-year ACS

``` r
bmore_census_unemploy
```

    ## # A tibble: 200 x 348
    ##    GEOID NAME  B23001_001E B23001_001M B23001_002E B23001_002M B23001_003E
    ##    <chr> <chr>       <dbl>       <dbl>       <dbl>       <dbl>       <dbl>
    ##  1 2451… Cens…        5552         402        2613         321          96
    ##  2 2451… Cens…        3634         447        1811         292           0
    ##  3 2451… Cens…        3598         469        1582         343         201
    ##  4 2451… Cens…        2537         181        1285         147           8
    ##  5 2451… Cens…        1194         125         530          86           9
    ##  6 2451… Cens…        1570         190         777         154          51
    ##  7 2451… Cens…        3288         353        1486         211         122
    ##  8 2451… Cens…        2167         316         867         250         115
    ##  9 2451… Cens…        1402         142         432          88          62
    ## 10 2451… Cens…        1380         205         776         159           0
    ## # … with 190 more rows, and 341 more variables: B23001_003M <dbl>,
    ## #   B23001_004E <dbl>, B23001_004M <dbl>, B23001_005E <dbl>, B23001_005M <dbl>,
    ## #   B23001_006E <dbl>, B23001_006M <dbl>, B23001_007E <dbl>, B23001_007M <dbl>,
    ## #   B23001_008E <dbl>, B23001_008M <dbl>, B23001_009E <dbl>, B23001_009M <dbl>,
    ## #   B23001_010E <dbl>, B23001_010M <dbl>, B23001_011E <dbl>, B23001_011M <dbl>,
    ## #   B23001_012E <dbl>, B23001_012M <dbl>, B23001_013E <dbl>, B23001_013M <dbl>,
    ## #   B23001_014E <dbl>, B23001_014M <dbl>, B23001_015E <dbl>, B23001_015M <dbl>,
    ## #   B23001_016E <dbl>, B23001_016M <dbl>, B23001_017E <dbl>, B23001_017M <dbl>,
    ## #   B23001_018E <dbl>, B23001_018M <dbl>, B23001_019E <dbl>, B23001_019M <dbl>,
    ## #   B23001_020E <dbl>, B23001_020M <dbl>, B23001_021E <dbl>, B23001_021M <dbl>,
    ## #   B23001_022E <dbl>, B23001_022M <dbl>, B23001_023E <dbl>, B23001_023M <dbl>,
    ## #   B23001_024E <dbl>, B23001_024M <dbl>, B23001_025E <dbl>, B23001_025M <dbl>,
    ## #   B23001_026E <dbl>, B23001_026M <dbl>, B23001_027E <dbl>, B23001_027M <dbl>,
    ## #   B23001_028E <dbl>, B23001_028M <dbl>, B23001_029E <dbl>, B23001_029M <dbl>,
    ## #   B23001_030E <dbl>, B23001_030M <dbl>, B23001_031E <dbl>, B23001_031M <dbl>,
    ## #   B23001_032E <dbl>, B23001_032M <dbl>, B23001_033E <dbl>, B23001_033M <dbl>,
    ## #   B23001_034E <dbl>, B23001_034M <dbl>, B23001_035E <dbl>, B23001_035M <dbl>,
    ## #   B23001_036E <dbl>, B23001_036M <dbl>, B23001_037E <dbl>, B23001_037M <dbl>,
    ## #   B23001_038E <dbl>, B23001_038M <dbl>, B23001_039E <dbl>, B23001_039M <dbl>,
    ## #   B23001_040E <dbl>, B23001_040M <dbl>, B23001_041E <dbl>, B23001_041M <dbl>,
    ## #   B23001_042E <dbl>, B23001_042M <dbl>, B23001_043E <dbl>, B23001_043M <dbl>,
    ## #   B23001_044E <dbl>, B23001_044M <dbl>, B23001_045E <dbl>, B23001_045M <dbl>,
    ## #   B23001_046E <dbl>, B23001_046M <dbl>, B23001_047E <dbl>, B23001_047M <dbl>,
    ## #   B23001_048E <dbl>, B23001_048M <dbl>, B23001_049E <dbl>, B23001_049M <dbl>,
    ## #   B23001_050E <dbl>, B23001_050M <dbl>, B23001_051E <dbl>, B23001_051M <dbl>,
    ## #   B23001_052E <dbl>, B23001_052M <dbl>, B23001_053E <dbl>, …

Creating variables: labor force, unemployed, unemployment rate

``` r
# These operations with rowwise() can be quite confusing, but unfortunately it's
# necessary given how the moe_sum() function works. The regular expression is
# also a bit confusing, but is also a bit easier to read than if you had to
# write out every variable name in full. What the matches() is doing is
# selecting all the columns whose names match this regular expression. And the
# regular expression (regex) is a way to concisely write a pattern that it
# should match. My first attempt at this wasn't actually working as intended. I
# did some testing and this works now. You can see how i tested it out here
# https://regex101.com/r/KGti8P/1 I copied the names from the dataset and then
# wrote the regex and made sure it was selecting everything we wanted (and
# nothing extra!)

bmore_census_unemployment <- bmore_census_unemploy %>% 
  rowwise() %>% 
  mutate(
    
    laborforce_est = sum(
      c_across(matches("B23001_0{0,2}(6|13|20|27|34|41|48|55|62|69|74|79|84|92|99|106|113|120|127|134|141|148|155|160|165|170)E"))
    ),
    
    laborforce_moe = moe_sum(
      moe = c_across(matches("B23001_0{0,2}(6|13|20|27|34|41|48|55|62|69|74|79|84|92|99|106|113|120|127|134|141|148|155|160|165|170)M")),
      estimate = laborforce_est
    ),
    
    pop_unemployed_est = sum(
      c_across(c(matches("B23001_0{0,2}(8|15|22|29|36|43|50|57|64|71|76|81|86|94|101|108|115|122|129|136|143|150|157|162|167|172)E")))
    ),
    
    pop_unemployed_moe = moe_sum(
      moe = c_across(c(matches("B23001_0{0,2}(8|15|22|29|36|43|50|57|64|71|76|81|86|94|101|108|115|122|129|136|143|150|157|162|167|172)M"))),
      estimate = pop_unemployed_est)
  ) %>% 
  ungroup() %>%
  mutate(
    unemploy_rate_est = pop_unemployed_est / na_if(laborforce_est,0),
    unemploy_rate_moe = moe_prop(pop_unemployed_est, na_if(laborforce_est,0), pop_unemployed_moe, laborforce_moe)
  )

# just for this preview, remove all columns matching this pattern, so we can
# more easily see our new columns
bmore_census_unemployment %>% 
  select(-matches("B\\d{5}_\\d{3}."))
```

    ## # A tibble: 200 x 8
    ##    GEOID NAME  laborforce_est laborforce_moe pop_unemployed_… pop_unemployed_…
    ##    <chr> <chr>          <dbl>          <dbl>            <dbl>            <dbl>
    ##  1 2451… Cens…           4003           424.              149            112. 
    ##  2 2451… Cens…           2968           405.               56             78.2
    ##  3 2451… Cens…           1719           357.              205            105. 
    ##  4 2451… Cens…           2130           259.               30             65.7
    ##  5 2451… Cens…            726           126.               15             59.2
    ##  6 2451… Cens…            878           180.              114             88.4
    ##  7 2451… Cens…           2026           308.              290            113. 
    ##  8 2451… Cens…            972           233.              132            102. 
    ##  9 2451… Cens…            745           165.              157             84.7
    ## 10 2451… Cens…           1069           203.               49             67.4
    ## # … with 190 more rows, and 2 more variables: unemploy_rate_est <dbl>,
    ## #   unemploy_rate_moe <dbl>

3.  Calling the education table then manipulating that data

<!-- end list -->

``` r
bmore_census_educ <- get_acs(
  geography = "tract", 
  state = "MD",
  county = c("510"),
  table = "B15003",
  survey = "acs5",
  output = "wide",
  geometry = FALSE)
```

    ## Getting data from the 2014-2018 5-year ACS

``` r
bmore_census_educ
```

    ## # A tibble: 200 x 52
    ##    GEOID NAME  B15003_001E B15003_001M B15003_002E B15003_002M B15003_003E
    ##    <chr> <chr>       <dbl>       <dbl>       <dbl>       <dbl>       <dbl>
    ##  1 2451… Cens…        4697         376          65          69           0
    ##  2 2451… Cens…        3498         449          20          32           0
    ##  3 2451… Cens…        2391         363          19          21           0
    ##  4 2451… Cens…        2383         172           0          12           0
    ##  5 2451… Cens…        1121         129           8          10           0
    ##  6 2451… Cens…        1268         182          22          21           0
    ##  7 2451… Cens…        2757         300          23          27           0
    ##  8 2451… Cens…        1669         235          27          33           0
    ##  9 2451… Cens…        1170         133          35          23           0
    ## 10 2451… Cens…        1216         188           0          12           0
    ## # … with 190 more rows, and 45 more variables: B15003_003M <dbl>,
    ## #   B15003_004E <dbl>, B15003_004M <dbl>, B15003_005E <dbl>, B15003_005M <dbl>,
    ## #   B15003_006E <dbl>, B15003_006M <dbl>, B15003_007E <dbl>, B15003_007M <dbl>,
    ## #   B15003_008E <dbl>, B15003_008M <dbl>, B15003_009E <dbl>, B15003_009M <dbl>,
    ## #   B15003_010E <dbl>, B15003_010M <dbl>, B15003_011E <dbl>, B15003_011M <dbl>,
    ## #   B15003_012E <dbl>, B15003_012M <dbl>, B15003_013E <dbl>, B15003_013M <dbl>,
    ## #   B15003_014E <dbl>, B15003_014M <dbl>, B15003_015E <dbl>, B15003_015M <dbl>,
    ## #   B15003_016E <dbl>, B15003_016M <dbl>, B15003_017E <dbl>, B15003_017M <dbl>,
    ## #   B15003_018E <dbl>, B15003_018M <dbl>, B15003_019E <dbl>, B15003_019M <dbl>,
    ## #   B15003_020E <dbl>, B15003_020M <dbl>, B15003_021E <dbl>, B15003_021M <dbl>,
    ## #   B15003_022E <dbl>, B15003_022M <dbl>, B15003_023E <dbl>, B15003_023M <dbl>,
    ## #   B15003_024E <dbl>, B15003_024M <dbl>, B15003_025E <dbl>, B15003_025M <dbl>

Creating variables:

``` r
bmore_census_education <- bmore_census_educ %>%
  mutate(
    pop_edu_denom_est = B15003_001E,
    pop_edu_denom_mpe = B15003_001M,
  ) %>%
  rowwise() %>%
  mutate(
    # Did you mean for this to be population without a High School? 
    # (I think that might be more helpful than % with just HS)
    pop_edu_nohs_num_est = sum(c_across(c(matches("B15003_0{0,2}(2|3|4|5|6|7|8|9|10|11|12|13|14|15|16)E")))),
    pop_edu_nohs_num_moe = moe_sum(
      moe = c_across(c(matches("B15003_0{0,2}(2|3|4|5|6|7|8|9|10|11|12|13|14|15|16)M"))),
      estimate = pop_edu_nohs_num_est
    ),
    pop_edu_somecoll_plus_est = sum(c_across(c(matches("B15003_0{0,2}(19|20|21|22|23|24|25)E")))),
    pop_edu_somecoll_plus_num = moe_sum(
      moe = c_across(c(matches("B15003_0{0,2}(19|20|21|22|23|24|25)M"))),
      estimate = pop_edu_somecoll_plus_est
    )
  ) %>%
  ungroup() %>% 
  mutate(
    # Here you can follow the example above with unemployment to create you final proportions
  )
  
bmore_census_education %>% 
  select(-matches("B\\d{5}_\\d{3}."))
```

    ## # A tibble: 200 x 8
    ##    GEOID NAME  pop_edu_denom_e… pop_edu_denom_m… pop_edu_nohs_nu…
    ##    <chr> <chr>            <dbl>            <dbl>            <dbl>
    ##  1 2451… Cens…             4697              376              427
    ##  2 2451… Cens…             3498              449              174
    ##  3 2451… Cens…             2391              363              609
    ##  4 2451… Cens…             2383              172              108
    ##  5 2451… Cens…             1121              129              116
    ##  6 2451… Cens…             1268              182              226
    ##  7 2451… Cens…             2757              300              448
    ##  8 2451… Cens…             1669              235              455
    ##  9 2451… Cens…             1170              133              392
    ## 10 2451… Cens…             1216              188              109
    ## # … with 190 more rows, and 3 more variables: pop_edu_nohs_num_moe <dbl>,
    ## #   pop_edu_somecoll_plus_est <dbl>, pop_edu_somecoll_plus_num <dbl>

4.  Calling race/ethnicity table then manipulating that data

<!-- end list -->

``` r
bmore_census_race <- get_acs(
  geography = "tract", 
  state = "MD",
  county = c("510"),
  table = "B02001",
  survey = "acs5",
  output = "wide",
  geometry = FALSE)
```

    ## Getting data from the 2014-2018 5-year ACS

``` r
bmore_census_race
```

    ## # A tibble: 200 x 22
    ##    GEOID NAME  B02001_001E B02001_001M B02001_002E B02001_002M B02001_003E
    ##    <chr> <chr>       <dbl>       <dbl>       <dbl>       <dbl>       <dbl>
    ##  1 2451… Cens…        5706         430        3533         351        1143
    ##  2 2451… Cens…        3877         509        3195         506          89
    ##  3 2451… Cens…        3983         551          27          28        3775
    ##  4 2451… Cens…        2869         208        2593         222         107
    ##  5 2451… Cens…        1376         160         447         131         764
    ##  6 2451… Cens…        2331         353          33          30        2259
    ##  7 2451… Cens…        4324         554          28          32        4278
    ##  8 2451… Cens…        2971         442         196          98        2654
    ##  9 2451… Cens…        2025         204           7          10        1991
    ## 10 2451… Cens…        1525         239         589         117         836
    ## # … with 190 more rows, and 15 more variables: B02001_003M <dbl>,
    ## #   B02001_004E <dbl>, B02001_004M <dbl>, B02001_005E <dbl>, B02001_005M <dbl>,
    ## #   B02001_006E <dbl>, B02001_006M <dbl>, B02001_007E <dbl>, B02001_007M <dbl>,
    ## #   B02001_008E <dbl>, B02001_008M <dbl>, B02001_009E <dbl>, B02001_009M <dbl>,
    ## #   B02001_010E <dbl>, B02001_010M <dbl>
