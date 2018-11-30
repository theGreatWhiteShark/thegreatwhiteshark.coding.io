+++
date = "2018-11-30T12:41:45+01:00"
draft = false
comment = true
type = "post"
slug = "ecmwf_retrieve"
title = "ecmwf_retrieve"
subtitle = "A package enabling you to download _all_ public data from the ECMWF servers"
tags = ["R", "data science", "data", "climate"]
description = "The European Centre for Medium-Range Weather Forecasts (ECMWF) allows you to access its vast set of model data via a free account and a Python 3 package they provide as well. Unfortunately, a download limit of 30GB prevents the user to retrieve all public data. To circumvent this restriction, I wrote the package [ecmwf_retrieve](https://gitlab.com/theGreatWhiteShark/ecmwf_retrieve), which chops your request in small parts, sends all of them to the API of the ECMWF, and joins the retrieved data into one single file."
+++

In the last post about the [dwd2r](../dwd2r) **R** package I showed
you how to retrieve large amount of observational data from within
Germany. This is one of the highest quality data sets available and
certainly a very nice starting point for your climatological
analysis. But it only covers Germany. If you intend to analysis data
from another location on the globe, hopefully the government of the
corresponding country shares their data with you. This is, however,
not that likely. In such cases, or when the locations of interest are
over the sea, we can use so-called reanalysis data sets, as the ones
provided by the European Centre for Medium-Range Weather Forecasts
(ECMWF).

## Reanalysis data

The general idea is that you want/have to use different sources of
observational data all around the world, like satellites, buoys,
classical measurement stations on land, or temperature measurements of
the sea water by the intake of the engine of various ships. This data
can be projected on a grid spanning the globe and thus allows you to
analysis the weather of the whole earth at once. But in order to make
studies about the overall climate, we need more than a mere snapshot
of the current state of the atmosphere in time and more climatological
information at all grid points than measured.

Both shortcomings will be coped with the usage of _reanalysis
data_. Essentially, we use consecutive snapshots of the atmosphere,
which are mainly composed out of temperatures and pressures at both
ground and sea-level, feed them into a state-of-the-art global weather
model, and make it run smoothly through all its input. We start with
the first snapshot in time and use it as the initial condition of the
weather model. It will calculate the global state of the atmosphere
based on the input and evolves it further in time. This way we started
with just ground-level observations but can now extract all quantities
included in the model, like air pressures at various heights, cloud
coverage etc. But with this few initial conditions the temporal
evolution of the atmosphere will probably take off in an arbitrary
direction, which is very far from reality. To prevent this from
happening, we force the modelled state of the atmosphere to resemble
the next snapshot as close as possible. This way we are using the
model to interpolate between the observations rather than for
simulation itself. We thus now have a more complete picture of the atmosphere
in both time, vertical resolution, and climatological quantities but
still base it on real-world observations.

To be able to study the climate of the earth with a consistent set of
data, the reanalysis is run with a single global weather model on all
_trustworthy_ observations from the past. Since satellites are a
rather new invention, the most popular data set of the
[ECMWF](https://confluence.ecmwf.int/display/WEBAPI), the so-called
**ERA-Interim**, only starts in 1979. For a complete introduction into
the topic of reanalysis data sets and all technical details of their
implementation at the ECMWF please see
[their](https://www.ecmwf.int/en/research/climate-reanalysis)
explanations, papers, and wiki.


## The `ecmwf_retrieve` package

It is rather easy and convenient to get a [free
account](https://apps.ecmwf.int/registration/) at the ECMWF and to use
their **Python 3** package to download publicly available data sets,
like ERA-Interim or CERA-20C, via their [MARS
API](https://confluence.ecmwf.int/display/WEBAPI/Access+MARS). But if
you want to include multiple parameters at all time steps, dates, and
grid points, you very soon reach the **download limit of 30GB**
superimposed onto the free account.

My
[ecmwf_retrieve](https://gitlab.com/theGreatWhiteShark/ecmwf_retrieve)
package helps you to circumvent this download restriction by splitting
your requests into separated ones (one for every year) and combining
all the downloaded netCDF files into a single file afterwards. Note:
**this only works with netCDF** and not with the GRIB data type.

#### Requirements

Before you attempt to install the package, make sure you have the
[netCDF operator](http://nco.sourceforge.net/) tools and the
`setuptools` Python package installed on your system.

``` bash
## On Debian-based systems
sudo apt install nco python3-setuptools
```
Or, if you are working with Anaconda

``` bash
conda install -c conda-forge nco setuptools

```

In addition, we need the `netcdf4` package to handle the format of
choice of the ECMWF and their custom package `ecmwfapi` to talk with
the MARS API.

``` bash
pip3 install netcdf4 ecmwf-api-client
```

Apart from the requirements on the software side you still need to
register a free account at the ECMWF. See
[this](https://software.ecmwf.int/wiki/display/WEBAPI/Access+ECMWF+Public+Datasets)
guide for instructions.

#### Installation

First, clone the package to your local computer

``` bash
git clone https://gitlab.com/theGreatWhiteShark/ecmwf_retrieve
```

jump to the folder containing the project, and install it using the
*setup.py*.

``` bash
cd ecmwf_retrieve
python setup.py install
```

#### Documentation

The documentation of the project is provided as a *HTML* file and can
be accessed (from the root of the repository) via

``` bash
firefox doc/_build/html/index.html
```

If you want to build the documentation on your own, be sure you have
both `sphinx` and `sphinxcontrib-napoleon` installed. Afterwards, jump
to the *doc* folder and compile the it to the format you are
interested in. E.g.

``` bash
## Compile the HTML version of the documentation.
cd doc
make html
```

#### Usage

The main function of the **ecmwf_retrieve** package is called
`retrieve` as in the package provided by the ECMWF it wraps
around. But it also comes with some convenience functions specifying
default parameters for a ERA-Interim requests allowing the user to
just specify the key-value pairs she wants to alter.

``` python
# Load the module as 'ec'
import ecmwf_retrieve.ecmwf_retrieve as ec

# View the default options
ec.erainterim_default_options()

# Download data from the ECMWF
ec.retrieve( options = { 
	'date' : '1979-01-01/to/1981-02-28',
	'param' : '2t', 
	'target' : 'era-interim-analysis.nc' } )
```

More examples can be found in the [examples](https://gitlab.com/theGreatWhiteShark/ecmwf_retrieve/tree/master/examples/) folder.
