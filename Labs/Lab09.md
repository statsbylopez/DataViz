# DataViz Lab 09
Your Name Here  



***General instructions for labs***: 

Today's lab is a review lab. It is not meant to be comprehensive; instead, the goal is to recap a few of the items we've covered this year. For the second exam, anything we've done this far is fair play as far as visualization and interpretation. From the first unit, there will be not be questions on the specific aspects of the grammar of graphics.

## Preface

There's a new version of `ggplot2`! This is wonderful news. You can upload the new version of the package using the following code:


```r
#update.packages("ggplot2")
```

The main highlights of the `ggplot2` package are found at this link [here](https://blog.rstudio.org/2016/11/14/ggplot2-2-2-0/). For our purposes, I want to focus on two new developments, both of which should be easy to implement. 

First, subtitles, and a more coordinated effort at labeling axes. Additionally, notice the title is now left-aligned (instead of center-aligned).


```r
ggplot(mpg, aes(displ, hwy)) +
  geom_point(aes(color = class)) +
  geom_smooth(se = FALSE, method = "loess") +
  labs(
    title = "Fuel efficiency generally decreases with engine size",
    subtitle = "Two seaters (sports cars) are an exception because of their light weight",
    caption = "Data from fueleconomy.gov"
  )
```


Second, when making barplots, the categories now stack in the same order as the legend.


```r
avg_price <- diamonds %>% 
  group_by(cut, color) %>% 
  summarise(price = mean(price)) %>% 
  ungroup() %>% 
  mutate(price_rel = price - mean(price))

ggplot(avg_price) + 
  geom_col(aes(x = cut, y = price, fill = color))
```

Pretty dope!


## Part I

Return to the polls data set, which stores information from polls taken prior to the 2016 election.  

You can directly access the .csv in R, and please use the following code to filter the data set.


```r
library(dplyr); library(ggplot2)
polls.16 <- read.csv("http://projects.fivethirtyeight.com/general-model/president_general_polls_2016.csv")
polls.16 <- polls.16 %>% select(-createddate, -timestamp) %>%
  filter(type == "polls-plus") %>%
  mutate(pollster.abbrev = substr(pollster, 1, 8), 
         clin.perc = adjpoll_clinton/100)
```

**Q1** Make the following plot, which shows the results from 6 top-rated polls on the same date. Refer to the code above the subtitle and footnote. Use the `clin.perc` variable above.


![](Lab09_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


**Q2** Summarize the plot in 3-4 sentences, including reference to the meaning of the error bars. What does this suggest about the polls, and Clinton's overall voting share on that date? 

**Q3** On a scale of 1 (least accurate) to 10 (most), rate this poll, and justify your opinion. What could be done to improve its accuracy?


## Part II

A pollster decides to take a look at the adjusted poll support for Donald Trump relative to the raw numbers. Reminder: the adjusted numbers account for the demographics of those polled, relative to who is expected to turn out for the election. 

Make the following scatter plot using the `polls.16` data. No additional data manipulation is needed. 

![](Lab09_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

**Q5** How do state-level trends compare to the solid line? And what does this suggest about the adjustment method for voting support?

**Q6** Comment on the state-to-state differences in the links between adjusted and raw voting percentages (**Note**: this is different than comparing each state's support for Trump, which is a much easier question). For example, consider how strong each state's line-of-best-fit is. Notice anything?


## Part III

A pollster is interested in the support for Gary Johnson. Use the following code to get you started. 


```r
library(ggthemes); library(maps)
states <- map_data("state")
dim(states)
```

```
## [1] 15537     6
```

```r
polls.16.ave <- polls.16 %>%
  mutate(region = tolower(state)) %>%
  group_by(region) %>% 
  summarise(johnson.vote.ave = mean(adjpoll_johnson, na.rm = TRUE), n.poll = n())
  
states.johnson <- inner_join(polls.16.ave, states, by = "region")
```

Now, make the following plot:

![](Lab09_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

**Q7** Comment on region-level differences in the support for Johnson in 1-2 sentences that the lay reader could understand.

**Q8** Which state was polled most often and least often? Does that effect your interpretation of the map? 
