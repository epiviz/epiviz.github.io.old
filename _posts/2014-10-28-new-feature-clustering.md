---
layout: post
title:  "New Clustering Feature for Heatmap Plots"
date:   2014-10-28 12:00:00
categories: features
---

**[See it in {{ site.epiviz }}]({{ site.epivizUiMain }}?ws=UqaReWj5Sd&settings=default)**

We implemented a new **Clustering** feature in the *Heatmap Plot*! You can use it to group together
measurements using the values in the selected region. 

For example, we selected the following samples in the *gene expression barcode*: ```kidney normal``` and ```tumor```,
```lung normal``` and ```tumor```, ```esophagus normal``` and ```tumor```, ```breast normal``` and ```tumor```, 
```stomach normal``` and ```tumor```.

In the *custom settings* menu of the newly generated chart, we enabled clustering:

<img src="{{ site.baseurl }}/img/heatmap_clustering_dialog.png" style="max-width: 100%" alt="Heatmap Custom Settings" />

This causes the rows to be re-ordered according to the clustering, and a dendrogram to be displayed:

<img src="{{ site.baseurl }}/img/heatmap_clustering.png" style="max-width: 100%" alt="Heatmap Clustering" />

**[See it in {{ site.epiviz }}]({{ site.epivizUiMain }}?ws=UqaReWj5Sd&settings=default)**
