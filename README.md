
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - Instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

``` r
# Deaths per avenger
deaths <- av |> 
  select(Name.Alias, starts_with("Death")) |> 
  pivot_longer(
    Death1:Death5,
    names_to = "Time",
    values_to = "Death"
  ) |>
  mutate(
    Time = parse_number(Time)
  ) |>
  filter(Death != "")

head(deaths)
```

    ## # A tibble: 6 × 3
    ##   Name.Alias                       Time Death
    ##   <chr>                           <dbl> <chr>
    ## 1 "Henry Jonathan \"Hank\" Pym"       1 YES  
    ## 2 "Janet van Dyne"                    1 YES  
    ## 3 "Anthony Edward \"Tony\" Stark"     1 YES  
    ## 4 "Robert Bruce Banner"               1 YES  
    ## 5 "Thor Odinson"                      1 YES  
    ## 6 "Thor Odinson"                      2 YES

Similarly, deal with the returns of characters.

``` r
# Returns per avenger
returns <- av |>
  select(Name.Alias, starts_with("Return")) |> 
  pivot_longer(
    Return1:Return5,
    names_to = "Time",
    values_to = "Return"
  ) |>
  mutate(
    Time = parse_number(Time)
  ) |>
  filter(Return != "")

head(returns)
```

    ## # A tibble: 6 × 3
    ##   Name.Alias                       Time Return
    ##   <chr>                           <dbl> <chr> 
    ## 1 "Henry Jonathan \"Hank\" Pym"       1 NO    
    ## 2 "Janet van Dyne"                    1 YES   
    ## 3 "Anthony Edward \"Tony\" Stark"     1 YES   
    ## 4 "Robert Bruce Banner"               1 YES   
    ## 5 "Thor Odinson"                      1 YES   
    ## 6 "Thor Odinson"                      2 NO

Based on these data sets calculate the average number of deaths an
Avenger suffers.

``` r
# Average number of deaths per Avenger
avg_deaths <- deaths |> 
  filter(Death == "YES") |>  
  group_by(Name.Alias) |>
  summarise(death_count = n()) |>
  summarise(avg_deaths = mean(death_count))

avg_deaths
```

    ## # A tibble: 1 × 1
    ##   avg_deaths
    ##        <dbl>
    ## 1       1.39

The average number of deaths an Avenger suffer’s is 1.39

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

## Betsy’s Work

Quoted statement I plan to fact-check: \> “Out of 173 listed Avengers,
my analysis found that 69 had died at least one time after they joined
the team”

Code to derive the (numeric) fact for the statement:

``` r
deaths |> 
  group_by(Name.Alias) |>
  summarise(death_count = n()) |>
  filter(death_count == 1) |> 
  summarise(one_death = n())
```

    ## # A tibble: 1 × 1
    ##   one_death
    ##       <int>
    ## 1       145

The result: According to the dataset, there are 145 Avengers with
exactly one death according to this data set which differs from
FiveThirtyEight’s analysis of 69 Avengers with one death.

## Maurycy’s Work

Quoted statement I plan to fact-check: \> “I counted 89 total deaths —
some unlucky Avengers are basically Meat Loaf with an E-ZPass — and on
57 occasions the individual made a comeback.”

Code to derive the (numeric) fact for the statement:

``` r
# Total deaths
total_deaths <- deaths |> 
  filter(Death == "YES") |> 
  nrow()

total_deaths
```

    ## [1] 89

``` r
# Total returns
total_returns <- returns |> 
  filter(Return == "YES") |> 
  nrow()

total_returns
```

    ## [1] 57

The result: According to the dataset, there are 89 total recorded deaths
and 57 returns, which matches the numbers reported in the
FiveThirtyEight analysis.
