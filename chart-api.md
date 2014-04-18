---
layout: page
id: mydi
title: Chart Plugin Tutorial
---

**[See it in EpiViz]({{ site.epivizUiMain }}?ws=Y8kWxCO2Ajn&settings=http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js&script[]=http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js&script[]=http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js)**

## To create a new visualization

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
