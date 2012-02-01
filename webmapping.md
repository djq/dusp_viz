## Overview

* Structure of a webpage: html, javascript
* Browser/Client interaction
* GoogleVis
* Openlayers
* Fusion Tables
* More things....


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