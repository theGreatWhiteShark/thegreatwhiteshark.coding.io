+++
date = "2017-11-03T20:02:00+01:00"
draft = false
comment = true
type = "post"
slug = "climex"
title = "Climex"
subtitle = "Extreme value analysis on climatic time series using R and Shiny"
tags = [ "R" ]
description = "In this post I present my R package climex for fitting extreme value distributions. It provides a more robust fit of the generalized extreme value and generalized Pareto distribution than other packages, convenience functions to download and import all daily observation data provided by the German weather service, and a neat Shiny-based web application to analyze the tails of the distributions of various station data."

+++

The **Climex** app is my longest and most comprehensive endeavor in
the realm of programming and software development up to now. It
started as a couple of wrapper functions in the beginning of my PhD in
2015 and is still under active development. 

In this post I won't explain the motivation and basics of the extreme
value analysis (EVA). But I intend to do a separate post dedicated to
this topic.

# Motivation

But there are already a bunch of R packages for the fitting of extreme
value distributions. So, why did I decided to write my own one?

Right from the start I wanted to perform EVA on a massive
parallel scale by fitting the generalized extreme value (GEV) and
generalized Pareto (GP) distribution to all stations within a country
or region or to all grid points of a climate model/reanalysis
data. But I always encountered some errors and numerical
artifacts. Exchanging the fitting functions and packages helped for
specific stations. But then again all packages produced errors. Just
for different stations. So I decided to dive into this topic and to
write a package, which is more robust and fits my needs and permits a
massive parallel application.

# More robust parameter estimation 

The very details of what went wrong and how I fixed it will be
presented in my thesis. I also intend to upload it to, but
it won't happen before September 2018. Therefore, I will only provide
a brief description of how I implemented the fitting procedure in the
package. 

The fitting of GEV and GP parameters using unconstrained optimization
(done by all other R packages dedicated to EVA) tends to
produce numerical artifacts. This is due to the presence of logarithms
in the negative log-likelihood functions and a limited range of shape
parameters 
for which the maximum likelihood estimator is defined. While producing
absurdly large parameters or causing the optimization to fail when
using the **BFGS** algorithm (*extRemes*), the differences using the
**Nelder-Mead** algorithm (all other packages) might be small, barely
noticeable, and totally plausible.

In order to avoid those numerical artifacts, the **augmented
Lagrangian method** is used to incorporate both the logarithms and the
limited range of shape parameters as nonlinear constraints. With this
approach the optimization can be started at arbitrary initial
parameter combinations and will almost always converge to the global
optimum.

# Interface to the FTP server of the German weather service

The German weather service (DWD) hosts a [FTP
server](ftp://ftp-cdc.dwd.de/pub/CDC/observations_germany/climate/daily/kl/recent/)
providing the measurement data of all the German weather stations as
.csv file with daily resolution. Since the number of stations is quite
large, 1081 in total, I wrote a couple of functions to download and
format the data, as well as to import them into a R session. 

For an introduction into how to download and handle the data, please
see the package's vignette on its [general
usage](https://github.com/theGreatWhiteShark/climex/blob/v1.2.0/vignettes/data_dwd_and_usage.Rmd).

# Shiny-based web application

Since it's quite cumbersome to handle dozens, not to mention many
hundreds of time series, I also built a
[Shiny](https://shiny.rstudio.com/)-based web application to perform
the EVA on the individual series. 

![leaflet-tab](/thegreatwhiteshark.coding.io/images/posts/2017/climex/climex_map.jpeg)

It features two different tabs. The first one holds a
[leaflet](https://rstudio.github.io/leaflet/) map the user can
interactively choose the station she want to perform the analysis
on from. Also the most important information concerning the chosen series
is displayed in a box.

![time-series-tab](/thegreatwhiteshark.coding.io/images/posts/2017/climex/climex_time-series.png)

The second tab provides full control over all steps involved in the
fitting of the station data via an intuitive GUI in a persistent way
(changes will be applied to the analysis of all following time
series). You can e.g. pick either the GEV or GP distribution, exclude
single points or whole parts of a time series from the analysis, or
calculate the return level for the minimum values instead of the
maximum ones.

A detailed description of the different features is given in the
corresponding
[vignette](https://github.com/theGreatWhiteShark/climex/blob/v1.2.0/vignettes/climex_app.Rmd). 

# Installation

You are curious and want to try the package or the web application?

Using the following link you can test the [web
application](http://climex.pks.mpg.de/).

To use the package, you have to have R installed on your operation
system. Since the [Shiny](https://shiny.rstudio.com/)-based web app
uses 
rather new features of the R programming language, you have to have
**at least R-3.3.0** or newer (you can check your version by running
`R --version` in the terminal). If you don't fulfill this condition
yet, be sure to get the binary or source of an appropriate R version
via [CRAN](https://CRAN.R-project.org/).

In order to install this package, you have to use the *install_github*
function from the **devtools** package.

Just open a R shell on your computer and type in the following commands

``` r
## Installing the devtools package (in case your haven't done it yet).
install.packages( "devtools" )

## Installing the climex package from Github.
devtools::install_github( "theGreatWhiteShark/climex" )
```

This will install the climex package residing on the **master** branch
of this git repository. If you instead want to download and install a
different branch, use the *ref* argument to specify it. E.g.

``` r
devtools::install_github( "theGreatWhiteShark/climex", ref = "v1.2.0" )
```

Even if you do not intend to use the full capabilities of the package
but just parts like the download of the DWD data, be sure to
nevertheless install the whole package via the commands listed
above. It just uses 1.4 MB of space and you this way you ensure that
all required packages will be installed and all environmental
variables will be set.

# Usage

You are new to **R**? Then check out the [compiled list of
resources](https://www.rstudio.com/online-learning/#R) from RStudio or
the [official
introduction](https://CRAN.R-project.org/doc/manuals/R-intro.pdf).

A thorough introduction is provided for the [general
usage](res/README_data_dwd_and_usage.Rmd) of the package and the
Shiny-based [web application](res/climex_app.Rmd), as mentioned
beforehand.

When using this package in your own analysis, keep in mind that its
functions expect your time series to be of class
[xts](https://CRAN.R-project.org/web/packages/xts/index.html) and not
numeric!
