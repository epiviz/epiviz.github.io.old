---
layout: default
title: Epiviz
logo: <img src="../img/epiviz_logo_large_white.png" wodth="191" height="41" alt="Epiviz" />
---

[**Epiviz**]({{ site.epivizUiMain }}) is an interactive visualization tool for functional genomics data. It supports
genome navigation like other genome browsers, but allows multiple visualizations of data within genomic regions using
scatterplots, heatmaps and other user-supplied visualizations. It also includes data from the
[Gene Expression Barcode project](http://barcode.luhs.org/) for transcriptome visualization. It has a flexible plugin
framework so users can add [d3](http://d3js.org/) visualizations. You can find more information about Epiviz at
[http://epiviz.github.io](http://epiviz.github.io) and see a video tour [here](http://youtu.be/099c4wUxozA).

<iframe width="480" height="360" src="http://www.youtube.com/embed/099c4wUxozA" frameborder="1" allowfullscreen></iframe>

The [**Epivizr** Bioconductor package](http://bioconductor.org/packages/release/bioc/html/epivizr.html) implements two-way
communication between the `R/Bioconductor` computational genomics environment and Epiviz. Objects in an `R` session
can be displayed as tracks or plots on Epiviz. Epivizr uses [WebSockets](http://www.websocket.org/) for communication
between the browser `JavaScript` client and the `R` environment, the same technology underlying the popular
[Shiny](http://www.rstudio.com/shiny/) system for authoring interactive web-based reports in `R`.

## Recent Posts

{% include post_loop.html %}
