---
layout: page
title: Chart Tutorial
sections: [
  ["Adding a chart to the workspace","#add-chart"],
  ["Manipulating charts","#manipulating-charts"],
]
---

## Table of Contents

[Adding a chart to the workspace](#add-chart)

[Manipulating charts](#manipulating-charts)

**More about charts:** [Creating a new chart plugin]({{ site.baseurl }}plugins.html#new-chart-plugin)

<a name="add-chart">
## Adding a chart to the workspace
</a>

1. Epiviz has two types of charts: *Plots*, that display data by feature, and *Tracks*, that display data by location.
For each of the two, it has a separate menu. Whether you want to add a *Plot* or a *Track*, open the corresponding menu,
and select the chart you want to add. These menus expand dynamically, as the user adds more chart plugins.

  ![Plots Menu]({{ site.baseurl }}/img/scr_add_chart_menu.png)

2. Select the appropriate data source group from the list. Only one row can be selected. Click *Next*.

  Some charts require measurements to belong to the same *data source group*, like *Scatter Plots*. Measurements with
  the same *data source group* have the same genomic ranges associated to each item in the data, and what differentiates
  them are their values. For example, all measurements that measure the expression of genes for different tissue types
  belong to the same *data source group*, `affymetrix_probeset`. The same applies for the *Gene Expression Barcode* samples, which all belong
  to the *data source group* called `gexp_barcode`.

  ![Select Data Source Group]({{ site.baseurl }}/img/scr_add_chart_dialog_dsgroup.png)

3. <span class="optional">optional</span> Filter the displayed measurements to a subset you’re interested in. In the
search boxes you can use regular expressions. For example applying the filter `(colon (normal|tumor))` to the name of
the measurements in the heatmap measurements dialog will filter to: `colon normal`, `colon tumor`, `sigmoid_colon normal`,
`sigmoid_colon tumor`, `sigmoid_colon_mucosa tumor`.

4. Select the measurements you want to use for the newly created chart. You can use the `Shift` or `Ctrl` (**Mac**:
`Command`) keys to select multiple measurements. Click *Finish*.

  Some charts require a minimum number of measurements selected. For example, the *Scatter Plot* requires two. The order
  in which you select the measurements is also important! For example, the *Scatter Plot* will display the first selected
  measurement on the `X` axis and the second on `Y`.

  ![Select Measurements]({{ site.baseurl }}/img/scr_add_chart_dialog_measurements.png)

5. The new chart is displayed on the screen.

  ![A New Heatmap Plot]({{ site.baseurl }}/img/scr_add_chart_heatmap.png)


<a name="manipulating-charts">
## Manipulating charts
</a>

The charts in Epiviz are all resizable, and all feature a number of options, some specific to the chart, some general.
In this section we go over a few of them.

1. **Toggle Tooltip.** This button allows the user to toggle between showing and hiding tooltips on chart object hover.

  ![Toggle Tooltip]({{ site.baseurl }}/img/scr_tooltip_button.png)

  ![Tooltip]({{ site.baseurl }}/img/scr_tooltip.png)

2. **Colors.** In Epiviz, colors in all charts can be customized.
  * Take, for example, a line track with two measurements: `Methylation Colon Cancer`, and `Methylation Colon Normal`.
Initially, the first measurement is colored blue, and the second, red.<br/>
  <img alt="Line Track" src="{{ site.baseurl }}/img/scr_line_met_cancer_normal_red_blue.png" style="max-width: 100%" />
  * In this case, the default chosen colors are counter-intuitive, so we choose to change `Methylation Colon Cancer` to
   `red`, and `Methylation Colon Normal` to `green`. Open the *Colors* dialog, and select the first color, assigned to
   `Methylation Colon Cancer`; pick a shade of red for it. Then select the second color, assigned to the second
   measurement; pick green. Click *Ok*.<br/>
  ![Color Picker Dialog]({{ site.baseurl }}/img/scr_color_picker.png)
  <img alt="Line Track" src="{{ site.baseurl }}/img/scr_line_met_cancer_normal_green_red.png" style="max-width: 100%" />

3. **Save Chart.** All charts in Epiviz are represented as [SVG](http://en.wikipedia.org/wiki/Scalable_Vector_Graphics),
and can be saved to the local computer in various file formats: *PDF*, *PS*, *PNG*, *SVG*, and *EPS*. Click the *Save*
button; in the dialog, choose the desired file format, and click *Ok*.

  ![Save Chart Button]({{ site.baseurl }}/img/scr_save_chart_button.png)

  ![Save Chart Dialog]({{ site.baseurl }}/img/scr_save_chart_dialog.png)

4. **Custom Settings.** Charts in Epiviz also have settings specific to the visualization type, allowing to customize
how many objects are displayed at a time, their size, axis boundaries, margins. Each chart has a *Custom settings*
button that opens the options for that particular chart type.
