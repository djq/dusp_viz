## Overview

* Structure of a webpage: HTML & javascript
* Browser/Client interaction
* GoogleVis (R package)
* Openlayers
* Fusion Tables
* More things....

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
	<script type="text/javascript" src="http://myfolder/OpenLayers.js" ></script>
	
Here we just linked the `OpenLayers` library to our `HTML` page. Comments in Javascript are as follows:

	// This is a comment in javascript
	

I recommend linking to your javascript file outside of your `HTML` file, as it is easier to view (you can have `Javascript` pasted in with the `HTML`). I also suggest that you install [FireBug](http://getfirebug.com/) so that you can debug your webpages.	A very useful feature in firebug is the `console.log()` function, and the interactive terminal.

## Browser/Client interaction

* Quick discussion
* Quick digression with `GoogleVis`

## GoogleVis (web-mapping package)

Using R and a Google library (googleVis), you can generate a map from country level information. First install the following packages:

	install.packages('RJSONIO')
	install.packages('googleVis')

This example is from (spatialanalysis.co.uk)[http://spatialanalysis.co.uk/2011/02/using-r-to-map-with-google-chart-tools/], with very minor modifications. Use the dataset `UN_data.csv`:

	library(googleVis)										# attach the library
	
	input<- read.csv("data.csv")							# load the data	
	
	select<- input[which(input$Subgroup=="Total 5-14 yr"),]	# get all values from "input" where < Subgroup=="Total 5-14 yr" >
	
	Map<- data.frame(select$Country.or.Area, select$Value)  # make a datrage with two columns
	
	names(Map)<- c("Country", "Percentage")					
	
	Geo=gvisGeoMap(Map, locationvar="Country", numvar="Percentage",	options=list(height=350, dataMode='regions'))
	
	plot(Geo)
	
Instead of using regions, you can also use cities (see Googles [city-code](http://code.google.com/apis/adwords/docs/appendix/cities_world.html) index)

Caveats: [from the documentation](http://code.google.com/p/google-motion-charts-with-r/) "In all cases Flash and an internet connection is required to view the visualisation output." Not open-source and not reliable in the very long-term (flash, TOCs etc.). 

## Openlayers

First, download and unzip the openlayers library to your local directory (library)[http://openlayers.org/download/OpenLayers-2.11.zip]. You can link to it online, but it's quicker to save it locally. `OpenLayers` is a great library, but the documentation is a little sparse. The (examples)[http://openlayers.org/dev/examples/] are very useful.

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
			<script type="text/javascript" src="libraries/proj4js-combined.js"></script>   	<!-- proj4  -->
			
			<!-- ########################## MY SCRIPTS ########################## -->
			<script type="text/javascript" src="js/initialize.js"></script>			
		
		</head>
	  
		<body onload="init();">
		
			<!-- Map canvas-->
			<div id="mapCanvas"></div>
			
		</body>
	
	</html>

`Javascript`:

	//This function makes the map
	function init(){    		
			
			map = new OpenLayers.Map("mapCanvas");				// make a map object
			osmLayer = new OpenLayers.Layer.OSM();				// using OpenStreetMap (OSM)
			map.addLayer(osmLayer);								// add OSM to map	
			map.addControls();
											
			var mapCenter = new OpenLayers.LonLat(0, 47);		// make a lon/lat object
			map.setCenter(mapCenter, 3);  						// set the zoom level
			
			map.addLayer(osmLayer);								// add layer to map
	  
	}
	
`CSS`:

	#mapCanvas{ 
		position: absolute; 
		top:0; 
		left:0; 
		width: 100%;  
		height: 100%;	
		}


A more sophisticated example here:

	var map;
	
	function init() {
		map = new OpenLayers.Map('map');
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
		
		// add behavior to html
		var animate = document.getElementById("animate");
		animate.onclick = function() {
			for (var i=map.layers.length-1; i>=0; --i) {
				map.layers[i].animationEnabled = this.checked;
			}
		};
	}





