![A poster displaying the talk title, "Efficient machine learning," as well as my name and website [simonpcouch.com](https://simonpcouch.com). Beside the text is a set of six hexagonal logos, showing hex stickers for selected tidymodels packages.](figures/hero.png)

This repository contains source code and slides for the talk "From hours to minutes: accelerating your tidymodels code" at the Salt Lake City R User Group in early 2025. The **slides** for the talk are available [here](https://simonpcouch.github.io/slc-rug-25).

This talk is based on the introductory chapter to my forthcoming book "Efficient machine learning with R." **You can check out the current draft at [emlwr.org](https://emlwr.org).**

To learn more about machine learning with R:

-   Machine learning with tidymodels: [tmwr.org](https://tmwr.org)
-   More example notebooks with tidymodels are at [tidymodels.org](https://tidymodels.org).

## tl;dr

After some setup (see the book chapter for the full setup code):

``` r
library(tidymodels)
library(finetune)
library(bonsai)

bt <- 
  boost_tree(learn_rate = tune(), trees = tune()) %>%
  set_mode("classification")
```

Here's the "basic" tidymodels tuning pipeline:

``` r
bt_res <-
  tune_grid(
    object = bt,
    preprocessor = class ~ .,
    resamples = d_folds,
    grid = 12
  )
```

Here's the optimized tuning code that, in this example, was 145x faster and fractions of a percent less performant:

``` r
plan(multisession, workers = 4)

set.seed(1)
bt_grid <- bt %>%
  extract_parameter_set_dials() %>% 
  grid_regular(levels = 4)

bt_res_speedy <-
  tune_race_anova(
    object = bt %>% set_engine("lightgbm"),
    preprocessor = class ~ .,
    resamples = d_folds,
    grid = bt_grid
  )
```

------------------------------------------------------------------------

In this repository,

-   `index.qmd` contains the source code for the slides. The slides use images in the `/figures` directory.
-   `/docs` is auto-generated from `index.qmd`. Content in that folder is likely unhelpful for a human reader, and is better viewed at the links above. :)
