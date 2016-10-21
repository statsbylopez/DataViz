# DataViz Lab 06
Your Name Here  



***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

## Part I: Statistical (un)certainty, trend lines

The first goal today is to better understand what it means for groups or trends to be statistically significant, at least in the context of visualizing error.

Let's start by loading the `SAT` data set, which is part of the mosaic package. If you don't have the `mosaic` package, be sure to install this first.


```r
#install.packages("mosaic")
library(mosaic)
head(SAT)
?SAT
```

One potentially interesting link is the relationship between teacher salary and overall SAT score. In this plot, we make a scatter plot of these two variables.


```r
ggplot(SAT, aes(salary, sat)) + 
  geom_point() + 
  geom_smooth(method = "lm")
```

In the plot, the grey area reflects our uncertainty in the trend. Roughly, at each point on the -axis, the trend line *could* conceivably be going through any part of the grey area. Given this rough guideline, the easiest way to consider the statistical significance of a trend is to consider a horizontal line fit across the graph. 


```r
ggplot(SAT, aes(salary, sat)) + 
  geom_point() + 
  geom_smooth(method = "lm") + 
  geom_hline(aes(yintercept = 1000))
```

1. Try different horizontal lines above. Is there any horizontal line that fits entirely in the grey area? What does this suggest about the association between salary and SAT score?


We can also use a similar strategy with a curved trend line. 


```r
ggplot(SAT, aes(salary, sat)) + 
  geom_point() + 
  geom_smooth(method = "loess")
```

In either case, we can be confident that the association between these two variabiles does not appear due to chance alone.

2. Make a scatter plot of average sat score by average pupil/teacher ratio (`ratio`). Is this association positive or negative? Does the link between these two variables appear significant? 

## Part II: Statistical (un)certainty, continuous variables

We may also be interested in contrasting SAT scores within different subgroups using averages. This is common in many fields - you have heard of the `t-test`, for example. For visualizing differences in average, we commonly use error bars.

Our goal in this example is to compare the SAT scores among states with several test takers, relative to states with only a few test takers.

First, we make a variable `Fraction`, defined as whether or not 35\% or more of a state's students took the SAT.


```r
SAT <- SAT %>%
  mutate(Fraction = ifelse(frac >=35, "High rate", "Low rate"))
head(SAT)
```

Alabama, with only 8\% of its students taking the SAT, is defined a low-taking state, while Alaska, where 47\% of its students take the SAT, is defined as a high-taking state.

Our first step is to get sample statistics within each group.


```r
SAT.summary <- SAT %>%
  group_by(Fraction) %>%
  summarise(mean.states = mean(sat), sd.states = sd(sat), n.states = n()) %>%
  mutate(moe.states = 2*sd.states/sqrt(n.states))
SAT.summary
```

3. Describe what each step in the `summary()` and `mutate()` commands are doing above. 

4. The margin of error in low test-taking states is higher, despite having more states. Why?

5. Using code from Lecture 6, make a bar plot of the average SAT score - with error bars - comparing these state types. 



6. Is there a meaningful difference between the two groups, as far as SAT scores goes? Again, use the horizontal line trick to guide you.

7. Return to your scatter plot in question 1, adding a colour aesthetic of `Fraction`. Does this change your interpretation of the association between salary and SAT score? What is this an example of? 

## ggthemes

Let's have some fun. Install the `ggthemes` package.


```r
#install.packages("ggthemes")
library(ggthemes)
```

Using your scatter plot in question 7, try a few of the themes listed in the description linked [here](https://github.com/jrnold/ggthemes). Which are your favorite? *Note*: we add a theme to a ggplot by using `+ theme_fivethirtyeight()`, for example. 

