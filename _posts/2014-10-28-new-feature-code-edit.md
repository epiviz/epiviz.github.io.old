---
layout: post
title:  "New Code Customization Feature for Visualizations"
date:   2014-10-28 13:00:00
categories: features
---

**[See it in {{ site.epiviz }}]({{ site.epivizUiMain }}?ws=MaQiywcMkec&settings=default)**

**[See it in {{ site.epiviz2 }}]({{ site.epiviz2UiMain }}?ws=MaQiywcMkec&settings=default)**

Now visualizations' code can be customized in the UI, based on user needs!

To do it, simply click on the *Edit code* button on any of the available charts: <img src="{{ site.baseurl }}/img/edit_code_button.png" alt="Edit code button" />

Select the method(s) you want to edit. Only the methods corresponding to this particular instance
of the chart will be modified. For example, in the MA Plot at 
[{{ site.epivizUiMain }}?ws=S5qde7SD1Je]({{ site.epivizUiMain }}?ws=S5qde7SD1Je&settings=default), 
we edited the ```_drawAxes``` method by adding the following code at the beginning of the method:

```javascript
  this._svg.selectAll('.midline').remove();
  this._svg.append('line')
      .attr('class', 'midline')
      .attr('x1', this.margins().left())
      .attr('x2', this.width() - this.margins().right())
      .attr('y1', yScale(0) + this.margins().top())
      .attr('y2', yScale(0) + this.margins().top())
      .style('stroke', '#444444')
      .style('shape-rendering', 'crispEdges');
```

<img src="{{ site.baseurl }}/img/edit_code_dialog.png" style="max-width: 100%" alt="Edit code dialog" />

This should add a line at ```y=0```, which is a common view for the MA Plot.

Once happy with the changes, click *Save* and see the changes take effect immediately:

<img src="{{ site.baseurl }}/img/edit_code_ma_plot.png" style="max-width: 100%" alt="Heatmap Clustering" />

**The changes can be saved in the current workspace!**

**[See it in {{ site.epiviz }}]({{ site.epivizUiMain }}?ws=MaQiywcMkec&settings=default)**

**[See it in {{ site.epiviz2 }}]({{ site.epiviz2UiMain }}?ws=MaQiywcMkec&settings=default)**