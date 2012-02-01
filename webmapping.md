## Overview

* Structure of a webpage: HTML & javascript
* Browser/Client interaction
* GoogleVis
* Openlayers
* Fusion Tables
* More things....

## HTML and Javascript

`HTM`L elements are the basic building-blocks of static webpages.
	
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

## Browser/Client interaction



## GoogleVis (web-mapping package)

Using R and a Google library (googleVis), you can generate a map from country level information. First install the following packages:

	install.packages('RJSONIO')
	install.packages('googleVis')

This example is from (spatialanalysis.co.uk)[http://spatialanalysis.co.uk/2011/02/using-r-to-map-with-google-chart-tools/]. Use the dataset `UN_data.csv`:

	library(googleVis)										# attach the library
	
	input<- read.csv("data.csv")							# load the data	
	
	select<- input[which(input$Subgroup=="Total 5-14 yr"),]	# get all values from "input" where < Subgroup=="Total 5-14 yr" >
	
	Map<- data.frame(select$Country.or.Area, select$Value)  # make a datrage with two columns
	
	names(Map)<- c("Country", "Percentage")					
	
	Geo=gvisGeoMap(Map, locationvar="Country", numvar="Percentage",	options=list(height=350, dataMode='regions'))
	
	plot(Geo)
	
Instead of using regions, you can also use cities (see Googles [city-code](http://code.google.com/apis/adwords/docs/appendix/cities_world.html) index)

Caveats: [from the documentation](http://code.google.com/p/google-motion-charts-with-r/) "In all cases Flash and an internet connection is required to view the visualisation output." Not open-source and not reliable in the very long-term (flash, TOCs etc.). 

