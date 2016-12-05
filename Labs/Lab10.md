# DataViz Lab 10
Your Name Here  



***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

***Goals of today's lab***: 

1. Improved colouring schemes and use of grey vs. colour contrast to highlight important observations.

2. Interactivity using `ggplotly`



## Part I: Colours

One of R's basic data sets provides information on 32 cars. We'll start there.


```r
library(ggplot2)
head(mtcars)
p <- ggplot(mtcars, aes(mpg, wt, colour = factor(cyl))) + 
              geom_point()
p 
```

As in above, the default colouring in R uses a combination of red, green, and blue to depict 4, 6, and 8-cylinder cars. If those aren't your ideal colours, its straightforward to tune the specifics of your graph. In this case, we use `scale_colour_manual`.


```r
p + scale_colour_manual(values = c("purple","red", "black"))
```

For reference, there are several lists of `ggplot2` colours: one common one is provided in the link below:

[http://sape.inf.usi.ch/quick-reference/ggplot2/colour](http://sape.inf.usi.ch/quick-reference/ggplot2/colour)

**Question 0**: How does `scale_colour_manual` assign colours to each factor? 


### RColorBrewer

#### Categorical variables

Since manually choosing colors can be difficult, a color specialist (Cynthia Brewer) created colours for print and web available in the package `RColorBrewer`. You will need to install and load this package to use.


```r
#install.packages("RColorBrewer")
library(RColorBrewer)
```

The exact colouring schemes in the `RColorBrewer` package can be obtained using:


```r
display.brewer.all()
```

In addition to Crayola-style names, a more general and machine-readable approach to color specification is as hexadecimal triplets. That's what is behind RColorBrewer. In other wors, Dark2 palette is actually stored:


```r
brewer.pal(n = 8, name = "Dark2")
```

For example, this scheme is labeled `Dark2`. The first colour in `Dark2` is `#1B9E77`; the hashtag doesn't mean anything, but the next 6 elements deal with the color consistency of the first colour in `Dark2`. 

We return to our plot of `mtcars` to use this colouring scheme, noting that we are choosing three colours to match the number needed for the scatter plot. 


```r
Colors <- brewer.pal(n = 3, name = "Dark2")
p + scale_colour_manual(values = Colors)
```
The above code will produce a chart that's identical to the one below, which uses `scale_colour_brewer()` to save a step in the process. 


```r
p + scale_colour_brewer(palette = "Dark2")
```

**Question 1** Re-code the line above, removing the `palette = "Dark2"`. What is wrong with the plot that shows up? 

**Question 2** Re-code the line above, trying alternative colour schemes from the display of colour brewer that you ran earlier.



#### Continuous variables

With categorical variables, it makes sense to choose colouring schemes to specify one unique colour for each factor. For continuous data, or for data with several factors on an ordinal scale, that's not always the case. 

Let's take a look back at the diamonds data (note that I am using a sample of the data to ensure a quicker lab). 


```r
dsamp <- diamonds[sample(nrow(diamonds), 1000), ]
d <- ggplot(dsamp, aes(carat, price)) +
  geom_point(aes(colour = clarity))
d
```

There are eight different clarity levels! Yikes. That's too many. Fortunately, we return to our colour brewer.



```r
d + scale_colour_brewer()
d + scale_colour_brewer(palette = "Greens")
```

**Question 3**: Describe the link between price and diamond carat. Next, use the colours to suggest what the impact of clarity is on price (recall that clarify `IF` is highest).

**Question 4**: Add `direction = -1` to the colour brewer code. What does that do to the ensuing chart? 


#### Adding a grey background. 

Hopefully, at this point you are able to adjust your colouring schemes as you see fit. However, what if this question was posed to you: 

*Using the diamonds plot, identify the type of association between price and carat among `VVS1` diamonds.*

That would be, well, difficult using the graph above.

One possibility is to use a grey background on most diamonds, leaving the colored diamonds to be featured using a single colour. In this case, we'll chart all of the points in grey, and add red points on top of the grey ones for the selected clarity.



```r
d1 <- ggplot(dsamp, aes(carat, price)) +
  geom_point(colour = "grey") + 
  geom_point(data = filter(dsamp, clarity == "VVS1"), 
             aes(carat, price), colour = "red")
d1
```

Now we're rolling. This is a really useful graphs for projects, when you may want to draw the audience's attention to certain points or regions of the graph.

**Question 5.** Add a smoothed trend to the above chart for *only* `VVS1` diamonds. Does it make sense to use a line or a curve?

Finally, we can do this for each diamond. We first need to use a neat trick, which is described in more detailed in a blog post [here](https://www.r-bloggers.com/plotting-background-data-for-groups-with-ggplot2/). 

This first step is to create a new data set, absent of the factor we are colouring on. For example, we can create a diamonds data set without clarity.


```r
dsamp.background <- select(dsamp, -clarity)
```

Next, we recreate a similar graph to the one in red above, this time faceted by clarity. 


```r
d1 <- ggplot(dsamp, aes(carat, price, colour = clarity)) +
  geom_point(data = dsamp.background, aes(carat, price), alpha = 0.8, colour = "grey") + 
  geom_point() + 
  facet_wrap(~clarity)
```



**Question 6**: Add a smoothed curve to each of the facets above, with a separate one for each clarity type. Describe the association between price and carat among different clarity types. 

**Question 7**: Refine the graph by: (i) reducing the darkness of the grey points by using `alpha = 0.4`, (ii) adding a better colour scheme, and getting rid of the clarity legend. 


## Interactivity

Making interactive plots in R is straightforward. By interactive, we refer to the user's ability to scroll over certain aspects of the graph for better identification.


```r
#install.packages("plotly")
library(plotly)
g <- ggplot(dsamp, aes(carat, price, colour=clarity)) + 
  geom_point()
ggplotly(g)
```

Note that when done in R, you can also zoom in and out by using the tabs on top of the graph. Additionally, you can use the cursor to select certain areas of the chart that you want to look at.

**Question 8**. Knit your lab with an interactive graph. Ensure that the interactivity shows up on the knit version.  


**Question 9**. Think of all the plots we've done this year, both univariate, bivariate, and multivariate. On which ones does it make the *most* sense? On which ones does it make the *least* sense?




## On your own I

Returning to the diamonds data set (using the `dsamp`). Create a histogram of diamond carat (`carat`), with a separate histogram for each cut (`cut`). As in above, colour your faceted histograms to ensure a grey background with the histograms of all diamonds in the `dsamp` data set. What do we learn about the distributions of diamont carats, both overall and within each cut?

*Hint*: On histograms, there is generally only one aesthetic, and we color in the bars using `fill = `. 



## On your own II

Using something besides a scatter plot, make an interactive plot using the diamonds data set. Think carefully about the role of interactivity, and be prepared to justify your choice. 



