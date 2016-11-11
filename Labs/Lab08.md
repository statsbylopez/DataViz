# DataViz Lab 08
Your Name Here  



***General instructions for labs***: 

Today we are going to work in groups to answer questions of your choice. Our data set of interest looks at 2016 election polls, initally compiled by 538 and linked from [here](https://www.kaggle.com/benhamner/2016-us-election). 

You can directly access the .csv in R by using the following code: 


```r
polls.16 <- read.csv("http://projects.fivethirtyeight.com/general-model/president_general_polls_2016.csv")
polls.16 %>% head()
```

The goals of this lab are to: 

1. Review mutivariate visualization techniques. Feel free to consider any type of plot that we've looked at. There are both continuous and categorical variables in the polling data set.
2. Review mapping techniques. 
3. Review accuracy & margins of error
4. Get used to presenting with your partner(s)!

You have roughly 1-hour to conduct the analysis of your choice. At the end, please knit your Markdown file as an HTML. Once knit, click `Publish` in the top right-hand corner of the HTML. Follow the prompts. Our class user name is `SkidmoreDataViz` and our password is `skidmore`. 

During the final twenty minutes of class, each group will summarize two charts of their choice. 

**Note 1**: you can link the polls data to the USA mapping data using, for example, the following code:


```r
states <- map_data("state")
dim(states)
states %>% head(3)
polls.16.ave <- polls.16 %>%
  mutate(region = tolower(state)) %>%
  group_by(region) %>% 
  summarise(clin.vote.ave = mean(adjpoll_clinton))
  
states.clinton <- inner_join(polls.16.ave, states, by = "region")
```

**Note 2**: For your presentation, feel free to focus in on one region, state, candidate, or time period, poll grade, etc. 

**Note 3**: Adjusted poll numbers attempt to account for the demographics of the sample to better represent who will vote on election day.

**Note 4**: You are allowed to look at actual election results and compare them to the polls if you'd like. 
