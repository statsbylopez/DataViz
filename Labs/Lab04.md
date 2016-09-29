# DataViz Lab 04
Your Name Here  

***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

## Part I: Graph practice

**1.**-**3.**

Consider the following data from our lecture


|    | Clinton | Trump|
| :------------ | :-----------: | -------------------: |
| Black| 623| 108 |
| White| 3190| 4100|
| Other| 1009  | 802|

Table: Voting preferences, sample of 2016 voters

Sketch two charts, and identify what questions those charts are able and unable to answer.


## Part II: OkCupid data

OkCupid is a free online data site. In May of 2016, Danish researchers scraped user-level data from 70,000 OkCupid users, including user names, political leanings, and information on sex and drug preferences. For background information on the eventual controversy, see the Fortune story [here](http://fortune.com/2016/05/18/okcupid-data-research/).

While it would inappropriate for us to examine identifiable personal information, Middlebury Professor Albert Kim was nice enough to put de-identified portions of the data online for our exploration.

To set up the OkCupid data, first install the package:


```r
install.packages("okcupiddata")
```

Once you've downloaded the package, we grab the specific data set of interest:


```r
library(okcupiddata) 
library(dplyr)
library(ggplot2)
data(profiles)
names(profiles)
profiles %>%
  head()
```


**1.** What are the dimensions of the OkCupid data? Identify a continuous variable and a categorical variable.

## Sex, Drugs, and OkCupid

Our question to start is whether or not OkCupid users with preferences for drinking are also interested in drugs. In the `profiles` data, these are coded as `drinks` and `drugs`, respectively. We can access a summary of each variable by using the `count()` command within dplyr.


```r
profiles %>%
  count(drinks)

profiles %>%
  count(drugs)
```

Note that several people did not respond to each of these two questions.

The first graph we'll make is a barchart for `drinks`. 


```r
ggplot(profiles, aes(drinks)) + 
  geom_bar()
```

**2.** What happened to the subjects with missing information? What about the chart makes it difficult to interpret?

**3.** Make the same chart, only with the `drugs` variable. Why is missing data a bigger deal with this variable than with the drinking variable?

### Re-ordering a factor variable. 

One important characteristic that can increase the interpretability of charts is the order in which information is presented. Both `drinks` and `drugs` are ordinal categorical variables. 

To re-order a categorical variable, we have to enter in the order in which we want categories to appear in a chart using the `factor()` command.


```r
profiles <- profiles %>%
  mutate(drinks = factor(drinks, levels = c("not at all", "rarely", 
  "socially", "often", "very often", "desperately")), 
  drugs = factor(drugs, levels = c("never", "sometimes","often")))

ggplot(profiles, aes(drinks)) + 
  geom_bar()
```

Bam! Our barchart is now arranged in order, from least amount of alcohol to the most. As a bonus, we now see an additional bar for missing observations; these are individuals who did not answer this question.

### Bivariate analysis 

The following code creates a barplot showing the distribution of drug behavior (`drugs`) given a person's alcohol preference (`drinks`). 


```r
ggplot(profiles, aes(drinks, fill = drugs)) + 
  geom_bar()
```


**3.** Name two conclusions that are clear from this chart, and identify one or two weaknesses. Let's call this chart plot **A**

**4.** Refer to the code from the most recent lecture. Create both plot **B**, a stacked barchart showing percentages and plot **C.**, a side-by-side barchart.

**5.** Which of the plots made above (**A**, **B**, or **C**) best allow you to:

+ Identify the marginal distribution of drinks
+ Identify if someone is likely to be a `sometimes` drug user given their known `drinks` preference
+ Contrast different `drug` levels given a person's `drinks` preference.


**6.** In plot **B.**, the percentages of non-response (the grey area) grows with increasing drink levels. Any guesses as to why that is?

**7.** Using previous code, rotate the x-axis on plot **B** by 45-degrees, and change the axis labels. In particular, the y-axis could use a more appropriate label.


## Faceting

One useful tool in `ggplot()` is the facet, which allows us to view separate charts simulataneously, with a unique chart for different subgroups of a sample.

For example, what if we wanted to look at the distribution of drug-use given a person's alcohol preference, but to look separately for male and female users?


```r
ggplot(profiles, aes(drinks, fill = smokes)) + 
  geom_bar(position = "fill") + 
  facet_wrap(~sex)
```

**8.** Are there any obvious differences in the two graphs between the two genders?
