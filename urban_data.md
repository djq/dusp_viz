# Overview of workshop

* Basics: interface, loading data, installing packages
* `ggplot2`: visualization and exploration
* `sp` and `maptools`: plot quick maps

### Datasets
The datasets we will use are available [here](https://github.com/djq/dusp_viz/blob/master/data.zip?raw=true). Make a folder for this class and unzip them there.

## Basic commands for R

This is a comment:

	# this line is ignored
	
First, we are going to start by installing a package:

	install.packages('ggplot2')	

Do this for `sp` and `maptools` also.

Loading data into a dataframe:

	# a dataframe is similar to an excel-spreadsheet
	# 'dataSample' is a variable; you can use any name here
	dataSample <- read.csv('pathToFile/file.csv')  # this assumes a csv file with headers
	
To see the top part of your data use the `head` command:

	head(dataSample)
	
To access a column use the $ notation:

	dataSample$colName
	
#### Try:
* Calculating the mean and standard deviation of all columns
* Examining the output of `summary(dataSample)`

### Reading information from a shapefile

To read in a `dbf` file you can use the following command:
	
	library(foreign) 												# load a library that is installed by default in R
	setwd('/Users/djq/Dropbox/dusp_viz')							# set your working directory
	attributeTable <- read.dbf('pathToShapefile/shapefileName.dbf') # note this is to the '.dbf' part of the shapefile. We are ignoring the spatial information

The first shapefile we are using here is a sample of tax-assessors parcels from New York. Open it in QGIS to examine it, then read in the attribute table:

	mn <- read.dbf('data/new_york/mn_small.dbf')
	
We are going to focus on exploring non-spatial patterns first.
	

## ggplot2

[ggplot2](http://had.co.nz/ggplot2/) is a visualization and analysis package for R. We are going to focus on using some of the plotting functions, and illustrate facet-plotting. First load the library into your workspace:

	library(ggplot2)	
	
On the ggplot2 website you can see many different types of plots. The most basic is the `qplot()` function; the more complicated plotting function is `ggplot()`. First try using `qplot` to examine some of the NY data.

	qplot(data=mn, x=YearBuilt, y=BldgArea)
	
This shows one huge value on the y-axis. Lets remove it (ignoring the underlying reasoning)

	mn <- subset(mn, mn$BldgArea < 1e7)		# we are overwriting the dataframe 'mn'

There also several 0 year values - remove them too
	
	mn <- subset(mn, mn$YearBuilt != 0)		# '!=' means 'does not equal'

Now replot:
	
	qplot(data=mn, x=YearBuilt, y=BldgArea)
	
You can make the same plot using `ggplot`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() 	
	
Now add a third dimension. We will use the amount of residential area to make the color (colour!) proportional to `ResArea`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(colour = ResArea))
	
Illustrate this third dimension using another approach. Use the amount of residential area to make the point proportional to `ResArea`

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(size = ResArea))
	
Put everything together (a little over the top and redundant, but still interesting)

	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point(aes(size = ResArea, colour=NumFloors))
	
Another plotting technique:

	ggplot(data=mn, aes(LandUse, YearBuilt)) + geom_boxplot()
	
Finally, let's group these plots by a category. `ggplot2` refers to this as 'facet plotting'. Rexamining the data one more time:

	head(mn)
	summary(mn)
	unique(mn$ZipCode)		# see all the unique values to get an idea how many groups there are
	
Now, make a plot by these groups:
	
	# facet plot by zipcode
	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() + facet_grid(~ZipCode)
	
	# facet wrap by zipcode
	ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point() + facet_wrap(~ZipCode, nrow = 2)
	
Try extending these plots by adding the previous dimensions, ilustrated by size or color.

### Saving your plots
	
	plot <- ggplot(data=mn, aes(YearBuilt, BldgArea)) + geom_point()
	ggsave()
	ggsave(plot, file="plot.pdf", width=4, height=4)
	

###	Joining more data to a dataframe

Load a dataframe of zipfiles for New York:

	elec <- read.csv('data/NY_Zip_energy.csv')

Now let's join this to tax-data, joining by zipcode keyword. I have aggregated the values from an NYC dataset, available [here](http://nycopendata.socrata.com/Environmental-Sustainability/Electric-Consumption-by-ZIP-Code-2010/74cu-ncm4)
		
Examine `elec` dataframe. Merge using `ZipCode` (note that spelling is identical in both dataframes):

	ny_data <- merge(mn, elec, 'ZipCode')
	
Try making a facet-plot by zipcode illustrating energy use.	
	
## sp

`sp` is another package in R that can be used for many spatial analyses. Here we are just using it to plot a shapefile.

	library(spplot)
	
We are not focusing on beautiful cartography with this package. We just want to make quick plots of the data that are understandable.

Load in the NY zipcode shapefile and the following libraries:
	
	library(sp)
	library(maptools) 
	
Now, load the spatial piece of NY_Zip_Energy (the `.shp):
	
	demo <- readShapePoly('data/ny_zip/NY_Zip_Energy.shp') 
	
And also load the attribute table for easy perusal:

	att <- read.dbf('data/ny_zip/NY_Zip_Energy.dbf') # load data
	
You can make a plot using the following commands:
	  
	spplot(demo, "kWh_res") # residential
	spplot(demo, "kWh_com") # commercial
	spplot(demo, "kWh_res", scales=list(draw = F), colorkey=F) # remove scales and key/legend
	
To save the plot (as a pdf, for example):

	ny_res_energy <- spplot(demo, "kWh_res") # store the plot	
	  
	pdf('ny_res_energy.pdf',height=5,width=5) # set up pdf
	print(ny_res_energy)    # print
	dev.off()               # close the PDF file
	
Fine tuning:
	
	spplot(demo, c("kWh_res","kWh_com")) # colour scale is a bit hard to read as one map has much larger values than the other
	
	spplot(demo, c("kWh_res","kWh_com"), names.attr = c("Residential","Commercial")) # change names
	
	spplot(demo, c("kWh_res","kWh_com"), col.regions = rainbow(100, start = 4/6, end = 1)) # tweaking colours

Scale-bars and further refinement are not easy to include. My preference would be to use another program for organizing these  details.
	
[spplot references](http://r-spatial.sourceforge.net/gallery/)



	









