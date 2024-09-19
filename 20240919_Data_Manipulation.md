Data Manipulation
================
Kaleb J. Frierson
2024-09-19

- [Notes](#notes)
- [Setup](#setup)
- [Data Manipulation](#data-manipulation)
  - [select](#select)
  - [filter](#filter)
  - [mutate](#mutate)
  - [arrange](#arrange)
  - [piping](#piping)

# Notes

Major steps:

Select relevant variables, filter out unnecessary obs, create new
variables or change existing ones, arrange in an easy to digest format.
Make it make sense to you and your research question. Each of these
steps has a function within the dplyr package.

dplyr comes with tidyverse. So just call that library.

The idea that you have one function per step is an intentional choice
within dplyr.

Dataframe centric: start with a dataframe and end with a dataframe. One
goes in and one goes out. That is how it works in tidyverse every time.

This allows you to pipe together collections of operations that gets you
to the dataset that you need. Piece meal resulting in big thing that you
need. Pipes came from magrittr package; loaded by everything in the
tidyverse. There is an updated one that is built into R!

So, what are pipe operators?

Steps to day:

    Wake up, brush teeth, do data science

In “R” I can nest these actions:

happy_kaleb = do_ds(brush_teeth(wake_up(asleep_kaleb)))

This is hard to read! Alternatively you can name a bunch of intermediate
objects that you don’t need and you end up with a bunch of dataframes
and you might not know which to use.

Piping says start with one thing go to another then another:

happy_kaleb = wake_up(asleep_kaleb) \|\> brush_teeth() \|\> do_ds()

Read \|\> as “and then”; you have all of these steps linked together!

Per Jeff: be greatful that you don’t have to appreciate how great piping
is!

# Setup

``` r
litters_df = 
  read_csv("data_import_examples/FAS_litters.csv", na = c("NA","","."))
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df = janitor::clean_names(litters_df) 
```

``` r
pups_df = 
  read_csv("data_import_examples/FAS_pups.csv", na=c("NA","","."))
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df = janitor::clean_names(pups_df)
```

# Data Manipulation

## select

Use select to select variables:

``` r
select(litters_df, group, litter_number, gd0_weight)
```

    ## # A tibble: 49 × 3
    ##    group litter_number   gd0_weight
    ##    <chr> <chr>                <dbl>
    ##  1 Con7  #85                   19.7
    ##  2 Con7  #1/2/95/2             27  
    ##  3 Con7  #5/5/3/83/3-3         26  
    ##  4 Con7  #5/4/2/95/2           28.5
    ##  5 Con7  #4/2/95/3-3           NA  
    ##  6 Con7  #2/2/95/3-2           NA  
    ##  7 Con7  #1/5/3/83/3-3/2       NA  
    ##  8 Con8  #3/83/3-3             NA  
    ##  9 Con8  #2/95/3               NA  
    ## 10 Con8  #3/5/2/2/95           28.5
    ## # ℹ 39 more rows

``` r
select(litters_df, group:gd18_weight)
```

    ## # A tibble: 49 × 4
    ##    group litter_number   gd0_weight gd18_weight
    ##    <chr> <chr>                <dbl>       <dbl>
    ##  1 Con7  #85                   19.7        34.7
    ##  2 Con7  #1/2/95/2             27          42  
    ##  3 Con7  #5/5/3/83/3-3         26          41.4
    ##  4 Con7  #5/4/2/95/2           28.5        44.1
    ##  5 Con7  #4/2/95/3-3           NA          NA  
    ##  6 Con7  #2/2/95/3-2           NA          NA  
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA  
    ##  8 Con8  #3/83/3-3             NA          NA  
    ##  9 Con8  #2/95/3               NA          NA  
    ## 10 Con8  #3/5/2/2/95           28.5        NA  
    ## # ℹ 39 more rows

``` r
select(litters_df, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 1 more variable: pups_dead_birth <dbl>

``` r
select(litters_df, -(group:gd18_weight)) 
```

    ## # A tibble: 49 × 4
    ##    gd_of_birth pups_born_alive pups_dead_birth pups_survive
    ##          <dbl>           <dbl>           <dbl>        <dbl>
    ##  1          20               3               4            3
    ##  2          19               8               0            7
    ##  3          19               6               0            5
    ##  4          19               5               1            4
    ##  5          20               6               0            6
    ##  6          20               6               0            4
    ##  7          20               9               0            9
    ##  8          20               9               1            8
    ##  9          20               8               0            8
    ## 10          20               8               0            8
    ## # ℹ 39 more rows

``` r
select(litters_df, starts_with("gd")) 
```

    ## # A tibble: 49 × 3
    ##    gd0_weight gd18_weight gd_of_birth
    ##         <dbl>       <dbl>       <dbl>
    ##  1       19.7        34.7          20
    ##  2       27          42            19
    ##  3       26          41.4          19
    ##  4       28.5        44.1          19
    ##  5       NA          NA            20
    ##  6       NA          NA            20
    ##  7       NA          NA            20
    ##  8       NA          NA            20
    ##  9       NA          NA            20
    ## 10       28.5        NA            20
    ## # ℹ 39 more rows

``` r
select(litters_df, contains("birth"))
```

    ## # A tibble: 49 × 2
    ##    gd_of_birth pups_dead_birth
    ##          <dbl>           <dbl>
    ##  1          20               4
    ##  2          19               0
    ##  3          19               0
    ##  4          19               1
    ##  5          20               0
    ##  6          20               0
    ##  7          20               0
    ##  8          20               1
    ##  9          20               0
    ## 10          20               0
    ## # ℹ 39 more rows

``` r
select(litters_df, GROUP=group)
```

    ## # A tibble: 49 × 1
    ##    GROUP
    ##    <chr>
    ##  1 Con7 
    ##  2 Con7 
    ##  3 Con7 
    ##  4 Con7 
    ##  5 Con7 
    ##  6 Con7 
    ##  7 Con7 
    ##  8 Con8 
    ##  9 Con8 
    ## 10 Con8 
    ## # ℹ 39 more rows

``` r
rename(litters_df, GROUP=group)
```

    ## # A tibble: 49 × 8
    ##    GROUP litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

using rename above lets you keep the rest of the variables.

``` r
select(litters_df, litter_number, gd0_weight, everything())
```

    ## # A tibble: 49 × 8
    ##    litter_number   gd0_weight group gd18_weight gd_of_birth pups_born_alive
    ##    <chr>                <dbl> <chr>       <dbl>       <dbl>           <dbl>
    ##  1 #85                   19.7 Con7         34.7          20               3
    ##  2 #1/2/95/2             27   Con7         42            19               8
    ##  3 #5/5/3/83/3-3         26   Con7         41.4          19               6
    ##  4 #5/4/2/95/2           28.5 Con7         44.1          19               5
    ##  5 #4/2/95/3-3           NA   Con7         NA            20               6
    ##  6 #2/2/95/3-2           NA   Con7         NA            20               6
    ##  7 #1/5/3/83/3-3/2       NA   Con7         NA            20               9
    ##  8 #3/83/3-3             NA   Con8         NA            20               9
    ##  9 #2/95/3               NA   Con8         NA            20               8
    ## 10 #3/5/2/2/95           28.5 Con8         NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
relocate(litters_df, litter_number, gd0_weight)
```

    ## # A tibble: 49 × 8
    ##    litter_number   gd0_weight group gd18_weight gd_of_birth pups_born_alive
    ##    <chr>                <dbl> <chr>       <dbl>       <dbl>           <dbl>
    ##  1 #85                   19.7 Con7         34.7          20               3
    ##  2 #1/2/95/2             27   Con7         42            19               8
    ##  3 #5/5/3/83/3-3         26   Con7         41.4          19               6
    ##  4 #5/4/2/95/2           28.5 Con7         44.1          19               5
    ##  5 #4/2/95/3-3           NA   Con7         NA            20               6
    ##  6 #2/2/95/3-2           NA   Con7         NA            20               6
    ##  7 #1/5/3/83/3-3/2       NA   Con7         NA            20               9
    ##  8 #3/83/3-3             NA   Con8         NA            20               9
    ##  9 #2/95/3               NA   Con8         NA            20               8
    ## 10 #3/5/2/2/95           28.5 Con8         NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

Using relocate you don’t have to state everything after
rearranging/stating what you want first two columns to be.

Learning assessment:

## filter

Filter works on rows/observations.

``` r
filter(litters_df, gd_of_birth == 20) 
```

    ## # A tibble: 32 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  3 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  4 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  5 Con8  #3/83/3-3             NA          NA            20               9
    ##  6 Con8  #2/95/3               NA          NA            20               8
    ##  7 Con8  #3/5/2/2/95           28.5        NA            20               8
    ##  8 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ##  9 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## 10 Con8  #3/6/2/2/95-3         NA          NA            20               7
    ## # ℹ 22 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

This is how you can ask whether something is true about a variable!

``` r
filter(litters_df, pups_born_alive >8) 
```

    ## # A tibble: 12 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  2 Con8  #3/83/3-3             NA          NA            20               9
    ##  3 Con8  #5/4/3/83/3           28          NA            19               9
    ##  4 Mod7  #103                  21.4        42.1          19               9
    ##  5 Mod7  #4/2/95/2             23.5        NA            19               9
    ##  6 Mod7  #8/110/3-2            NA          NA            20               9
    ##  7 Low7  #107                  22.6        42.4          20               9
    ##  8 Low7  #98                   23.8        43.8          20               9
    ##  9 Low7  #102                  22.6        43.3          20              11
    ## 10 Low7  #101                  23.8        42.7          20               9
    ## 11 Mod8  #5/93                 NA          41.1          20              11
    ## 12 Mod8  #2/95/2               28.5        44.5          20               9
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, pups_born_alive >=8) 
```

    ## # A tibble: 28 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/2/95/2             27          42            19               8
    ##  2 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  3 Con8  #3/83/3-3             NA          NA            20               9
    ##  4 Con8  #2/95/3               NA          NA            20               8
    ##  5 Con8  #3/5/2/2/95           28.5        NA            20               8
    ##  6 Con8  #5/4/3/83/3           28          NA            19               9
    ##  7 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ##  8 Mod7  #59                   17          33.4          19               8
    ##  9 Mod7  #103                  21.4        42.1          19               9
    ## 10 Mod7  #3/83/3-2             NA          NA            19               8
    ## # ℹ 18 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, pups_born_alive !=9) 
```

    ## # A tibble: 39 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con8  #2/95/3               NA          NA            20               8
    ##  8 Con8  #3/5/2/2/95           28.5        NA            20               8
    ##  9 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ## 10 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## # ℹ 29 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

What about character variables?

``` r
filter(litters_df, group=="Low8")
```

    ## # A tibble: 7 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Low8  #53                 21.8        37.2          20               8
    ## 2 Low8  #79                 25.4        43.8          19               8
    ## 3 Low8  #100                20          39.2          20               8
    ## 4 Low8  #4/84               21.8        35.2          20               4
    ## 5 Low8  #108                25.6        47.5          20               8
    ## 6 Low8  #99                 23.5        39            20               6
    ## 7 Low8  #110                25.5        42.7          20               7
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, group %in% c("Low7","Low8"))
```

    ## # A tibble: 15 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Low7  #84/2               24.3        40.8          20               8
    ##  2 Low7  #107                22.6        42.4          20               9
    ##  3 Low7  #85/2               22.2        38.5          20               8
    ##  4 Low7  #98                 23.8        43.8          20               9
    ##  5 Low7  #102                22.6        43.3          20              11
    ##  6 Low7  #101                23.8        42.7          20               9
    ##  7 Low7  #111                25.5        44.6          20               3
    ##  8 Low7  #112                23.9        40.5          19               6
    ##  9 Low8  #53                 21.8        37.2          20               8
    ## 10 Low8  #79                 25.4        43.8          19               8
    ## 11 Low8  #100                20          39.2          20               8
    ## 12 Low8  #4/84               21.8        35.2          20               4
    ## 13 Low8  #108                25.6        47.5          20               8
    ## 14 Low8  #99                 23.5        39            20               6
    ## 15 Low8  #110                25.5        42.7          20               7
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, group %in% c("Low7","Low8"), pups_born_alive ==8)
```

    ## # A tibble: 6 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Low7  #84/2               24.3        40.8          20               8
    ## 2 Low7  #85/2               22.2        38.5          20               8
    ## 3 Low8  #53                 21.8        37.2          20               8
    ## 4 Low8  #79                 25.4        43.8          19               8
    ## 5 Low8  #100                20          39.2          20               8
    ## 6 Low8  #108                25.6        47.5          20               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

Remember, anything with variables is CASE SENSITIVE!!!

Below: you can drop everything or you can specift what to drop.

``` r
drop_na(litters_df)
```

    ## # A tibble: 31 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                 19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2           27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  5 Mod7  #59                 17          33.4          19               8
    ##  6 Mod7  #103                21.4        42.1          19               9
    ##  7 Mod7  #3/82/3-2           28          45.9          20               5
    ##  8 Mod7  #5/3/83/5-2         22.6        37            19               5
    ##  9 Mod7  #106                21.7        37.8          20               5
    ## 10 Mod7  #94/2               24.4        42.9          19               7
    ## # ℹ 21 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
drop_na(litters_df, gd0_weight)
```

    ## # A tibble: 34 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                 19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2           27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  5 Con8  #3/5/2/2/95         28.5        NA            20               8
    ##  6 Con8  #5/4/3/83/3         28          NA            19               9
    ##  7 Mod7  #59                 17          33.4          19               8
    ##  8 Mod7  #103                21.4        42.1          19               9
    ##  9 Mod7  #3/82/3-2           28          45.9          20               5
    ## 10 Mod7  #4/2/95/2           23.5        NA            19               9
    ## # ℹ 24 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

^ lets you filter out rows with missing information

Learning assessment:

``` r
filter(pups_df, sex == 1) 
```

    ## # A tibble: 155 × 6
    ##    litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85               1       4      13        7      11
    ##  2 #85               1       4      13        7      12
    ##  3 #1/2/95/2         1       5      13        7       9
    ##  4 #1/2/95/2         1       5      13        8      10
    ##  5 #5/5/3/83/3-3     1       5      13        8      10
    ##  6 #5/5/3/83/3-3     1       5      14        6       9
    ##  7 #5/4/2/95/2       1      NA      14        5       9
    ##  8 #4/2/95/3-3       1       4      13        6       8
    ##  9 #4/2/95/3-3       1       4      13        7       9
    ## 10 #2/2/95/3-2       1       4      NA        8      10
    ## # ℹ 145 more rows

## mutate

## arrange

## piping
