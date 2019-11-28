
<!-- README.md is generated from README.Rmd. Please edit that file -->

# dymium <img src="man/figures/logo.png" align="right" alt="" width="120" />

<!-- badges: start -->

[![Lifecycle:
maturing](https://img.shields.io/badge/lifecycle-maturing-blue.svg)](https://www.tidyverse.org/lifecycle/#maturing)
[![Travis build
status](https://travis-ci.org/dymium-org/dymium.svg?branch=master)](https://travis-ci.org/dymium-org/dymium)
<!-- badges: end -->

The goal of dymium is to provide an easy-to-install and update solution
for the dymium packages. If you are interested in learning about the
framework and start developing your own microsimulation model using R
please see [dymiumCore](https://github.com/dymium-org/dymiumCore). Some
ready to use urban microsimulation modules are available at
[dymiumModules](https://github.com/dymium-org/dymiumModules).

## Installation

The dymium package has not been released on CRAN, but you can install
[GitHub](https://github.com/) with:

``` r
# install.packages("remotes")
remotes::install_github("dymium-org/dymium")
```

## Getting started

## A Minimal Example

This is a minimal example of a microsimulation model built with Dymium

``` r
library(dymium)
#> + dymiumCore 0.0.10      Date: 2019-11-27
#>                                   R: 3.6.1
#>                                  OS: macOS Mojave 10.14.6
#>                                 GUI: X11
#>                                
#> 
library(data.table)
library(R6)

pop_data <- generate_population()
head(pop_data$ind_data)
#>    pid hid    sex age marital_status
#> 1:   1   1   male  32        married
#> 2:   2   1 female  66  never married
#> 3:   3   1   male  72      separated
#> 4:   4   1   male  15 not applicable
#> 5:   5   1 female  45       divorced
#> 6:   6   1 female  49        married
head(pop_data$hh_data)
#>    hid hhsize
#> 1:   1      6
#> 2:   2      3
#> 3:   3      2
#> 4:   4      1
#> 5:   5      6
#> 6:   6      2

Pop <-
  Population$new(
    ind_data = pop_data$ind_data,
    hh_data = pop_data$hh_data,
    pid_col = "pid",
    hid_col = "hid"
  )
#> [17:44:49] INFO  dymiumCore self$get("Individual")$initialise_data: sets hid_col to: 'hid'

Ind <- Pop$get("Individual")

# create a choice model
choices <- data.table(
  sex = c('male', 'female'),
  probs = list(c(0.1,0.9), c(0.9,0.1)),
  choices = list(c('can drive', 'cannot drive'), c('can drive', 'cannot drive'))
)
head(choices)
#>       sex   probs                choices
#> 1:   male 0.1,0.9 can drive,cannot drive
#> 2: female 0.9,0.1 can drive,cannot drive

# create a Transition
TransitionCandrive <- R6::R6Class(
  classname = "TransitionCandrive",
  inherit = TransitionClassification
)

candrive <- TransitionCandrive$new(Ind, choices)

barplot(
  table(candrive$get_result()[['response']]),
  main = "Transition result: driver status",
  col = c('steelblue', 'salmon')
)
```

<img src="man/figures/README-example-1.png" width="100%" />

# Credits

This repo is basically a modified version of the
[Tidyverse](https://github.com/tidyverse/tidyverse) package to load the
dymium packages.
