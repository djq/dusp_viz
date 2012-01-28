# Overview of workshop

* Basics: loading data, installing packages, exporting images
* Data management strategies
* Visualization approachs
* Using ggplot2
* Using spplot 
* Spatial analysis

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
	data <- read.csv('pathToFile\file.csv')  # this assumes a csv file with headers
	
To see the top part of your data use the `head` command:

	head(data)
	
To access a column use the $ notation:

	data$names

### Try:
* Calculate mean and standard deviation of all columns
* Try plotting two columns against each other using `plot(x = data$ColName1, y = data$ColName2)`
* Run `plot(data)`
* Use the `subset` function. To use the help, type `help(subset)`



## ggplot2

[ggplot2](http://had.co.nz/ggplot2/) is a visualization and analsis package for R. We are going to focus on using facet-plotting today. 


## spplot



## Class Data 

Dataset for New York:

* Energy:  By Zipcode
* Buildings: 
* Roads: 



The datasets we will use will be avilable [here](http://)