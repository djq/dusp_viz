# Overview of workshop

* Basics: loading data, installing packages, exporting images
* Data management strategies
* Visualization approachs
* Using ggplot2
* Using spplot 


## Basic commands for R

This is a comment:

	# this line is ignored

Loading a dataframe into `R`:

	# a dataframe is similar to an excel-spreadsheet
	data <- read.csv('pathToFile\file.csv')  # this assumes a csv file with headers
	
To see the top part of your data use the `head` command:

	head(data)
	
To access a column use the $ notation:

	data$names

Installing a package:

	install.packages('ggplot2')	


## ggplot2

[ggplot2](http://had.co.nz/ggplot2/) is a visualization and analsis package for `R`. 


## spplot

## Class Data 

The datasets we will use will be avilable [here](http://)