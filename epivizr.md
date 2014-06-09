---
layout: page
title: Epivizr
---

The `epivizr` Bioconductor package implements two-way communication between the [R/Bioconductor](http://bioconductor.org) environment and epiviz. Objects in the R environment can be displayed as tracks or plots on Epiviz. Epivizr uses the [Websocket data provider API]({{ site.baseurl }}/websocket.html) for communication between the browser Javascript client and the R environment.

**Epiviz2** To use `epivizr` with the `Epiviz2` APIs described here, you must use its [development branch](#development-version) (version 1.3.3 or higher)
 
## Installation and requirements
Epivizr is available as part of the [Bioconductor](http://bioconductor.org) project as of version 2.13. To install the release version of `epivizr`:

```{r}
source("http://bioconductor.org/biocLite.R")
biocLite("epivizr")
```

## Try it out

The easiest way to try `epivizr` out is to follow the package vignette:

```{r}
require(epivizr)
browseVignettes("epivizr")
```

## Development version

The [`epivizr` github repository](http://github.com/epiviz/epivizr) contains the latest and greatest version of `epivizr` and is tracked by the devel version in Bioconductor (see
[http://bioconductor.org/developers/how-to/useDevel/](http://bioconductor.org/developers/how-to/useDevel/) for more info. In summary, if you install R-devel, you'll be set.

## Non-blocking

Epivizr (as of version 1.2) supports a non-blocking workflow on both UNIX-like and Windows systems where data is served to the webapp without blocking
the R/bioc interactive session. Make sure you are using the latest version of the [httpuv package](http://cran.r-project.org/web/packages/httpuv/index.html) (version 1.3 or greater to use this. (Thanks to the
[Rstudio](http://rstudio.org) folks for folding our daemonizing code into the main httpuv release).
