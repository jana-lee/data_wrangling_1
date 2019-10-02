Tidy\_Data
================
Jana Lee
9/24/2019

## Importing data

``` r
pulse_data =
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

## pivot\_longer

pivot\_longer lengthens data, increasing the number of rows and
decreasing the number of columns. Inverse is pivot\_wider.

BDI score is spread across 4 columns, corresponding to 4 observation
times. We can fix the problem with pivot\_longer.

We want a visit ID number that removes the bdi\_score… etc. We can use
the names\_prefix to get RID of this.

``` r
pulse_tidy_data = 
  pivot_longer(
    pulse_data, 
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
) %>%
  mutate(
    visit = recode(visit, "bl" = "00m"),
    visit = factor(visit, levels = str_c(c("00", "01", "06", "12"), "m"))) %>%
  arrange(id, visit)
```

## separate in litters

``` r
litters_data = 
  read_csv("./data_import_examples/FAS_litters.csv", col_types = "ccddiiii") %>% 
  janitor::clean_names() %>%
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>%
  mutate(
    dose = str_to_lower(dose),
    wt_gain = gd18_weight - gd0_weight) %>%
  arrange(litter_number)
```

## Learning Assessment, pivot\_longer

``` r
litters_data %>% 
  select(litter_number, ends_with("weight")) %>% 
   pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd", 
    values_to = "weight") %>% 
  mutate(gd = recode(gd, "gd0_weight" = 0, "gd18_weight" = 18))
```

    ## # A tibble: 98 x 3
    ##   litter_number      gd weight
    ##   <chr>           <dbl>  <dbl>
    ## 1 #1/2/95/2           0     27
    ## 2 #1/2/95/2          18     42
    ## 3 #1/5/3/83/3-3/2     0     NA
    ## 4 #1/5/3/83/3-3/2    18     NA
    ## 5 #1/6/2/2/95-2       0     NA
    ## # … with 93 more rows

``` r
# This is tidy because we have a variable for day and a variable for weight rather than using values in the variable names.
```

## pivot\_wider

pivot\_wider widens data, increasing the number of columns and
decreasing the number of rows.

Deliberately untidying this

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

pivot_wider(
  analysis_result,
  names_from = "time",
  values_from = "mean",
)
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

## Binding rows

Efficient implementation of the common pattern of do.call or do.call for
binding many data frames into one.

What happens when we have data spread across multiple tables?

``` r
fellowship_data = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "B3:D6") %>%
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")
```

## bind\_rows

``` r
lotr_tidy = 
  bind_rows(fellowship_data, two_towers, return_king) %>%
  janitor::clean_names() %>%
  pivot_longer(
    female:male,
    names_to = "sex", 
    values_to = "words") %>%
  mutate(race = str_to_lower(race)) %>% 
  select(movie, everything()) 
```

## Joining datasets

Adding the pup\_data and litter\_data first. Then, making fas\_data (new
dataset) to join the pups data together.

``` r
pup_data = 
  read_csv("./data_import_examples/FAS_pups.csv", col_types = "ciiiii") %>%
  janitor::clean_names() %>%
  mutate(sex = recode(sex, `1` = "male", `2` = "female")) 

litter_data = 
  read_csv("./data_import_examples/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))

# We are specifying that we start with pup_data first and then add the litter_data)
# if you don't tell the left_join function with by, it might get you into trouble. It is smart and tries to join by itself
fas_data =
  left_join(pup_data, litter_data, by = "litter_number")

# Joining by multiple: ... by = c(...)
```

# Learning Assessment

``` r
surv_os = read_csv("./survey_results/surv_os.csv") %>% 
  janitor::clean_names() %>% 
  rename(id = what_is_your_uni, os = what_operating_system_do_you_use)
```

    ## Parsed with column specification:
    ## cols(
    ##   `What is your UNI?` = col_character(),
    ##   `What operating system do you use?` = col_character()
    ## )
