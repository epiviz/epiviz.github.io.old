---
layout: page
title: Chart Tutorial
---

## Table of Contents

[Adding a chart to the workspace](#add-chart)

[Manipulating charts](#manipulating-charts)

[Creating a new chart plugin](#new-chart-plugin)

<a name="add-chart">
## Adding a chart to the workspace
</a>

1. EpiViz has two types of charts: *Plots*, that display data by feature, and *Tracks*, that display data by location.
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

The charts in EpiViz are all resizable, and all feature a number of options, some specific to the chart, some general.
In this section we go over a few of them.

1. **Toggle Tooltip.** This button allows the user to toggle between showing and hiding tooltips on chart object hover.

  ![Toggle Tooltip]({{ site.baseurl }}/img/scr_tooltip_button.png)

  ![Tooltip]({{ site.baseurl }}/img/scr_tooltip.png)

2. **Colors.** In EpiViz, colors in all charts can be customized.
  * Take, for example, a line track with two measurements: `Methylation Colon Cancer`, and `Methylation Colon Normal`.
Initially, the first measurement is colored blue, and the second, red.<br/>
  <img alt="Line Track" src="{{ site.baseurl }}/img/scr_line_met_cancer_normal_red_blue.png" style="max-width: 100%" />
  * In this case, the default chosen colors are counter-intuitive, so we choose to change `Methylation Colon Cancer` to
   `red`, and `Methylation Colon Normal` to `green`. Open the *Colors* dialog, and select the first color, assigned to
   `Methylation Colon Cancer`; pick a shade of red for it. Then select the second color, assigned to the second
   measurement; pick green. Click *Ok*.<br/>
  ![Color Picker Dialog]({{ site.baseurl }}/img/scr_color_picker.png)
  <img alt="Line Track" src="{{ site.baseurl }}/img/scr_line_met_cancer_normal_green_red.png" style="max-width: 100%" />

3. **Save Chart.** All charts in EpiViz are represented as [SVG](http://en.wikipedia.org/wiki/Scalable_Vector_Graphics),
and can be saved to the local computer in various file formats: *PDF*, *PS*, *PNG*, *SVG*, and *EPS*. Click the *Save*
button; in the dialog, choose the desired file format, and click *Ok*.

  ![Save Chart Button]({{ site.baseurl }}/img/scr_save_chart_button.png)

  ![Save Chart Dialog]({{ site.baseurl }}/img/scr_save_chart_dialog.png)

4. **Custom Settings.** Charts in EpiViz also have settings specific to the visualization type, allowing to customize
how many objects are displayed at a time, their size, axis boundaries, margins. Each chart has a *Custom settings*
button that opens the options for that particular chart type.

<a name="new-chart-plugin">
## Creating a new chart plugin
</a>

**[See it in EpiViz]({{ site.epivizUiMain }}?ws=Y8kWxCO2Ajn&settings=http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js&script[]=http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js&script[]=http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js)**

1. Create a class for the actual visualization

  **Example**

  ```javascript

  goog.provide('epiviz.plugins.charts.MyTrack');

  /**
   * @constructor
   */
  epiviz.plugins.charts.MyTrack = function() {
  };
  ```

2. Inherit either `epiviz.ui.charts.Track` or `epiviz.ui.charts.Plot` (depending on display type)

  **Example**

  ```javascript
  goog.provide('epiviz.plugins.charts.MyTrack');

  /**
   * @param id
   * @param {jQuery} container
   * @param {epiviz.ui.charts.ChartProperties} properties
   * @extends {epiviz.ui.charts.Track}
   * @constructor
   */
  epiviz.plugins.charts.MyTrack = function(id, container, properties) {
    // Call superclass constructor
    epiviz.ui.charts.Track.call(this, id, container, properties);

    this._initialize();
  };

  /*
   * Copy methods from upper class
   */
  epiviz.plugins.charts.MyTrack.prototype = epiviz.utils.mapCopy(epiviz.ui.charts.Track.prototype);
  epiviz.plugins.charts.MyTrack.constructor = epiviz.plugins.charts.MyTrack;


  /**
   * @param {epiviz.datatypes.GenomicRange} [range]
   * @param {epiviz.measurements.MeasurementHashtable.<epiviz.datatypes.GenomicDataMeasurementWrapper>} [data]
   * @param {number} [slide]
   * @param {number} [zoom]
   * @returns {Array.<epiviz.ui.charts.UiObject>} The objects drawn
   */
  epiviz.plugins.charts.MyTrack.prototype.draw = function(range, data, slide, zoom) {
    return epiviz.ui.charts.Chart.prototype.draw.call(this, range, data, slide, zoom);
  };

  /**
   * @returns {string}
   */
  epiviz.plugins.charts.MyTrack.prototype.chartTypeName = function() {
    return 'epiviz.plugins.charts.MyTrack';
  };
  ```

3. Implement the `draw()` method

  **Example**

  ```javascript
  /**
   * @param {epiviz.datatypes.GenomicRange} [range]
   * @param {epiviz.measurements.MeasurementHashtable.<epiviz.datatypes.GenomicDataMeasurementWrapper>} [data]
   * @param {number} [slide]
   * @param {number} [zoom]
   * @returns {Array.<epiviz.ui.charts.UiObject>} The objects drawn
   */
  epiviz.plugins.charts.MyTrack.prototype.draw = function(range, data, slide, zoom) {
      epiviz.ui.charts.Track.prototype.draw.call(this, range, data, slide, zoom);

      // If data is defined, then the base class sets this._lastData to data.
      // If it isn't, then we'll use the data from the last draw call.
      // Same with this._lastRange and range.
      data = this._lastData;
      range = this._lastRange;

      // If data is not defined, there is nothing to draw
      if (!data || !range) { return []; }

      // Using D3, compute a function that maps base-pair locations to chart pixel coordinates
      var xScale = d3.scale.linear()
        .domain([range.start(), range.end()])
        .range([0, this.width() - this.margins().left() - this.margins().right()]);

      // Compute the minimum maximum values of values to be plotted on the Y axis:
      // Iterate throught all measurements that the chart is supposed to draw and find
      // default minimum and maximum values.
      var minY, maxY;
      this.measurements().foreach(function(m) {
        if (minY == undefined || m.minValue() < minY) {
          minY = m.minValue();
        }
        if (maxY == undefined || m.maxValue() > maxY) {
          maxY = m.maxValue();
        }
      });

      // Now create a function that maps measurement values to chart pixel coordinates
      var yScale = d3.scale.linear()
        .domain([minY, maxY])
        .range([this.height() - this.margins().top() - this.margins().bottom(), 0]);

      // Draw x and y axes
      var xTicks = 10, yTicks = 5;
      this._clearAxes();
      this._drawAxes(xScale, yScale, xTicks, yTicks);

      // Create an array of items to be drawn
      /** @type {Array.<epiviz.ui.charts.UiObject>} */
      var items = [];
      this.measurements().foreach(function(m, i) {
        /** @type {epiviz.datatypes.GenomicDataMeasurementWrapper} */
        var dataSeries = data.get(m);

        for (var j = 0; j < dataSeries.size(); ++j) {
          /** @type {epiviz.datatypes.GenomicDataMeasurementWrapper.ValueItem} */
          var valueItem = dataSeries.get(j);

          // Check that the current item overlaps the requested draw range; if not, skip it.
          if (valueItem.rowItem.start() > range.end() || valueItem.rowItem.end() < range.start()) { continue; }

          items.push(new epiviz.ui.charts.UiObject(
            sprintf('item-%s-%s', i, valueItem.globalIndex), // an id that uniquely identifies this object
            valueItem.rowItem.start(), // start location of current item
            valueItem.rowItem.end(), // end location of current item
            [valueItem.value], // the Y value for the current object
            i, // series index
            [[valueItem]],
            [m], // measurement
            'item' // css class
          ));
        }
      });

      var itemsGroup = this._svg.select('.items');
      if (itemsGroup.empty()) {
        itemsGroup = this._svg.append('g').attr('class', 'items');
      }
      itemsGroup.attr('transform', 'translate(' + this.margins().left() + ', ' + this.margins().top() + ')');

      var selection = itemsGroup.selectAll('.item')
        .data(items, function(uiObj) { return uiObj.id; });

      var self = this;
      selection
        .enter()
        .append('rect')
        .attr('class', function(uiObj) { return uiObj.cssClasses; })
        .style('fill', function(uiObj) { return self.colors().get(uiObj.seriesIndex); })
        .attr('x', function(uiObj) { return xScale(uiObj.start); })
        .attr('width', function(uiObj) { return xScale(uiObj.end + 1) - xScale(uiObj.start); })
        .attr('y', function(uiObj) { return yScale(minY) - yScale(uiObj.values[0]); })
        .attr('height', function(uiObj) { return yScale(uiObj.values[0]); })
        .style('opacity', '0')
        .on('mouseout', function () { self._unhover.notify(); })
        .on('mouseover', function (uiObj) { self._hover.notify(uiObj); })
        .on('click', function(uiObj) {
          self._deselect.notify();
          self._select.notify(uiObj);
          d3.event.stopPropagation();
        });

      selection
        .transition()
        .duration(500)
        .attr('x', function(uiObj) { return xScale(uiObj.start); })
        .attr('width', function(uiObj) { return xScale(uiObj.end + 1) - xScale(uiObj.start); })
        .attr('y', function(uiObj) { return yScale(minY) - yScale(uiObj.values[0]); })
        .attr('height', function(uiObj) { return yScale(uiObj.values[0]); })
        .style('opacity', '0.2');

      selection
        .exit()
        .transition()
        .duration(500)
        .style('opacity', '0')
        .remove();

      return items;
  };
  ```

4. Create a class used as factory, derived from `epiviz.ui.charts.TrackType` or `epiviz.ui.charts.PlotType`

  **Example**

  ```javascript
  goog.provide('epiviz.plugins.charts.MyTrackType');

  /**
   * @param {epiviz.Config} config
   * @extends {epiviz.ui.charts.TrackType}
   * @constructor
   */
  epiviz.plugins.charts.MyTrackType = function(config) {
      // Call superclass constructor
      epiviz.ui.charts.TrackType.call(this, config);
  };

  /*
   * Copy methods from upper class
   */
  epiviz.plugins.charts.MyTrackType.prototype = epiviz.utils.mapCopy(epiviz.ui.charts.TrackType.prototype);
  epiviz.plugins.charts.MyTrackType.constructor = epiviz.plugins.charts.MyTrackType;

  /**
   * @param {string} id
   * @param {jQuery} container The div where the chart will be drawn
   * @param {epiviz.ui.charts.ChartProperties} properties
   * @returns {epiviz.plugins.charts.MyTrack}
   */
  epiviz.plugins.charts.MyTrackType.prototype.createNew = function(id, container, properties) {
      return new epiviz.plugins.charts.MyTrack(id, container, properties);
  };

  /**
   * @returns {string}
   */
  epiviz.plugins.charts.MyTrackType.prototype.typeName = function() { return 'epiviz.plugins.charts.MyTrack'; };

  /**
   * @returns {string}
   */
  epiviz.plugins.charts.MyTrackType.prototype.chartName = function() { return 'My Track'; };

  /**
   * @returns {string}
   */
  epiviz.plugins.charts.MyTrackType.prototype.chartHtmlAttributeName = function() { return 'mytrack'; };

  /**
   * @returns {epiviz.measurements.Measurement.Type}
   */
  epiviz.plugins.charts.MyTrackType.prototype.chartContentType = function() { return epiviz.measurements.Measurement.Type.FEATURE; };
  ```

5. In the settings file, add an entry in the `chartTypes` property corresponding to the track type.

  **Example**

  ```javascript
  ...
  chartTypes: [
      ...,
      'epiviz.plugins.charts.MyTrackType'
  ],
  ...
  ```

6. If using EpiViz on a remote server, plug in your scripts and settings file and start using them! (For more information, see [Plugging in external scripts and settings]({{ site.baseurl }}/plugins.html))

  **Example**
  * `my-track.js`: [http://gist.github.com/florin-chelaru/11017610#file-my-track-js](http://gist.github.com/florin-chelaru/11017610#file-my-track-js)
  * `my-track-type.js`: [https://gist.github.com/florin-chelaru/11017650#file-my-track-type-js](https://gist.github.com/florin-chelaru/11017650#file-my-track-type-js)
  * `my-epiviz-settings.js`: [https://gist.github.com/florin-chelaru/11017763#file-my-epiviz-settings-js](https://gist.github.com/florin-chelaru/11017763#file-my-epiviz-settings-js)

  In the Track Menu, notice the new type of visualization, called **My Track**

  ![The new track in the Track Menu]({{ site.baseurl }}/img/scr_add_mytrack.png)

  <img src="{{ site.baseurl }}/img/scr_mytrack.png" style="max-width: 100%" />

---

**[See it in EpiViz]({{ site.epivizUiMain }}?ws=Y8kWxCO2Ajn&settings=http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js&script[]=http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js&script[]=http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js)**
