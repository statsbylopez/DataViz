# DataViz Lab 07
Your Name Here  



***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

## Part I: Accessing data

The first goal today is to be able to access data files from your own computer. To start, open up Microsoft Excel and enter in the following summary table regarding voter preferences for the 2016 election. If you don't have Excel, you can alternatively use a spreadsheet in Google Docs.


| Race   | Clinton | Trump|
| :------------ | :-----------: | -------------------: |
| Black| 623| 108 |
| White| 3190| 4100|
| Other| 1009  | 802|

Once you've entered the table, go ahead and save your file as a csv. For purposes of our lab, you can call the data `Lab07.csv`. I'd recommend saving it in a folder where you can store data for our class. 

The first thing you'll need to do is find the file path for your folder. If you can't find `Lab07.csv`, one option is to search for it in the control panel.

**Q1** Write the file path for `Lab07.csv`.

It's easy to read in data using R. Change the code below in order to access the data to Lab07.


```r
library(dplyr)
lab07 <- read.csv("<FilePath>/Lab07.csv")
lab07 %>% head()
```

**Q2** Make a bar plot of the percentage of voters of each race who are voting Clinton. Include bars that account for the margin of error of these percentages. Are the differences in the fraction of voters statistically meaningful? *Note*: Refer to Lecture 4 for reminders on barplot coding with summary data.

Hopefully, you now feel comfortable reading in data from .csv's that you can do so with your own data.



## Scraping data from HTML sites


Several sites store data in HTML tables, which are easy to read into R but often difficult to deal with. Let's use an example using baseball data. 

The website `Baseball Reference` stores data from the last several decades, including league standings. For an example, the site below

[http://www.baseball-reference.com/leagues/MLB/2016-standings.shtml](http://www.baseball-reference.com/leagues/MLB/2016-standings.shtml)

shows the standings at the end of the recent season.

We can read HTML tables into R using the `XML` package.


```r
library(XML) #You'll need to install this package first
url <- "http://www.baseball-reference.com/leagues/MLB/2016-standings.shtml"
tab.baseball <- readHTMLTable(url)
names(tab.baseball)
```

Baseball Reference stores its standings table as `expanded_standings_overall`. We'll grab that table and store it as something more appropriate.


```r
tab.standings <- tab.baseball$expanded_standings_overall
tab.standings %>% head()
```

Woilla!

## Data cleaning/Data wrangling

The good news is that we have easy access to excellent data. The bad news is we still need to do some formatting. 

First, the column labels are messy. 


```r
names(tab.standings)
```

From my perspective, columns 7, 20, 23, and 24 are each problematic to call. 


```r
names(tab.standings)[c(7, 20, 23, 24)]
```

**Q3** What is wrong with these four variable names? 

Instead of renaming **every** variable, we can rename certain ones as follows.


```r
names(tab.standings)[c(7, 20, 23, 24)] <- c("WinLossPct", "OneRun", "Gr500", "L500")
tab.standings %>% head(3) %>% print.data.frame()
```


Our next task is to identify teams performance in `OneRun` games - that is, how well MLB teams have done in games decided by exactly one run. As an example, the Chicago Cubs were 22-23 in one run games, which means that won 22 games by one run and lost 23 games by one run.

Because `OneRun` is stored as a won-loss record, we'll need to get the number of wins and losses to more aptly contrast team performance in one run contests. Fortunately, R has several tools capable of dealing with messy strings. One of them is the `separate` function, which is part of the `tidyr` package.


```r
library(tidyr) #You'll need to install this package first
tab.standings1 <- tab.standings %>%
  separate(col = OneRun, into = c("OneRunWin", "OneRunLoss"), sep = "-")
tab.standings1 %>% head()
```

As you see, `separate` took the messy `OneRun` variable, and turned it into two columns, `OneRunWin` and `OneRunLoss`.

Next, let's visualize team performance in one run games.


```r
tab.standings1 <- tab.standings1 %>%
  mutate(OneRunWin = as.numeric(as.character(OneRunWin)),
         OneRunLoss = as.numeric(as.character(OneRunLoss)),
         OneRunWLP = OneRunWin/(OneRunWin + OneRunLoss))
tab.standings1 %>% head()
```

**Q4** Which team finished with the best won-loss percentage in one-run games? Which team finished with the worst?

## For loops and data scraping.

**Warning: Advanced coding to follow**

Above, we looked at one year of team performance in one run baseball games. It would perhaps be more interesting to look at several years. 

One option would be to collect a different data frame for each year:


```r
url16 <- "http://www.baseball-reference.com/leagues/MLB/2016-standings.shtml"
url15 <- "http://www.baseball-reference.com/leagues/MLB/2015-standings.shtml"
url14 <- "http://www.baseball-reference.com/leagues/MLB/2014-standings.shtml"

standings16 <- readHTMLTable(url16)
standings15 <- readHTMLTable(url15)
standings14 <- readHTMLTable(url14)
```

Of course, this would get somewhat lengthy if we want to do it across several years. What we could do is write a loop to do this task across several years. To do that, we'll use a `for` loop.

First, we need a way of accessing different years of standings without using several different url names.


```r
seasons <- 2016
url <- paste("http://www.baseball-reference.com/leagues/MLB/", seasons, "-standings.shtml", sep = "")
url
```

You'll see why this makes sense in the forthcoming loop. Note that this code will take a few minutes to run - it's accessing roughly 35 years worth of data. See if you can answer question 6 while the `for` loop runs.

**Q5**. At each step below, identify what R is doing.


```r
standings.all <- NULL
for (seasons in 1980:2016){
  url <- paste("http://www.baseball-reference.com/leagues/MLB/", seasons, "-standings.shtml", sep = "")
  standings.year <- readHTMLTable(url)
  df.temp <- standings.year$expanded_standings_overall
  df.temp <- df.temp %>%
    select(Tm, `1Run`)
  df.temp$Season <- seasons
  standings.all <- rbind(standings.all, df.temp)
}
dim(standings.all)
standings.all <- standings.all %>% filter(Tm != "Avg")
```

Next, we return to code from earlier to extract team performance in one-run games.


```r
standings.all <- standings.all %>%
  separate(col = `1Run`, into = c("OneRunWin", "OneRunLoss"), sep = "-")
standings.all %>% head()
standings.all1 <- standings.all %>%
  mutate(OneRunWin = as.numeric(as.character(OneRunWin)),
         OneRunLoss = as.numeric(as.character(OneRunLoss)),
         OneRunWLP = OneRunWin/(OneRunWin + OneRunLoss))
standings.all1 %>% head()
```

Finally, we plot the data over time.

**Q6**. Make a plot of team performance in one-run games over time, faceting and/or grouping by team. Add appropriate titles and labels, as well as a horizontal line across 0.500 on the y-axis. Have any teams consistently outperformed or underperformed in this metric?

**Q7** Use similar code to track team won-loss percentage over time. This is the variable `W-L%`. Note that you do **not** need to use the `separate` function. 

