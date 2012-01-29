# Overview of workshop

* Basics: loading data, installing packages, exporting images
* Data management strategies
* Visualization approachs
* ggplot2: small multiples
* spplot: make quick maps
* Spatial Operations (if there is enough time)

## Premise

Urban data can have multiple dimensions of information. Conside the US Census data which has several hundred fields. How can we explore patterns with only a general idea of what the data looks like?

## Basic commands for R

This is a comment:

	# this line is ignored
	
First, we are going to start by installing a package:

	install.packages('ggplot2')	

Try this for `spplot` also.


Loading data into a dataframe:

	# a dataframe is similar to an excel-spreadsheet
	# `dataSample` is a variable; you can use any name here
	dataSample <- read.csv('pathToFile\file.csv')  # this assumes a csv file with headers
	
To see the top part of your data use the `head` command:

	head(dataSample)
	
To access a column use the $ notation:

	dataSample$colName
	
### Try:
* Calculate mean and standard deviation of all columns
* Try plotting two columns against each other using `plot(x = data$ColName1, y = data$ColName2)`
* Run `plot(data)`
* Use the `subset` function. To use the help, type `help(subset)`

## Reading infrormation from a shapefile

To read in a `dbf` file you can use the following command:
	
	library(foreign) 	# load a library that is installed by default
	attributeTable <- read.dbf('pathToShapefile\shapefileName.dbf') # note this is to the `.dbf` part of the file. We are ignoring the spatial information

The first shapefile we are using here is a sample of parcels from New York.

	ny_parcels <- read.dbf('pathToShapefile\new_york_parcels.dbf')
	

## ggplot2

[ggplot2](http://had.co.nz/ggplot2/) is a visualization and analsis package for R. We are going to focus on using some of the plotting functions, and illustrate facet-plotting. First load the library into your workspace:

	library(ggplot2)
	
On the ggplot2 website you can see many different types of plots. The most basic is the `qplot()` function; the more complicated plotting function is `ggplot()`. First try using `qplot` to examine some of the NY data.

	qplot()
	
You can make the same plot using `ggplot`

	ggplot(data=ny_parcels, aes(res, tax)) + geom_point() 
	
You can use an almost identical command, but change what type of plot you want 
	
	ggplot(data=ny_parcels, aes(res, tax)) + geom_bar()
	
There are lots of different plot choices, but the data structure is very important. Essentially, the data frame should be long rather than wide. For example:

	X | Y | Measurement_A | Measurement_B
	1 | 3 |        4      |    2
	
While it seems like that is less efficient for storing the information, it enables easy plotting in ggplot2. For example: 

	ggplot(data=ny_parcels, aes(res, tax)) + geom_point(aes(colour = )) 
	
Finally, we will explore facet plotting. A facet is a grid of plots, organized by groups. In this example, the measurements `X` is plotted against `Y`, grouped by `boroughs:

	ggplot(data=ny_parcels, aes(res, tax)) + facet_grid( ~.boroughs)
	
Try this for alternative measurements. You could also create the groups through a statistical operation. For example if you wanted to split up a particular measuremnt by quantiles:

	quantile_values <- quantile(ny_parcels$tax) # calculate the quantile values for tax
	ny_parcels$quintile_group <- ny_parcels$tax
	
	ind <- cut(ny_parcels$tax, quantile_values, include.lowest = TRUE)
	split(y_parcels$tax, ind)
		
## spplot



## Class Data 

Dataset for New York:

* Energy:  By Zipcode
* Buildings: 
* Roads: 



The datasets we will use will be avilable [here](http://)