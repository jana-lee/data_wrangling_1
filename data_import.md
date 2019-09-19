Data Import
================
Jana Lee
9/17/2019

## Load in a dataset for from FAS\_litters

``` r
litters_data = read_csv(file = "./data_import_examples/FAS_litters.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

``` r
litters_data = janitor::clean_names(litters_data)
```

## Learning Assessment 1, load in pups data

``` r
pups_data = read_csv(file = "./data_import_examples/FAS_pups.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

``` r
pups_data = janitor::clean_names(pups_data)
```

\#\#parsing columns - back ticks show that the column name is NOT in a
standard format. It’s a warning to the code\!

``` r
litters_data = read_csv(file = "./data_import_examples/FAS_litters.csv",
col_types = cols(
    Group = col_character(),
    `Litter Number` = col_character(),
    `GD0 weight` = col_double(),
    `GD18 weight` = col_double(),
    `GD of Birth` = col_integer(),
    `Pups born alive` = col_integer(),
    `Pups dead @ birth` = col_integer(),
    `Pups survive` = col_integer()
  )
)
```

## Learning assessment 2, parsing example

## Read in an Excel file…

``` r
library(readxl)
mlb11_data = read_excel(path = "./data_import_examples/mlb11.xlsx")
```

## Read in SAS…

``` r
pulse_data = haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat")
```
