
<!-- README.md is generated from README.Rmd. Please edit that file -->

# oncokbR

<!-- badges: start -->
<!-- badges: end -->

{oncokbR} allows you to annotate genomic data sets using MSK’s [OncoKB
Precision Oncology Knowledge Base](https://www.oncokb.org/) directly in
R. The package wraps existing [OncoKB API
endpoints](https://api.oncokb.org/oncokb-website/api) so R users can
easily leverage the existing API to annotate data on mutations, copy
number alterations and fusions.

This package is compatible with oncoKB data v3.16, but is subject to
change as new versions are released. For more information on oncoKB, see
[Chakravarty et
al. 2017](https://ascopubs.org/doi/full/10.1200/PO.17.00011).

## Installation

You can install the development version of oncokbR with:

``` r
remotes::install_github('karissawhiting/oncokbR')
```

## Authentication

In order to use this package, you will need to acquire an oncoKB API
token and save it to your .Renviron file (or wherever you store
credentials). You will need to register for an account on [OncoKB
Precision Oncology Knowledge Base](https://www.oncokb.org/). Once your
registration is approved, you will see a token under your [Account
Settings](https://www.oncokb.org/account/settings). Save it in your
.Renviron file as `ONCOKB_TOKEN`. This will save the token as an
environmental variable called `ONCOKB_TOKEN` that can be recognized in
all package functions.

*Tip: The following {usethis} function can easily find and open the
.Renviron for you:*

``` r
usethis::edit_r_environ()
#> • Edit '/Users/kwhiting/.Renviron'
#> • Restart R for changes to take effect
```

Paste the token you were given (using the format below) in the .Renviron
file and save the file changes. After saving you should restart your R
session to ensure the token is saved and recognized.

``` r
ONCOKB_TOKEN = 'YOUR_TOKEN'
```

## Annotate Data

### Annotate Mutations:

``` r
library(oncokbR)
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

Annotate MAF data with tumor type indicated for annotations on
oncogenicity and oncoKB treatment levels:

``` r
blca_mutation <- oncokbR::blca_mutation %>%
  mutate(tumor_type = "BLCA")

annotated_tt <- annotate_mutations(mutations = blca_mutation[1:50,])

annotated_tt %>%
  select(oncogenic) %>% 
  table()
#> oncogenic
#>   Likely Neutral Likely Oncogenic        Oncogenic          Unknown 
#>                2                2                1               45
```

``` r
annotated_tt %>%
  select(treatments_level ) %>% 
  table()
#> treatments_level
#> LEVEL_3B 
#>        1
```

You can also annotated with no tumor type data for oncogenicity only:

``` r
blca_mutation <- oncokbR::blca_mutation

annotated_no_tt <- annotate_mutations(mutations = blca_mutation[1:50,])
#> ℹ No "tumor_type" found in data. No treatment-level annotations will be returned.

annotated_no_tt %>%
  select(oncogenic) %>% table()
#> oncogenic
#>   Likely Neutral Likely Oncogenic        Oncogenic          Unknown 
#>                2                2                1               45
```

### Annotate CNA:

``` r
blca_cna <- blca_cna %>%
  mutate(tumor_type = "BLCA")


annotated <- annotate_cna(blca_cna[1:10,])
table(annotated$oncogenic)
#> 
#> Likely Oncogenic        Oncogenic          Unknown 
#>                3                6                1
```

### Annotate Structural Variants:

``` r

blca_sv <- blca_sv %>%
  mutate(tumor_type = "BLCA")


annotated <- annotate_sv(blca_sv[1:10,])
table(annotated$oncogenic)
#> 
#> Likely Oncogenic        Oncogenic          Unknown 
#>                6                3                1
```
