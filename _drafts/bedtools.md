---
layout: post
title: "Using BED files with epiviz"
categories: epivizr
---

On [a twitter exchange](https://twitter.com/aaronquinlan/status/545559723853246464) I showed [A. Quinlan](https://twitter.com/aaronquinlan) how to use `epivizr` to load data from a custom `bed` file. Here is the code I gave him:

```r
library(epivizr)
library(rtracklayer)
 
# download example bed file
download.file("https://raw.githubusercontent.com/arq5x/bedtools/master/data/aluY.hg19.bed.gz", destfile="test.bed.gz", method="curl")
 
# start UI
mgr <- startEpiviz(workspace="mi9NojjqT1l")
 
# import bed file
gr <- import(BEDFile("test.bed.gz"))
 
# drop data from unplaced contigs
gr <- keepSeqlevels(gr, paste0("chr",c(1:22,"X","Y")))
 
# add track with bed file data
dev <- mgr$addDevice(gr, "example bed")
 
# finish up
mgr$stopServer()
```

Also at [https://gist.github.com/hcorrada/f930fa0092f1100f1d37](https://gist.github.com/hcorrada/f930fa0092f1100f1d37). 

The idea behind the `epivizr` BioC package is that it can use that infrastructure to import **a lot** of data formats into `GenomicRanges`-like objects one can manipulate (say, filter or transform), and have interactive visualization that reflects those manipulations immediately. However, it's a little cumbersome for the use-case of where you have data on a BED file that you don't need to manipulate, but just explore visually. 

An option we'd like to get started with to support this *contextual-data* use-case is to write small programs that would use, say [bedtools](https://github.com/arq5x/bedtools) for example, to implement the epiviz [Data Provider API](http://epiviz.github.io/plugins.html#new-data-provider-plugin) and serve data directly from a bed file. 





	
