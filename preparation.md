# Programs to install

Today we will analyze the presidential campaign contributions dataset. We
will go through the full process of downloading a new dataset, the
initial steps of understanding the data, visualizing it, coming up
with hypotheses, and exploring the dataset.  Hoefully, you'll learn
something new about presidential elections.

A lot of other organizations have analyzed the data:

* [Thing](http://url) is the organization that published the dataset, but also offers 

## First Steps

We assume that you have already [downloaded the
dataset](../day0/index.html).  We need to first unzip the file and rename
it to something meaningful:

    > unzip P00000001-ALL.zip
    > mv P00000001-ALL.txt donations.txt

Lets see how much data we are dealing with.  The word count (`wc`) command will tell us the number of lines in this 