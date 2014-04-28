---
layout: page
title: Data Provider Plugin Tutorial
---

**[See it in EpiViz]({{ site.epivizUiMain }}?ws=IqvEuzLIiMd&gist[]=11026256&settings=default)**

In EpiViz, data can be retrieved simultaneously from any number of servers (like the UMD PHP server, or the Epivizr
Websocket server). The proxies between the servers and the EpiViz UI are called **Data providers**. Currently, EpiViz
has two predefined types of data providers: `epiviz.data.WebServerDataProvider`, and `epiviz.data.WebSocketDataProvider`.
However, through EpiViz' plugin mechanism, users can add new data providers to interface other sources of data.

## To create a new data provider

1. Create a new class and inherit `epiviz.data.DataProvider`.

  **Example**

  ```javascript
  goog.provide('epiviz.plugins.data.MyDataProvider');

  /**
   * @constructor
   * @extends {epiviz.data.DataProvider}
   */
  epiviz.plugins.data.MyDataProvider = function () {
      epiviz.data.DataProvider.call(this);
  };

  /**
   * Copy methods from upper class
   */
  epiviz.plugins.data.MyDataProvider.prototype = epiviz.utils.mapCopy(epiviz.data.DataProvider.prototype);
  epiviz.plugins.data.MyDataProvider.constructor = epiviz.plugins.data.MyDataProvider;

  epiviz.plugins.data.MyDataProvider.DEFAULT_ID = 'myprovider';

  /**
   * @param {epiviz.data.Request} request
   * @param {function(epiviz.data.Response)} callback
   * @override
   */
  epiviz.plugins.data.MyDataProvider.prototype.getData = function (request, callback) {
  };
  ```

