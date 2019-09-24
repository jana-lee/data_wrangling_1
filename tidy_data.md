Tidy\_Data
================
Jana Lee
9/24/2019

## Wide to long

``` r
pulse_data =
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

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
  )
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

## pivot\_wider

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
