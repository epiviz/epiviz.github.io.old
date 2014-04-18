---
layout: page
title: Data Provider Plugin Tutorial
---

**TODO**
**[See it in EpiViz]({{ site.epivizUiMain }}?ws=Y8kWxCO2Ajn&settings=http://rawgit.com/florin-chelaru/11017763/raw/029170a67f62bbd8a98dc38761a9cf363476fc40/my-epiviz-settings.js&script[]=http://rawgit.com/florin-chelaru/11017650/raw/93c16c0af3f3246c348c217d738f625c72de66b9/my-track-type.js&script[]=http://rawgit.com/florin-chelaru/11017610/raw/bbf488cebe8d0b35e23c993b8cd8a851430dd6dc/my-track.js)**

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
