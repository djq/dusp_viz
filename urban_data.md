# Overview of workshop

* Basics: interface, loading data, installing packages
* ggplot2: visualization and exploration
* spplot: plot quick maps


## Datasets
The datasets we will use are available [here](https://github.com/djq/dusp_viz/blob/master/data.zip?raw=true)


## Basic commands for R

This is a comment:

	# this line is ignored
	
First, we are going to start by installing a package:

	install.packages('ggplot2')	

Do this for `spplot` also.

Loading data into a dataframe:

	# a dataframe is similar to an excel-spreadsheet
	# 'dataSample' is a variable; you can use any name here
	dataSample <- read.csv('pathToFile\file.csv')  # this assumes a csv file with headers
	
To see the top part of your data use the `head` command:

	head(dataSample)
	
To access a column use the $ notation:

	dataSample$colName
	
### Try:
* Calculating the mean and standard deviation of all columns
* Examining the output of `summary(dataSample)`

## Reading information from a shapefile

To read in a `dbf` file you can use the following command:
	
	library(foreign) 												# load a library that is installed by default in R
	setwd('/Users/djq/Dropbox/dusp_viz')							# set your working directory
	attributeTable <- read.dbf('pathToShapefile\shapefileName.dbf') # note this is to the '.dbf' part of the shapefile. We are ignoring the spatial information

The first shapefile we are using here is a sample of tax-assessors parcels from New York. Open it in QGIS to examine it, then read in the attribute table:

	mn <- read.dbf('data/new_york/mn_small.dbf')
	
We are going to focus on exploring non-spatial patterns first.
	

## ggplot2

[ggplot2](http://had.co.nz/ggplot2/) is a visualization and analsis package for R. We are going to focus on using some of the plotting functions, and illustrate facet-plotting. First load the library into your workspace:

	library(ggplot2)	
	
On the ggplot2 website you can see many different types of plots. The most basic is the `qplot()` function; the more complicated plotting function is `ggplot()`. First try using `qplot` to examine some of the NY data.

	qplot(data=mn, x=YearBuilt, y=BldgArea)
	
This shows one huge value on the y-axis. Lets remove it (ignoring the reasoning)

	mn <- subset(mn, mn$BldgArea < 1e7)		# we are overwriting the dataframe 'mn'

There also several 0 year values - remove them too
	
	mn <- subset(mn, mn$YearBuilt != 0)

Now replot:
	
	qplot(data=mn, x=YearBuilt, y=BldgArea)
	
You can make the same plot using `ggplot`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() 	
	
Now add a third dimension. We will use the amount of residential area to make the color (colour!) proportional to `ResArea`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(colour = ResArea))
	
Now add a third dimension. We will use the amount of residential area to make the point proportional to `ResArea`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(size = ResArea))
	
Put everything together (a little over the top and redundant, but still interesting)

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(size = ResArea, colour=NumFloors))
	
Finally, let's group these plots by a category. `ggplot2` refers to this as 'facet plotting'. Rexamining the data one more time:

	head(mn)
	summary(mn)
	unique(mn$ZipCode)		# see all the unique values to get an idea how many groups there are
	
Now, make a plot by these groups:
	
	# facet plot by zipcode
	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() + facet_grid(~ZipCode)
	
	# facet wrap by zipcode
	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() + facet_wrap(~ZipCode, nrow = 2)
	
Try extending these plots bya dding the previous dimensions, ilustrated by size or color.

### Saving your plots
	
	plot <- ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point()
	ggsave()
	ggsave(plot, file="plot.pdf", width=4, height=4)
	

###	Joining more data to a dataframe

Load a dataframe of zipfiles for New York:

	elec <- read.csv('data/NY_Zip_energy.csv')

Now let's join this to tax-data, joining by zipcode keyword. I have aggregated the values from an NYC dataset, available (here)[http://nycopendata.socrata.com/Environmental-Sustainability/Electric-Consumption-by-ZIP-Code-2010/74cu-ncm4]
		
Examine `elec` dataframe. Merge using `ZipCode` (note that spelling is identical in both dataframes):

	ny_data <- merge(mn, elec, 'ZipCode')
	
Try making a facet-plot by zipcode illustrating energy use.	
	
### spplot

spplot




## GoogleVis

Using R and a Google library (googleVis), you can generate a map from country level information. First install the following packages:

	install.packages('RJSONIO')
	install.packages('googleVis')

This example is from (spatialanalysis.co.uk)[http://spatialanalysis.co.uk/2011/02/using-r-to-map-with-google-chart-tools/]. Use the dataset `UN_data.csv`:

	library(googleVis)										# attach the library
	
	input<- read.csv("data.csv")							# load the data	
	
	select<- input[which(input$Subgroup=="Total 5-14 yr"),]	# get all values from "input" where < Subgroup=="Total 5-14 yr" >
	
	Map<- data.frame(select$Country.or.Area, select$Value)  # make a datrage with two columns
	
	names(Map)<- c("Country", "Percentage")					# 
	
	Geo=gvisGeoMap(Map, locationvar="Country", numvar="Percentage",	options=list(height=350, dataMode='regions'))
	
	plot(Geo)


Caveats: (from the documentation)[http://code.google.com/p/google-motion-charts-with-r/] "In all cases Flash and an internet connection is required to view the visualisation output."

Instead of using regions, you can also use cities (see Googles (city-code)[http://code.google.com/apis/adwords/docs/appendix/cities_world.html] index)