2. Override and implement the `getData(request, callback)` method.

  The **Request/Response API** has the following format:

  ```javascript
  {
      requestId: number,
      type: string,
      data: Object.<string, *>
  }
  ```
  * `requestId` is a unique numeric id identifying the request; responses are paired with requests by using the same `requestId` value.
  * `type` can be either `'request'` or `'response'`, depending on the message type.
  * `data` is a map of key-value pairs of request/response arguments.

    For requests, `data` always has an `'action'` key, identifying the request type. The possible values for `'action'`
    are defined in the EpiViz API in the enum type [`epiviz.data.Request.Action`]({{ site.baseurl }}/docs/epiviz.data.Request.html#Action).
    The other request keys and values vary depending on the action.

    Actions are grouped in two categories - *Server Actions* (requests from the UI to the Data Provider) and *UI Actions* (requests from
    the Data Provider to the UI).

    **Server Actions**

    Action              | Args                    | Response format | Description
    --------------------|-------------------------|-----------------|------------
    `'getRows'`         | `'seqName': string`,<br/>`'start': number`,<br/>`'end': number`,<br/>`'metadata': string[]`, a list of metadata columns in the data source table<br/>`'datasource': string`, the name of the data source table | <pre>{<br/>  globalStartIndex: ?number,<br/>  useOffset: boolean,<br/>  values: {<br/>    id: number[],<br/>    start: number[],<br/>    end: number[],<br/>    metadata: Object.&lt;string, string[]&gt;<br/>  }<br/>}</pre> | Requests range data for a given data source.
                        |                         |                 |
    `'getValues'`       | `'seqName': string`,<br/>`'start': number`,<br/>`'end': number`,<br/>`'datasource': string`, the name of the data source table,<br/>`'measurement': string`, the name of the column in the table where values reside | <pre>{<br/>  globalStartIndex: ?number,<br/>  values: number[]<br/>}</pre> | Requests the values in a particular column of a data source table.
                        |                         |                 |
    `'getMeasurements'` |                         | <pre>{<br/>  id: string[],<br/>  name: string[],<br/>  type: string[],<br/>  datasourceId: string[],<br/>  datasourceGroup: string[],<br/>  defaultChartType: string[],<br/>  annotation: Array.&lt;Object.&lt;string, string&gt;&gt;,<br/>  minValue: number[],<br/>  maxValue: number[],<br/>  metadata: Array.&lt;string[]&gt;<br/>} | Requests all the measurements available to serve by the data provider.
                        |                         |  |
    `'getSeqInfos'`     |                         | <pre>Array.&lt;Array&gt;</pre> an array of arrays of three elements: sequence name, minimum and maximum base pair boundaries. For example: <pre>[['chr1', 1, 248956422], ['myChr', 1, 1000000000]]</pre> | Requests the sequence information - name and boundaries available to serve by the data provider.
                        |                         |  |
    `'search'`          | `'q': string`, a text to filter results by<br/>`'maxResults': number`, the maximum number of results to return |  | <span class="optional">optional</span> Requests gene and Affymetrix probe information for data that contains the given filter string.
                        |                         |  |
    `'saveWorkspace'`   | `'id': string`,<br/>`'name': string`,<br/>`'content': Object.<string, *>` |  | <span class="optional">optional</span> Sends information about the active workspace to the data provider, in order to be saved.
                        |                         |  |
    `'deleteWorkspace'` | `'id': string`,         |  | <span class="optional">optional</span> Requests the deletion of the workspace with the given id.
                        |                         |  |
    `'getWorkspaces'`   | `'q': string`,<br/>`'ws': string`|  | <span class="optional">optional</span> Requests all the workspace for the current user whose names match the given filter.
                        |                         |  |

    **UI Actions**

    Action              | Args                    | Response format | Description
    --------------------|-------------------------|-----------------|------------
    `'addMeasurements'` |                         |                 |
    `'removeMeasurements'` |                      |                 |
    `'addSeqInfos'`     |                         |                 |
    `'removeSeqNames'`  |                         |                 |
    `'addChart'`        |                         |                 |
    `'removeChart'`     |                         |                 |
    `'clearDatasourceGroupCache'` |               |                 |
    `'flushCache'`      |                         |                 |
    `'navigate'`        |                         |                 |

  **Example implementation**

  ```javascript
  goog.provide('epiviz.plugins.data.MyDataProvider');

  /**
   * @constructor
   * @extends {epiviz.data.DataProvider}
   */
  epiviz.plugins.data.MyDataProvider = function () {
      epiviz.data.DataProvider.call(this);

      this._mockMeasurement = new epiviz.measurements.Measurement(
        'my_feature_column', // The column in the data source table that contains the values for this feature measurement
        'My Feature Measurement', // A name not containing any special characters (only alphanumeric and underscores)
        epiviz.measurements.Measurement.Type.FEATURE,
        'my_datasource', // Data source: the table/data frame containing the data
        'my_datasourcegroup', // An identifier for use to group with other measurements from different data providers
                              // that have the same seqName, start and end values
        this.id(), // Data provider
        null, // Formula: always null for measurements coming directly from the data provider
        'Line Track', // Default chart type filter
        null, // Annotation
        -5, // Min Value
        25, // Max Value
        ['my_metadata'] // Metadata columns
      );
  };

  /**
   * Copy methods from upper class
   */
  epiviz.plugins.data.MyDataProvider.prototype = epiviz.utils.mapCopy(epiviz.data.DataProvider.prototype);
  epiviz.plugins.data.MyDataProvider.constructor = epiviz.plugins.data.MyDataProvider;

  epiviz.plugins.data.MyDataProvider.DEFAULT_ID = 'myprovider';

  /**
   * @param {epiviz.data.Request} request
   * @param {function(epiviz.data.Response)} callback
   * @override
   */
  epiviz.plugins.data.MyDataProvider.prototype.getData = function (request, callback) {
      var requestId = request.id();
      var action = request.get('action');
      var seqName = request.get('seqName');
      var start = request.get('start');
      var end = request.get('end');
      var datasource = request.get('datasource');

      // Return a genomic range of 100 base pairs every 1000 base pairs
      var step = 1000, width = 100;
      var globalStartIndex, firstStart, firstEnd;
      if (action == epiviz.data.Request.Action.GET_ROWS || action == epiviz.data.Request.Action.GET_VALUES) {
        globalStartIndex = Math.floor((start - 1) / step) + 1;
        firstStart = globalStartIndex * step + 1;
        firstEnd = firstStart + width;

        if (firstEnd < start) {
          firstStart += step;
          firstEnd += step;
        }
      }

      var globalIndex, s;
      switch (action) {
        case epiviz.data.Request.Action.GET_ROWS:
          if (firstStart >= end) {
            // Nothing to return
            callback(epiviz.data.Response.fromRawObject({
              data: { values: { id: [], start: [], end:[], strand: [], metadata:{my_metadata:[]} }, globalStartIndex: null, useOffset: false },
              requestId: requestId
            }));
            return;
          }

          var ids = [], starts = [], ends = [], strands = '*', myMetadata = [];
          for (globalIndex = globalStartIndex, s = firstStart; s < end; ++globalIndex, s += step) {
            ids.push(globalIndex);
            starts.push(s);
            ends.push(s + width);
            myMetadata.push(epiviz.utils.generatePseudoGUID(5)); // Random string
          }

          callback(epiviz.data.Response.fromRawObject({
            data: {
              values: { id: ids, start: starts, end: ends, strand: strands, metadata:{ my_metadata: myMetadata } },
              globalStartIndex: globalStartIndex,
              useOffset: false
            },
            requestId: requestId
          }));
          return;

        case epiviz.data.Request.Action.GET_VALUES:
          if (firstStart >= end) {
            // Nothing to return
            callback(epiviz.data.Response.fromRawObject({
              data: { values: [], globalStartIndex: null },
              requestId: requestId
            }));
            return;
          }

          var values = [];
          for (globalIndex = globalStartIndex, s = firstStart; s < end; ++globalIndex, s += step) {
            var v = Math.random()
              * (this._mockMeasurement.maxValue() - this._mockMeasurement.minValue())
              +  this._mockMeasurement.minValue();
            values.push(v);
          }

          callback(epiviz.data.Response.fromRawObject({
            data: {
              values: values,
              globalStartIndex: globalStartIndex
            },
            requestId: requestId
          }));

          return;

        case epiviz.data.Request.Action.GET_MEASUREMENTS:
          callback(epiviz.data.Response.fromRawObject({
            requestId: request.id(),
            data: {
              id: [this._mockMeasurement.id()],
              name: [this._mockMeasurement.name()],
              type: [this._mockMeasurement.type()],
              datasourceId: [this._mockMeasurement.datasourceId()],
              datasourceGroup: [this._mockMeasurement.datasourceGroup()],
              defaultChartType: [this._mockMeasurement.defaultChartType()],
              annotation: [this._mockMeasurement.annotation()],
              minValue: [this._mockMeasurement.minValue()],
              maxValue: [this._mockMeasurement.maxValue()],
              metadata: [this._mockMeasurement.metadata()]
            }
          }));
          return;

        case epiviz.data.Request.Action.GET_SEQINFOS:
          callback(epiviz.data.Response.fromRawObject({
            requestId: request.id(),
            data: [['chr1', 1, 248956422], ['myChr', 1, 1000000000]]
          }));
          return;

        default:
          epiviz.data.DataProvider.prototype.getData.call(this, request, callback);
          break;
      }
  };
  ```

3. Declare the data provider in your settings file, in the `dataProviders` property.

  **Example**

  ```javascript
  dataProviders: [
      ...,
      'epiviz.plugins.data.MyDataProvider'
  ],
  ```

4. If using EpiViz on a remote server, plug in your scripts and settings file and start using them! (For more information, see [Plugging in external scripts and settings]({{ site.baseurl }}/plugins.html))

  **Example**
  * `my-data-provider.js`: [https://gist.github.com/florin-chelaru/11026256#file-my-data-provider-js](https://gist.github.com/florin-chelaru/11026256#file-my-data-provider-js)
  * `my-epiviz-settings-new-provider.js`: [https://gist.github.com/florin-chelaru/11026220#file-my-epiviz-settings-new-provider-js](https://gist.github.com/florin-chelaru/11026220#file-my-epiviz-settings-new-provider-js)

  In the measurement selection dialogs, notice the new data source, **my_datasource** and the new measurement, **My Measurement**

  ![The new data source]({{ site.baseurl }}/img/scr_mydataprovider_addblocks.png)

  ![The new measurement]({{ site.baseurl }}/img/scr_mydataprovider_addms.png)

  A heatmap displaying data provided by **My DataProvider**

  ![A heatmap with the new measurement]({{ site.baseurl }}/img/scr_mydataprovider_heatmap.png)

  A line track displaying data provided by **My DataProvider**

  <img src="{{ site.baseurl }}/img/scr_mydataprovider_line.png" style="max-width: 100%" />

  A blocks track displaying data provided by **My DataProvider**

  <img src="{{ site.baseurl }}/img/scr_mydataprovider_blocks.png" style="max-width: 100%" />



---

**[See it in EpiViz]({{ site.epivizUiMain }}?ws=IqvEuzLIiMd&gist[]=11026256&settings=default)**
