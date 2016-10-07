# DataViz Lab 05
Your Name Here  



***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

## Part I: Data and layers

The structure of `ggplot2` encourages a consistent structure from plot to plot. We've created univariate and multivariate charts, occasionally with a layer. Moving forward, we'll add a bit of complexity - in the grammar of graphics, these are called layers. 

### Multiple groups, single aesthetic

Up until now, our grouping has focused on colors, either with different colored points or, relatedly, different fills. Other data sets will call for an advanced complexity. In many situations, you want to separate your data into groups and render different units the same way - in other words, distinguish between individual subjects without labeling them.

Let's start by looking at the `Oxboys` data set, which is part of the `nlme` package.


```r
install.packages("nlme")
library(nlme)
library(ggplot2)
library(dplyr)
head(Oxboys)
```

Our interest is in the trajectory of each boy. Here are plots we could have considered using:


```r
ggplot(Oxboys, aes(age, height, colour = Subject)) + 
  geom_point()

ggplot(Oxboys, aes(age, height, colour = Subject)) + 
  geom_line()

ggplot(Oxboys, aes(age, height, colour = Subject)) + 
  geom_line() + 
  geom_point()
```

While colouring by subject helped distinguish the individuals, there are way too many of them! 

**Q1.**: Let's say we could get rid of that nasty legend to the right of the plot. What are some lingering difficulties with this separation of individuals (i.e., why would the plot still be flawed?)

Instead of separating individuals by colour, the `group` command will separate our geometric objects, but without a formal label.


```r
ggplot(Oxboys, aes(age, height, group = Subject)) + 
  geom_line() + 
  geom_point()
```

**Q2.** Describe the relationship between `age` and `height` among the 26 boys?  

**Q3.** The center of the x-axis is 0. What does it mean for age to be centered?

**Q4.** Drop the grouping variable in the chart. Can you see the sawtooth?

## Multiple groups, multiple layers

What if we wanted a trend line for all subjects? From earlier, we know that we can add a smoothed line by using `geom_smooth()`.


```r
ggplot(Oxboys, aes(age, height, group = Subject)) + 
  geom_line() + 
  geom_smooth()
```

Well this looks cool, but the smoothed line is calculated for each subject - **as an important point, this is because the `group = Subject` is the primary aesthetic in the plot.** That is, with `group = Subject` on the first line, we'll get a unique geometric shape (in this case, the line and the smoothed curve) for each Subject.

Instead, if we want a smoothed line for the entire data set, we can separate our aesthetics by shape.


```r
ggplot(Oxboys, aes(age, height)) + 
  geom_line(aes(group = Subject)) + 
  geom_smooth()
```

Much better. 

**Q5.** Return to high school algebra. Estimate the slope of the line in the chart above (using the main trend line for everyone), and interpret it with respect to the variables in the figure.

## Weighting observations

In each of the above scenarios (as well as in the earlier labs), our grouping variable is categorical. For example, it was `Subject` in the `Oxboys` data, `drugs` in our last lab, or `state` in lab 03.

What if our grouping variable is continous? 

Let's return to the `midwest` data set. 


```r
?midwest
midwest %>%
  head()
```

The following chart shows county level data comparing the percent of white residents and the percent below poverty.


```r
ggplot(midwest, aes(percwhite, percbelowpoverty)) + 
  geom_point()
```

Note that each county is weighted equally; that is, there's one point for the biggest county and the same size point for the smallest county.

**Q5.** Find the county with the largest population and the smallest population. Total population is defined as `poptotal`. 

To account for different sized counties, we can use the `size` aesthetic, which will map the size of each geometric object to a continuous variable.


```r
ggplot(midwest, aes(percwhite, percbelowpoverty, size = poptotal)) + 
  geom_point() 
```


**Q6**. Look at the following two sets of code. Why are they different from the code produced above? And what changes do they lead to?



```r
ggplot(midwest, aes(percwhite, percbelowpoverty)) + 
  geom_point(aes(size = poptotal)) 

ggplot(midwest, aes(percwhite, percbelowpoverty)) + 
  geom_point(size = poptotal) 
```


We can make a couple of tweaks to the above chart to make it more pristine.



```r
ggplot(midwest, aes(percwhite, percbelowpoverty, size = poptotal)) + 
  geom_point(aes(size = poptotal/1e6)) + 
  scale_size_area("Population\n(millions)", breaks = c(0.5, 1, 2, 4)) + 
  xlim(50, 100)
```

**Q7** Play around with the code above. What do the (i) `scale_size_area` command (ii) `breaks()` command and (iii) `poptotal/1e6` and `1e6` do?


**Q8** Add the following to the plot above: `+ xlim(50, 100)` and `+ ylim(0, 30)`. These commands will be critical moving forward.

We finish with two touches that may be of use. 



In the plot above, text size is proportional to county size.

Finally, we consider colour density is a mapping of our continuous variable.


```r
ggplot(midwest, aes(percwhite, percbelowpoverty)) + 
  geom_point(aes(colour = perchsd)) + 
  xlim(50, 100)
```

**Q9** Interpret the plot above - what is the link between percentage of white residents and percent below poverty, as it pertains to percentage with a high school diploma?

**Q10** Why did it make sense to use population total as it pertained to point size, but percent with a high school diploma as it pertained to the point's colour density?

Finally, we replace `geom_point()` with `geom_text(aes(label = county))`


```r
ggplot(midwest, aes(percwhite, percbelowpoverty, size = poptotal)) + 
  geom_text(aes(label = county)) + 
  scale_size_area("Population\n(millions)", breaks = c(0.5, 1, 2, 4)) + 
  xlim(50, 100)
```

Referring to our grammar of graphics, in this instance, the geometric object is the text!


## On your own

Compare the links between county-level percentage in poverty and education, accounting for things like state, population total, and race. There is no right answer!  But see what you can come up with. 

