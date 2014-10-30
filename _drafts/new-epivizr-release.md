---
layout: post
title: "New epivizr release"
categories: epivizr
---

Version 1.4 of [`epivizr`](http://www.bioconductor.org/packages/release/bioc/html/epivizr.html) was released as part of the new [Bioconductor 3.0 release](http://master.bioconductor.org/news/bioc_3_0_release/). There are a good number of new features in `epivizr` in this release, including the ability of running the {{ site.epiviz }} UI *within R*, making R/Bioconductor capable of running it's own genome browser!

## Running `epiviz` as a standalone

Previous versions of `epivizr` used the {{ site.epiviz }} web application hosted at the University of Maryland as the front-end for UI. In this new version, we bundle the `JavaScript` source for the {{ site.epiviz }} UI in the `epivizr` package which allows `R` to serve as it's own web host. Like [Shiny](http://shiny.rstudio.com/), we use the `httpuv` package to serve the interactive application. In fact, lots of thanks to [Joe Cheng](http://github.com/jcheng5) and [RStudio](http://rstudio.com) for helping to make this happen!

A genome browser without a genome is not very useful, so you need to tell it about the genome annotation you want to use. One place in `Bioconductor` where you can get genome annotation information is in their `OrganismDb` packages. The `startStandalone` function can take an object of this class and start the UI with that genome and its gene annotation loaded. Here's how you can browse the mouse genome with Bioconductor:

```r
library(epivizr)
library(Mus.musculus)

# this call makes the gene annotation from Mus.musculus, 
# takes a couple of seconds (see more info below)
mgr <- startStandalone(Mus.musculus, "mm10", keepSeqlevels=paste0("chr",c(1:19,"X","Y"))
mgr$stopServer()
```

## Dynamic genome annotation on any UI

This release also allows to add genome and gene information on any {{ site.epiviz }} UI, both standalone and hosted (e.g., at the University of Maryland). For instance, to add the mouse genome to the {{ site.epiviz }} UI hosted at UMD you can use:
	
```r
mgr <- startEpiviz(workspace="OJS2BPGrh7v")


# remove the pre-loaded hg19 annotation 
mgr$rmSeqinfo(paste0("chr",c(1:22,"X","Y")))

# make a genome annotation object (takes a few seconds)
anno <- epivizr::makeGeneTrackAnnotation(Mus.musculus, keepSeqlevels=paste0("chr", c(1:19,"X","Y")))

# add chromosome names and lengths
mgr$addSeqinfo(seqinfo(anno))

# check on UI, only 19 autosomes now
# add the gene annotation track
annoDevice <- mgr$addDevice(anno, "mm10", type="geneInfo")

# now you have a mouse genome browser!
mgr$stopServer()
```

## Heatmaps

You can now add heatmaps to your {{ site.epiviz }} sessions. Here is an example using exon-level RNA-seq count data from the TCGA project (this data is included in the `epivizr` package).

```r
library(epivizr)
data(tcga_colon_expression)

# make the names easier to understand
sampleNames <- paste0(seq(len=nrow(colData(colonSE))),":",colData(colonSE)$sample_type)
colnames(colonSE) <- sampleNames

# normalize counts using DE-Seq's size factors
ref_sample <- 2 ^ rowMeans(log2(assay(colonSE) + 1))
scaled <- (assay(colonSE) + 1) / ref_sample
scaleFactor <- Biobase::rowMedians(t(scaled))
assay_normalized <- sweep(assay(colonSE), 2, scaleFactor, "/")
assay(colonSE) <- log2(assay_normalized + 1)# start the UI managermgr <- startEpiviz(workspace="qyOTB6vVnff")

# add the count data
msObj <- mgr$addMeasurements(colonSE, "tcga exon expression")

# make a heatmap of the first 15 samples
devObj <- mgr$heatmapChart(msObj$getMeasurements()[1:15])

mgr$stopServer()
```

And since {{ site.epiviz }} [now supports clustering on heatmaps](http://epiviz.github.io/features/2014/10/28/new-feature-clustering.html), you have a dynamic heatmap visualization of your expression data as you browse the genome.

## What's coming next...

For the next release of `epivizr` we are planning some new fun things:

1. Support for visualization directly from `BigWig` and `BAM` files to support NGS workflows.
2. Using epiviz browser sessions, or standalone visualizations in publications (markdown documents, html5 slidedecks, or your own web application). 

We've started working on #1 above, you can see how it's going by using the [`epivizr` development version](https://github.com/epiviz/epivizr).



	
