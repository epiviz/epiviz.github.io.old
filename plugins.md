---
layout: page
title: Charts and Data Providers Plugins
---

## Table of Contents

[Plugging in external scripts and settings](#external-scripts-and-settings)

[Creating a new Chart Plugin](#new-chart-plugin)

[Creating a new Data Provider Plugin](#new-data-provider-plugin)

<a name="external-scripts-and-settings">
## Plugging in external scripts and settings
</a>

**EpiViz** allows users to plug in new visualizations and data providers on the fly, by using the **Charts API** and Data
**Providers API**, without the need of downloading or installing anything on the local machine. The new visualizations
and data providers can be used immediately, alongside existing ones. Also, users can override the default settings of the
UI to adapt it to their own needs. Here's how to do it:

1.

<!--


 To plug in your scripts and settings If using the EpiViz hosted on the UMD server, then you need to specify the two newly created charts as external scripts. To do that, create two Gist sources for each of them
Example
my-track.js: https://gist.github.com/florin-chelaru/11017610#file-my-track-js
my-track-type.js: https://gist.github.com/florin-chelaru/11017650#file-my-track-type-js
Also create a Gist copy of the epiviz default settings, located at http://epiviz-dev.cbcb.umd.edu/v2/ui/src/epiviz/default-settings.js, and add your chart type to the list of chart types as described above: https://gist.github.com/florin-chelaru/11017763#file-my-epiviz-settings-js
-	Get the raw address for each of these three files:
-	Click on “View Raw” in the Gist page
-	Replace “gist.githubusercontent” in the address bar with “rawgit”:
-	http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js
-	http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js
http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js
5.	Now you can use the visualization in epiviz:
http://epiviz-dev.cbcb.umd.edu/v2/ui/?settings=http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js&script[]=http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js&script[]=http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js&ws=&seqName=chr11&start=101233272&end=104816452&



-->