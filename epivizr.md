---
layout: page
title: Epivizr
sections: [
  ["Installation and requirements","#installation-and-requirements"],
  ["Try it out","#try-it-out"],
  ["Development version","#development-version"],
  ["Non-blocking","#non-blocking"],
]
---

## Table of Contents

[Installation and requirements](#installation-and-requirements)

[Try it out](#try-it-out)

[Development version](#development-version)

[Non-blocking](#non-blocking)

---

The `epivizr` Bioconductor package implements two-way communication between the [R/Bioconductor](http://bioconductor.org)
environment and epiviz. Objects in the R environment can be displayed as tracks or plots on Epiviz. Epivizr uses the
[Websocket data provider API]({{ site.baseurl }}/websocket.html) for communication between the browser Javascript client
and the R environment.

**Epiviz2** To use `epivizr` with the `EpiViz 2` APIs described here, you must use its [development branch](#development-version) (version 1.3.3 or higher)

<a name="installation-and-requirements">
## Installation and requirements
</a>

Epivizr is available as part of the [Bioconductor](http://bioconductor.org) project as of version 2.13. To install the
release version of `epivizr`:

```{r}
source("http://bioconductor.org/biocLite.R")
biocLite("epivizr")
```

<a name="try-it-out">
## Try it out
</a>

The easiest way to try `epivizr` out is to [follow the package vignette](http://bioconductor.org/packages/release/bioc/vignettes/epivizr/inst/doc/IntroToEpivizr.html):

```{r}
require(epivizr)
browseVignettes("epivizr")
```

<a name="development-version">
## Development version
</a>

The [`epivizr` github repository](http://github.com/epiviz/epivizr) contains the latest and greatest version of `epivizr` and is tracked by the devel version in Bioconductor (see
[http://bioconductor.org/developers/how-to/useDevel/](http://bioconductor.org/developers/how-to/useDevel/) for more info. In summary, if you install R-devel, you'll be set.

<a name="non-blocking">
## Non-blocking
</a>

Epivizr (as of version 1.2) supports a non-blocking workflow on both UNIX-like and Windows systems where data is served to the webapp without blocking
the R/bioc interactive session. Make sure you are using the latest version of the [httpuv package](http://cran.r-project.org/web/packages/httpuv/index.html) (version 1.3 or greater to use this. (Thanks to the
[Rstudio](http://rstudio.org) folks for folding our daemonizing code into the main httpuv release).
