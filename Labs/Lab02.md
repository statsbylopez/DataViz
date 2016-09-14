# DataViz Lab 02
Your Name Here  

***General instructions for labs***: 

+  Open the R Markdown file (.Rmd) from the course Github page

+  Change the heading to include your author name

+  Save the R Markdown file (named as:  [MikeID]-[Lab01].Rmd -- e.g. "mlopez-Lab01.Rmd") to somewhere where you'll be able to access it later (zip drive, My Documents, Dropbox, etc)

+  Your file should contain the code/commands to answer each question in its own code block, which will also produce plots that will be automatically embedded in the output file

+  Each answer must be supported by written statements (unless otherwise specified) as well as any code used

##  Data manipulation & dplyr

When working with data you must:

+ Figure out what you want to do.

+ Describe those tasks in the form of a computer program.

+ Execute the program.

The dplyr package makes these steps fast and easy:

+ By constraining your options, it simplifies how you can think about common data manipulation tasks.

+ It provides simple “verbs”, functions that correspond to the most common data manipulation tasks, to help you translate those thoughts into code.

+ It uses efficient data storage backends, so you spend less time waiting for the computer.

This document introduces you to dplyr’s basic set of tools, and shows you how to apply them to data frames.

### Data: nycflights13

To explore the basic data manipulation verbs of dplyr, we’ll start with the built in `nycflights13` data frame. This dataset contains all several thousand flights that departed from New York City in 2013. The data comes from the [US Bureau of Transportation Statistics](http://www.transtats.bts.gov/DatabaseInfo.asp?DB_ID=120&Link=0), and is documented in `?nycflights13`


```r
library(nycflights13)
library(dplyr)
options(dplyr.width = Inf)
flights <- flights %>% na.omit()
head(flights, 4)
```

**1.** What type of variables are `month`, `carrier`, and `flight`? What are the dimensions of the `flight()` data?

### dplyr functions

The dplyr package aims to provide a function for each basic verb of data manipulation:

+ `filter()` (and `slice()`)
+ `select()` (and `rename()`)
+ `mutate()` 
+ `arrange()`
+ `summarise()`
+ `sample_n()`

### Filter rows with `filter()`

`filter()` allows you to select a subset of rows in a data frame. The first argument is the name of the data frame. The second and subsequent arguments are the expressions that filter the data frame.

For example, we select all flights on January 1st with (recall: the `%>%` acts like a **then** statement):


```r
flights %>%
  filter(month == 1, day ==1)
```

To select rows by position, we use `slice()`


```r
flights %>%
  slice(5:10)
```

**2.** What was the departure time of the 100th flight?   How many flights were there from JFK to Boston (BOS) on February 2nd?

### Select columns with `select()`

Often you work with large datasets with many columns but only a few are actually of interest to you. select() allows you to rapidly zoom in on a useful subset using operations that usually only work on numeric variable positions:


```r
# Select columns by name
flights %>% 
  select(year, month, day)

flights %>% 
  select(-day)
```


### Add new columns with `mutate()`

Besides selecting sets of existing columns, it’s often useful to add new columns that are functions of existing columns. This is the job of `mutate()`:


```r
flights %>%
  mutate(gain = arr_delay - dep_delay,
         speed = distance / air_time * 60, 
         delayed.cat = arr_delay >= 0)
```

**3.** Describe the three new variables above

**4.** Create a new variable to identify if the flight was delayed by more than an hour

**5.** Describe what is going on in the following code. Which rows are identified?


```r
flights %>%
  mutate(gain = arr_delay - dep_delay) %>%
  filter(gain > 150) %>%
  select(month, day, origin, dest, gain) 
```


### Arrange values with `arrange()`

Arrange does exactly what you think it does - it arranges variables from lowest to greatest (or alphabetically, for characters).


```r
flights %>% 
  arrange(distance) %>%
  head(5)

flights %>%
  arrange(-distance) %>%
  head(5)
```

The shortest flights are between Newark and Philly (80 miles), while the longest flights in the data set are between JFK and Honolulu (4983 miles).

### Summarise values with `summarise()`

The last verb is summarise(). It collapses a data frame to a single row and executes a specific function.


```r
flights %>%
  summarise(delay.ave = mean(dep_delay), delay.med = median(dep_delay))
```

**6.** Describe the two results above with respect to departure delay. What plausible - and statistical - reasons can you give for this difference?

**7.** Use the `min` and `max` functions to obtain the minimum and maximum flight delay times (at departure).



### Randomly sample rows

You can use `sample_n()` to take a random sample of rows. 


```r
flights %>%
  sample_n(5)
```

If you run the command a few times, you should observe different sets of 5 rows each time.

## Grouped operations

These verbs are useful on their own, but they become really powerful when you apply them to groups of observations within a dataset. In dplyr, you do this by with the `group_by()` function. It breaks down a dataset into specified groups of rows. When you then apply the verbs above on the resulting object they’ll be automatically applied *by group*. Most importantly, all this is achieved by using the same exact syntax you’d use with an ungrouped object.

In particular, `summarise()` is powerful and easy to understand, as described in more detail below.

In the following example, we split the complete dataset into destinations (`dest`) and then summarise each plane by counting the number of flights (count = `n()`) and computing the average distance (`dist.ave = mean(Distance)`) and arrival delay (`delay.ave = mean(ArrDelay)`). Note that `count`, `dist.ave`, and `delay.ave` are user inputs - that is, they can be named whatever we want them to.


```r
flights %>% 
  group_by(dest) %>%
  summarise(count = n(), 
  dist.ave = mean(distance), 
  delay.ave = mean(arr_delay)) %>%
  head(4)
```
  
**8.** Look up airport codes for the airports shown above. Do these numbers make sense given what we know about these airports?

**9.** Replace the `head()` command with `filter()` to obtain the average delay time and distance for flights into JFK.

### Putting it all together

A researcher is interested in analyzing the performance of all airlines which have at least 20000 departing flights from NYC. Our goal is to obtain the airline with the lowest average delay time (departure delay) as well as the highest proportion of flights that left on time.


```r
airlines <- flights %>%
  mutate(on.time = arr_delay <= 0) %>%
  group_by(carrier) %>%
  summarise(count = n(), delay.ave = mean(arr_delay), on.time.ave = mean(on.time)) %>%
  filter(count >= 20000) %>%
  arrange(delay.ave)
airlines
```

You can identify airline codes by visiting ```http://www.iata.org/publications/Pages/code-search.aspx```.

**10.** Describe each of the steps above, and consider how the order in which the steps are made is important.

## On your own

**11.** Find the five individual flights with the longest delay times in the data

**12.** Find the five flight numbers (`tailnum`) with the longest average delay times.

**13.** Identify the total distance and average travel distance within each month.

**14.** Create a new variable, `nighttime`, which is an indicator (TRUE/FALSE) for whether or not the flight took place at 7:00 PM or later (`hour >= 19`), and estimate the average distance and depature delay time within both `nighttime` groups.



