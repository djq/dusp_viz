## Overview

* Structure of a webpage: HTML & Javascript
* Browser/Client interaction
* GoogleVis (R package)
* Openlayers
* Fusion Tables
* Approaches for larger datasets

## HTML and Javascript

`HTML` elements are the basic building-blocks of static webpages.
	
	<HTML>
		<HEAD>
		</HEAD>
		
		<BODY>
		</BODY>	
	
	</HTML>

`Javascript` (not Java!) is a dynamic scripting language that can run in your web-browser:

	var x;
	x = 10

We will be using `Javascript` today. 	

When you want to use javascript in your browser you need to include the file somehow. For example

	<HTML>
		<HEAD>
		< link to .js file goes here>
		</HEAD>
		
		<BODY>
		</BODY>	
	
	</HTML>

The exact syntax would be as follows:
	
	<!-- This is a comment in HTML-->
	<script type="text/javascript" src="pathToFile/OpenLayers.js" ></script>
	
Here we just linked the `OpenLayers` library to our `HTML` page. Comments in Javascript are as follows:

	// This is a comment in javascript
	

I recommend linking to your javascript file outside of your `HTML` file, as it is easier to view. You can have `Javascript` pasted in with the `HTML` but it gets messy quickly. I also suggest that you install [FireBug](http://getfirebug.com/) so that you can debug your webpages. A very useful feature in firebug is the `console.log()` function, and the interactive terminal.

	example: http://dl.dropbox.com/u/169746/duspViz/map0.html

## Browser/Client interaction

* Quick discussion
* Quick digression with `GoogleVis`

## GoogleVis (web-mapping package)

Using R and a Google library (googleVis), you can generate a map from country level information. First install the following packages:

	install.packages('RJSONIO')
	install.packages('googleVis')

This example is from [spatialanalysis.co.uk](http://spatialanalysis.co.uk/2011/02/using-r-to-map-with-google-chart-tools/), with very minor modifications. Use the dataset `UN_data.csv`:

	library(googleVis)										# attach the library	
	input<- read.csv("data.csv")							# load the data	
	
Run the following to select the variables that you are interested in:

	select<- input[which(input$Subgroup=="Total 5-14 yr"),]	# get all values from "input" where < Subgroup=="Total 5-14 yr" >	
	Map<- data.frame(select$Country.or.Area, select$Value)  # make a dataframe with two columns from the previous selection
	
	names(Map)<- c("Country", "Percentage")					# change the names in the dataframe	
	
Generate the map, which will open in your browser:

	Geo=gvisGeoMap(Map, locationvar="Country", numvar="Percentage",	options=list(height=350, dataMode='regions'))		
	plot(Geo)
	
Instead of using regions, you can also use cities (see Googles [city-code](http://code.google.com/apis/adwords/docs/appendix/cities_world.html) index)

Caveats: [from the documentation](http://code.google.com/p/google-motion-charts-with-r/) "In all cases Flash and an internet connection is required to view the visualisation output." Not open-source and not reliable in the very long-term (flash, TOCs etc.). 

## Openlayers

First, download and unzip the openlayers library to your local directory [library](http://openlayers.org/download/OpenLayers-2.11.zip). You can link to it online, but it's quicker to save it locally (you also have to copy the folders 'theme' and 'img' also.  `OpenLayers` is a great library, but the documentation can be a little sparse. The [examples](http://openlayers.org/dev/examples/) are very useful.

### Map 1: Basic Map
A basic map using [OpenStreetMap](http://openstreetmap.org)
`HTML`:

	<!DOCTYPE html>
	<html>
		<head>       
			
		<title>Web Map 1</title>
			
			<!-- ########################## CSS STYLE ########################## -->         
			<link rel="stylesheet" href="css/style.css" type="text/css">                      
			<link rel="stylesheet" href="css/openlayers.css" type="text/css"> 
				
			<!-- ########################## JS Libraries ########################## -->       
			<script type="text/javascript" src="libraries/OpenLayers.js"></script>	    	<!-- OpenLayers  -->			
			
			<!-- ########################## MY SCRIPTS ########################## -->
			<script type="text/javascript" src="js/initialize.js"></script>			
		
		</head>
	  
		<body onload="init();">
		
			<!-- Map canvas-->
			<div id="mapCanvas"></div>
			
		</body>
	
	</html>

`Javascript`:

	var map;
	
	//This function makes the map
	function initialize(){    		
			
			map = new OpenLayers.Map("mapCanvas");				// make a map object
			osmLayer = new OpenLayers.Layer.OSM();				// using OpenStreetMap (OSM)
			map.addLayer(osmLayer);								
											
			var mapCenter = new OpenLayers.LonLat(0, 47);		// make a lon/lat object
			map.setCenter(mapCenter, 3);  						// set the zoom level
						
	  
	}
	
`CSS`:

	#mapCanvas{ 
		position: absolute; 
		top:0; 
		left:0; 
		width: 100%;  
		height: 100%;	
		}


#### Try 

* setting this to be centered around MIT
* changing the zoom scale so it focuses on our building

### Map 2: Several different basemaps

Add the following line to your `HTML` file so that you can use google as a basemap:

	<script src="http://maps.google.com/maps/api/js?v=3.5&amp;sensor=false"></script>

A more sophisticated example here:

	var map;
	
	function initialize() {
		map = new OpenLayers.Map('mapCanvas');
		map.addControl(new OpenLayers.Control.LayerSwitcher());
		
		var gphy = new OpenLayers.Layer.Google(
			"Google Physical",
			{type: google.maps.MapTypeId.TERRAIN}
		);
		var gmap = new OpenLayers.Layer.Google(
			"Google Streets", // the default
			{numZoomLevels: 20}
		);
		var ghyb = new OpenLayers.Layer.Google(
			"Google Hybrid",
			{type: google.maps.MapTypeId.HYBRID, numZoomLevels: 20}
		);
		var gsat = new OpenLayers.Layer.Google(
			"Google Satellite",
			{type: google.maps.MapTypeId.SATELLITE, numZoomLevels: 22}
		);
	
		map.addLayers([gphy, gmap, ghyb, gsat]);
	
		// Google.v3 uses EPSG:900913 as projection, so we have to
		// transform our coordinates
		map.setCenter(new OpenLayers.LonLat(10.2, 48.9).transform(
			new OpenLayers.Projection("EPSG:4326"),
			map.getProjectionObject()
		), 5);		
		
	}

### Map 3: Loading KML

[NYC Demo](http://project.wnyc.org/news-maps/hurricane-zones/hurricane-zones.html)


### Map 4: Fusion Tables

The benefit of using `Openlayers` is that there is a lot of flexibility regarding your base-map, and you know that the functions will work in a years time if you save the JS library locally. This is not the case with commercial JS APIs. 

I haven't fully explored how to use Fusion Tables with OpenLayers but it is probably possible.....
Here, we will use the Google API directly to make a map using fusion tables:

	<html style='height: 100%'>
	  <head>
		<script type='text/javascript' src='http://maps.google.com/maps/api/js?sensor=false'></script>
		<script type='text/javascript'>
		  function initialize() {
			var bc_office = new google.maps.LatLng(37.788901, -122.403806);
			var map = new google.maps.Map(document.getElementById('accident-map'), {
			  center: bc_office,
			  zoom: 13,
			  mapTypeId: google.maps.MapTypeId.ROADMAP
			});
			var accidents_layer = new google.maps.FusionTablesLayer(433634);
			accidents_layer.setMap(map);
		  }
		</script>
	  </head>
	  <body onload='initialize()' style='height: 100%; margin: 0px; padding: 0px'>
		<div id="accident-map" style='height: 100%'></div>
	  </body>
	</html>

Source: [The Bay Citizen](http://www.baycitizen.org/data/bike-accidents/)

From the [Guardian](http://www.google.com/fusiontables/embedviz?viz=MAP&q=select+col0%2C+col1%2C+col2%2C+col3%2C+col4%2C+col5%2C+col6%2C+col7%2C+col8%2C+col9%2C+col10%2C+col11%2C+col12%2C+col13%2C+col14%2C+col15%2C+col16%2C+col17%2C+col18%2C+col19%2C+col20%2C+col21%2C+col22+from+628653+&h=false&lat=51.502758957640296&lng=-0.00823974609375&z=12&t=1&l=col0):

	<html style='height: 100%'>
	<head>
	<script type='text/javascript' src='http://maps.google.com/maps/api/js?sensor=false'></script>
	<script type='text/javascript'>
	function initialize() {
		map = new google.maps.Map(document.getElementById('map_canvas'), {
			center: new google.maps.LatLng(51.502758957640296, -0.00823974609375),
			zoom: 12,
			mapTypeId: google.maps.MapTypeId.ROADMAP
		});
	
	layer = new google.maps.FusionTablesLayer({
		map: map,
		heatmap: { enabled: false },
		query: {
			select: "col4",
			from: "628653",
			where: ""
		}
		});
	}
	
	</script>
	</head>
	<body onload='initialize()' style='height: 100%; margin: 0px; padding: 0px'>
	<div id="map_canvas" style='height: 100%'></div>
	</body>
	</html>


### Strategies 

* Using the fusion table builder you can explore other datasets:
	http://gmaps-samples.googlecode.com/svn/trunk/fusiontables/fusiontableslayer_builder.html
	
* JsFiddle enables you to test out snippets. Here is an example with the Openlayers Library loaded in:

	http://jsfiddle.net/stephane_klein/VLKzY/2/
	


## Approaches for Larger Datasets

* PostGreSQL/PostGIS
* Spatial Querying of data




