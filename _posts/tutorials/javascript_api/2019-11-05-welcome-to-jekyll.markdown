---
layout: tutorial
title:  "Javasctipr API"
date:   2019-11-05 15:54:40 +0530
permalink: 'javascript_api'
---


## Introduction
*Added various new features like iframe,codetab to documentation*

*markdowns can be hosted in giihub*

*Meun items are rendered dynamically*

Let's get started!

## Adding images

![Sample Image](https://images.pexels.com/photos/112460/pexels-photo-112460.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500)

## Attaching files {#download-template}

To get started with the tutorial, please download the template code:
- [template code](./template.zip)

The template code is provided so you can focus on implementing the Maps API code instead of focusing on trivial details like CSS.

When you download and unzip the template, you'll see the following file structure:

```
|--template
   |--index.html
   |--css
      |--index.css
      |--search.css
      |--sidebar.css
   |--js
      |--app.js
      |--config.js
      |--helpers.js
   |--resources
      |-- ...
      |-- ...
```

- `index.html` contains the app's HTML. You will only have to make minor edits to this file.
- the `css` directory contains the app's css. You won't have to make any changes to these files.
- the `js` directory contains all the application's logic. Throughout the tutorial, we'll be working with these files (and adding new ones).
- the `resources` directory contains various assets like fonts, icons, and images. You won't have to make any changes to these files.

You'll also want to start a local server during development.

## Adding youtube videos

<iframe width="560" height="315" src="https://www.youtube.com/embed/54J_ZCbeJdc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

For example in Python 2.x, run the following command:

```bash
python -m SimpleHTTPServer 8888
```
and in Python 3.x:

```bash
python -m http.server 8888
```

You can then open your browser and view the app at [localhost:8888](localhost:8888).

## Adding google charts

<html>
    <head>
      <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
      <script type="text/javascript">
        google.charts.load("current", {packages:["corechart"]});
        google.charts.setOnLoadCallback(drawChart);
        function drawChart() {
          var data = google.visualization.arrayToDataTable([
            ['Task', 'Hours per Day'],
            ['Task1',     11],
            ['Task2',      2],
            ['Task3',  2],
            ['Task4', 2],
            ['Task5',    7]
          ]);
  
          var options = {
            title: 'My Daily Activities',
            is3D: true,
          };
  
          var chart = new google.visualization.PieChart(document.getElementById('piechart_3d'));
          chart.draw(data, options);
        }
      </script>
    </head>
    <body>
      <div id="piechart_3d" style="width: 900px; height: 500px;"></div>
    </body>
  </html>
  

## Adding Code tabs

Let's get started by adding the Maps API for JavaScript imports.

{% codetabs %}

{% codetab index.html %}

```html
<!-- JS API -->
<link rel="stylesheet" type="text/css" href="https://js.api.here.com/v3/3.1/mapsjs-ui.css" />
<script src="https://js.api.here.com/v3/3.1/mapsjs-core.js"></script>
<script src="https://js.api.here.com/v3/3.1/mapsjs-service.js"></script>
<script src="https://js.api.here.com/v3/3.1/mapsjs-ui.js"></script>
<script src="https://js.api.here.com/v3/3.1/mapsjs-mapevents.js"></script>
```
{% endcodetab %}

{% codetab config.js %}

```javascript
const hereCredentials = {
   id: 'YOUR-HERE-ID',
   code: 'YOUR-HERE-CODE',
   apikey: 'YOUR-HERE-APIKEY'
}
```
{% endcodetab %}

{% codetab app.js %}

```javascript

/* ...
 * Existing app.js code goes here
 * ...
 */

// Initialize HERE Map
const platform = new H.service.Platform({ apikey: hereCredentials.apikey });
const defaultLayers = platform.createDefaultLayers();
const map = new H.Map(document.getElementById('map'),       defaultLayers.vector.normal.map, {
   center,
   zoom: 12,
   pixelRatio: window.devicePixelRatio || 1
});
const behavior = new H.mapevents.Behavior(new H.mapevents.MapEvents(map));
const provider = map.getBaseLayer().getProvider();

//Initialize router and geocoder
const router = platform.getRoutingService();
const geocoder = platform.getGeocodingService();

window.addEventListener('resize', () => map.getViewPort().resize());

export { router, geocoder }
```
{% endcodetab %}

{% endcodetabs %}

__app.js__
```javascript

/* ...
 * map and platform code from previous section goes here
 * ...
 */

let polygon;
const marker = new H.map.Marker(center, {volatility: true});
marker.draggable = true;
map.addObject(marker);

// Add event listeners for marker movement
map.addEventListener('dragstart', evt => {
   if (evt.target instanceof H.map.Marker) behavior.disable();
}, false);
map.addEventListener('dragend', evt => {
   if (evt.target instanceof H.map.Marker) {
      behavior.enable();
      calculateIsoline(); 
   }
}, false);
map.addEventListener('drag', evt => {
   const pointer = evt.currentPointer;
   if (evt.target instanceof H.map.Marker) {
     evt.target.setGeometry(map.screenToGeo(pointer.viewportX, pointer.viewportY));
   }
}, false);
```

## Adding JS fiddle

<iframe width="100%" height="300" src="//jsfiddle.net/itsmelibin/0nah92p7/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

## Adding codepen

<iframe width="700" height="500" src="https://codepen.io/davidkpiano/pen/wMqXea" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>