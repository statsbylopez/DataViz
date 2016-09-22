# DataViz Lab 03
Your Name Here  

***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

##  Graph practice

**1.**-**5.**

Make the five charts shown at the end of Lecture 03, linked [here](https://github.com/statsbylopez/DataViz/blob/master/Lectures/Lecture%203/Lecture_3.pdf). Describe one weakness for each plot (apart from the labels and theme) that hinder how one may interpret the visualization.

As a starter, please remember to load the `ggplot2` library (or to install it if you haven't already).

## Basic thematic tweaks

`ggplot` makes it easy to edit labels and make a title.

For example, let's look at the `midwest` data set, which contains demographic information of midwest counties.


```r
library(dplyr); library(ggplot2)
head(midwest)
dim(midwest)
```

We should be comfortable making a scatter plot. Here's one of `percbelowpoverty` by `percollege`.


```r
ggplot(midwest, aes(x = percollege, y = percbelowpoverty)) + 
  geom_point()
```

Unfortunately, while it is clear what they are representing, the variable labels are not pleasing to read on a plot. We can change the labels using the `xlab()` and `ylab()` commands, and can add a title using `ggtitle()`.


```r
ggplot(midwest, aes(x = percollege, y = percbelowpoverty)) + 
  geom_point() + 
  xlab("Percent college educated") + 
  ylab("Percent below the poverty line") + 
  ggtitle("Education % versus poverty % among 437 midwest counties")
```

**6.** Add a smoothed line or curve to the chart above, and describe the overall link between education percentage and poverty percentage. Does the graph's findings surprise you?

**7.** In the plot above, one county stands out for having a poverty percentage greater than 40. Using the `dplyr` package and the `filter()` command, identify this county. 

**8.** 

Describe what the following graph is showing, and add appropriate labels and a title

```r
ggplot(midwest, aes(x = percollege, colour = state)) + 
  geom_density() + 
  xlab("") + 
  ylab("Percent") + 
  ggtitle("")
```

## Combining `dplyr` and `ggplot()`

Our two favorite packages work well together, particularly when looking at within-group observations, creating new variables, or working within a subset of a larger data frame.

In looking at the `midwest()` data, a researcher is interested in comparing education levels among based on the percentage of each county that identifies as non-white. Unfortunately, no `non-white` variable exists.

**9.** Create a new variable, `non.white`, defined as the percentage of the population which identifies as non-white. Make a scatter plot of `percollege` by `non.white`, including an appropriate title and axis labels.

**10.** Repeat Question **9.**, only this time look only at counties in the state of Illinois (`state` = `IL`).

