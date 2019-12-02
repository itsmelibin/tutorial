---
layout: tutorial
title:  "Javasctipr API"
date:   2019-11-05 15:54:40 +0530
permalink: 'javascript_api'
---


## Introduction
*Last Updated: 11/09/2019 JS Library Version: 3.1* 

This tutorial is a detailed introduction to the [Maps API for JavaScript 3.1](https://developer.here.com/documentation/maps/topics/quick-start.html).

The Maps API for JavaScript was refreshed in 2019 to include some key new features:
- vector rendering
- customization of the map's look and feel
- runtime change of the map style
- ability to hide/show base map data layers at the runtime
- map tilting and rotation
- extruded buildings
- fractional zoom levels
- interactive traffic flow information
- rich interactions with the map - retrieve feature's meta information
- introduction of APIKey as new authentication method

In this tutorial, you will create a feature-rich isoline routing visualization application.

![screenshot of final app](../../../assets/images/graphics/app.png)
 
You'll learn how to:
- set up an interactive map with controls and events.
- integrate the [Isoline Routing API](https://developer.here.com/documentation/routing/topics/request-isoline.html) using the built-in JavaScript bindings to the HERE location service APIs.
- dynamically change the isoline parameters and map style.
- provide search suggestions using the HERE [Geocoding Autocomplete API](https://developer.here.com/documentation/geocoder-autocomplete/topics/quick-start-get-suggestions.html).
- implement some slick CSS transitions.

You can view the [completed version of the application here](https://heremaps.github.io/developer-blog/javascript-api-3.1/).

Let's get started!

![Sample Image](https://images.pexels.com/photos/112460/pexels-photo-112460.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500)

## Download the template {#download-template}

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

### Some notes on helper functions

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
  
The file `js/helpers.js` contains various helper functions like query selectors, date formatters, and label formatters.

Throughout this tutorial, you will see the symbols `$` and `$$` a few times. These symbols are helper functions for querying the DOM.

```javascript
const $ = q => document.querySelector(q);
const $$ = qq => document.querySelectorAll(qq);
```

These are just shorthand functions that reference the native DOM methods `querySelector()` and `querySelectorAll()` in order to help with readability of our code. They may remind you of [jQuery](https://stackoverflow.com/questions/10787342/why-does-jquery-have-dollar-signs-everywhere).

## Initialize the platform and map {#initialize-map}

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


Inside of `index.html` add the following script imports to your document's `<head>`:

Next, we'll add our HERE platform credentials:

Inside of `config.js`, edit the `hereCredentials` object to add your App Id, App Code, and API Key:

You can find this information in the __Projects__ section of your Developer Portal account.

Let's switch over to the file called `app.js`. This is where we will initialize the JavaScript map.

Inside of `app.js`, paste the following code:

In the code above, we've:

- initialized the platform and basic interactive map.
- added behavior controls and events. This way we can pan around the map. Try holding down either option (Mac) or alt (Windows) to have fun panning around the map in 3D!
- initialize the router and geocoding service. We'll be using these later on in the tutorial.
- added an event listener to the window to make sure the map resizes when the browser changes sizes.

If you save the file and refresh, you'll see a basic map up and running!

## Add a draggable marker {#add-draggable-marker}

We'll want to enable the user to pick a starting point of the isoline by either searching for a city location or by dragging and dropping a marker. Whenever a marker's location is changed, we'll want to recalculate the isoline. 

Let's add a marker to the map and enable its position to be changed. Inside of `app.js`, paste the following code:

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



<iframe width="100%" height="300" src="//jsfiddle.net/itsmelibin/0nah92p7/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>


We'll create a new marker with the variable `marker` and add it to the map. Next, we'll add some event listeners to the map. We'll listen to some events:
- on `dragstart`, we'll want to disable the normal behavior so the marker can be dragged.
- on `drag`, we'll want to update the marker's geometry to wherever it's being dragged.
- finally, on `dragend`, we'll want to recalculate the isoline. Later on, we'll introduce a function called `calculateIsoline()`, which will do just that.

Go ahead and save the file and give it a try! Since the `calculateIsoline()` function doesn't yet exist, the map will give a small error. Try commenting the `calculateIsoline()` line temporarily to test out dragging the marker.

<iframe width="700" height="500" src="https://codepen.io/davidkpiano/pen/wMqXea" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>