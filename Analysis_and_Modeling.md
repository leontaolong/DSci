---
title: "DSci"
output:
  html_document: default
  pdf_document:
    allow_html: yes
    keep_tex: yes
    latex_engine: xelatex
  word_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(plotly)
```

## P1

### (a) 
```{r}
  P.triple <- 10 / (15 + 25 + 10)
  P.milk = 25 / (15 + 25 + 10)
  P.triple.or.milk <- P.triple + P.milk
```
The probability of Yoyo getting a Triple Chocolate M&M or a Milk Chocolate M&M is __`r P.triple.or.milk`__.
    
### (b) 
```{r}
  P.first.getting.triple <- 10 / (15 + 25 + 10)
  P.then.not.getting.dark <- (49 - 15) / 49
  P.first.and.then = P.first.getting.triple * P.then.not.getting.dark
```
The probability of Yoyo first getting a Triple Chocolate M&M (she will eat it) and Lily then not getting a Dark Chocolate M&M from the bag is __`r P.first.and.then`__.

### (c)
```{r}
  P.two.triple.rep.small <- 1/2 * 1/2
```
The probability of sampling two Triple Chocolate M&Ms in a row when sampling with replacement is __`r P.two.triple.rep.small`__. 
```{r}
  P.two.triple.norep.small <- 1/2 * 9/19
```
The probability when sampling without replacement is __`r P.two.triple.norep.small`__.

### (d)
```{r}
  P.two.triple.rep.big <- 1/2 * 1/2
```
The probability of sampling two Triple Chocolate M&Ms in a row when sampling with replacement is __`r P.two.triple.rep.big`__. 
```{r}
  P.two.triple.norep.big <- 1/2 * 999/1999
```
The probability when sampling without replacement is __`r P.two.triple.norep.big`__.
```{r}
## constructs empty vectors
P.rep = c()
P.norep = c()
sample.size = c()

increment <- (2000 - 20) / 20

## calculate each distribution with different sample size
for (spl.size in seq(20, 2000, increment)) {
  sample.size <- append(sample.size, spl.size)
  ## the probability of every single choosing is the same with replacement,   so just simply square it for chossing twice.
  P.rep <- append(P.rep, ((spl.size / 2) / spl.size)^2)
  P.norep <- append(P.norep, (spl.size / 2) / spl.size * (spl.size / 2 - 1)   / (spl.size -1))
}

plot_data <- data.frame(sample.size, P.rep, P.norep)

## Modify x and y labels
x <- list(title = "Total Number of M%M")
y <- list(title = "Probabilities")

plot_ly(plot_data, x = ~sample.size, y = ~P.rep, name = 'with_replaxement', type = 'scatter', mode = 'lines') %>%
  add_trace(y = ~P.norep, name = 'without_replacement', mode = 'lines') %>%
  layout(xaxis = x, yaxis = y)
```

Even though as the sample size gets larger and larger, the probability of sampling without replacement gets closer to sampling with replacement, which is considered as independent, sampling without replacement, to some extent, does affect later events since the sample size changes. It doesn't meet the definition of independent events that when two events are said to be independent of each other, the probability that one event occurs in no way affects the probability of the other event occurring. 

## P2
### (a)
```{r}
x<-seq(500,1500, length=2000) ## get writing speed ranginf from 500 to 1500
density <- dnorm(x, 1000, 150)
plot(x, density, col="darkgreen",xlab="paper writing speed (words per hour)", ylab="Density", type="l",lwd=2, cex=2, main="Distribution of paper writing speed (wph) of students at ULE", cex.axis=.8)
```

### (b)
```{r}
z.score <- (1068 - 1000) / 150
```
The Z-Score for Yoyo's writing speed is __`r z.score`__.
Since the significance level is not given, let's assume it to be __0.05__, which is widely used. From the Z table, we find that the p-value is __0.325166__ given the Z-Score. The Z-Score tells us that Yoyo's writing speed is __`r z.score`__ standard deviation above the mean. The associated p-value tells us that Yoyo's writing speed is faster than 0.5 + 0.325166 = __`r (0.5 + 0.325166) * 100`%__ of the population.

### (c)
- First, we define the null hypothesis to be that the use of M&Ms has no effect the students' paper writing speed, and alternative hypothesis to be that the use of M&Ms affects the students' paper writing speed.

- Second, we determine the significance level alpha to be __0.05__

- Third, we load the data and put 'present' and 'absent' data into to vectors, and run the `t.tset()` to get test stats
```{r}
paperNinja <- read.csv("./paperNinja.csv", header=TRUE, sep=",")
MnM.present <- c()
MnM.absent <- c()

for(i in 1:nrow(paperNinja)) {
    row <- paperNinja[i,]
    if (row$MMs == 0) { ## if MMs column is 0, put into MnM.present vector
      MnM.present[[row$ID]] <- row$WPH
    } else { ## otherwise, put into MnM.present vector
      MnM.absent[[row$ID]] <- row$WPH
    }
}

t.test(MnM.present, MnM.absent, conf.level = 0.95, paired = TRUE)
```

- In this paired, two-sided t-test, since the p-value is __0.3594__, which is higher than __0.05__, we cannot reject the null hypothesis that the use of M&Ms has no effect the students' paper writing speed. Thus, we conclude that statistically the use of M&Ms has no effect the students' paper writing speed.


## P3

### (a)

I like the EDA that explores what was the worst day to fly out of NYC in 2013 if you dislike delayed flights since the question can potentially reveal something about that paticular day. It might be related to some events, extreme weather conditions, or social/political reasons. Besides, if we know the specific day and find certain pattern about that day, we can better prepare for it and prevent it from happening. As customers, we can try to book flight on that day in the future as well.

### (b)

As a follow-up of the last question, I want to explore more about what are the worst days to fly out of NYC and are there specific patterned we can reveal. It's interesting to know that the worst day in 2013 was Sept 12, but querying more worst days, probably we can get more insights on this topic.

```{r}
# Stardard libraries
library(nycflights13)


#loading the flights data
flights %>%  
  #Selecting only the columns required-Month, Day, average Departure delay
  #and average arrival delay
  select(month, day, arr_delay, dep_delay) %>% 
  
  #We will just focus on positive delays. This means that we DON'T consider
  #early arrivals and departures as 'Delays' as it is intuitively incorrect.
  filter(arr_delay >= 0, dep_delay >= 0) %>%
  
  #Grouping by Month & Day as we want other operations to find the mean delay
  #for each day of the  month in the year 2013.
  group_by(month, day) %>%
  
  #Now we summarize the entire delay into a single column (avg_delay) by adding 
  #up the mean of the departure & arrival delays for each day of the month. We 
  #should have ideally 365 rows after this operation
  summarise(avg_delay =  mean(arr_delay, na.rm = TRUE) + 
              mean(dep_delay, na.rm = TRUE)) %>%
  
  #We now ungroup the grouped data set as we want to now calculate the highest 
  #delay amongst all the 365 days rather than just in a particular month
  ungroup() %>%
  
  #Now we arrange the delays in descending order to get the highest delay in the  
  #first row of our data set.
  arrange(-avg_delay) %>%
  
  #Selecting the first few rows of the data set. Which shows the highest delay and the 
  #month and day corresponding to it.
  head(20)
```

As we queried the top 20 days with most delays, hardly can we find a noticable pattern from these dates. we could conclude that there's no obvious association between the dates of a year themselves and the likelyhood of having flights delayed on that day. 

## P4

### (a)

```{r}
courseSuccess <- read.csv("./courseSuccess.csv", header=TRUE, sep=",")
str(courseSuccess) # get stats including dimentions and variable types of the data

## clean data by making sure that there's no misssing and duplicate value
courseSuccess <- unique(courseSuccess[complete.cases(courseSuccess), ])
```

### (b)
```{r}
hist(courseSuccess$GPA, breaks=20, col="grey", xlab="GPA", 
  	main="Distribution of GPA", xlim = c(2,5), ylim = c(0,12)) 
```
The graph shows that GPAs generally range from __2.8__ to __3.6__ and number of students get fewer as the GPA goes high. However, there's a outlier of __4.9__ GPA. Considering that we are collecting GPA on a __4.0__ scale, this might be a data error. 

### (c)
```{R}
linearMod <- lm(GPA ~ HardWork, data=courseSuccess)
print(linearMod)
```

- The linear model: __GPA__ = __2.6539__ + __0.2021__ * __HardWork__.

- The Beta0 __2.6539__ means that we would expect an average GPA 2.6539 for a student in the class with no HardWork at all.
- The Beta1 __0.2021__ means that if HardWork differed by one unit, GPA will differ by 0.2021 units, on average.

```{r}
plot(courseSuccess$GPA, courseSuccess$HardWork, pch = 16, cex = 1.3, col = "blue", main = "GPA ~ HardWork", xlab = "GPA", ylab = "HardWork")
abline(linearMod) ## linear regression line
p.value <- anova(linearMod)$'Pr(>F)'[1] ## get p-value
```

Based on the visualization and p-value __`r p.value`__, which is larger than __0.05__, the correlation is not statistically significant. 

### (d)
```{r}
## get Multiple Linear Regression
fit <- lm(courseSuccess$GPA ~ Gender + WatchTV + HardWork + SleepHours + PrevGPA, data=courseSuccess)
summary(fit) # show results
```

- The multiple regression model: __GPA = 4.30299 - 0.18096 * Gender +  0.05842 * WatchTV + 0.20593 * HardWork - 0.25697 * SleepHours + 0.04283 *  PrevGPA__.
- Based on the summary, HardWork (**) and SleepHours (*) have the most small p-value, so for these two predictors, we can reject the null hypothesis H0 Beta SleepHours = 0 and H0 Beta HardWork = 0.

### (e)
Since based on the stats from previous model, we can say that __Gender__ and __WatchTV__ have relatively little correlation to GPA, we will explore different combinations of the three predictors: __HardWork__, __SleepHours__, and __GPA__:

#### Model A predictors: WatchTV, HardWork, SleepHours
```{r}
# Multiple Linear RegressionModel A
another_fit_a <- lm(courseSuccess$GPA ~ WatchTV + HardWork + SleepHours, data=courseSuccess)
summary(another_fit_a) # show results
```
#### Model B predictors: HardWork, SleepHours, PrevGPA
```{r}
# Multiple Linear RegressionModel B
another_fit_b <- lm(courseSuccess$GPA ~ HardWork + SleepHours + PrevGPA, data=courseSuccess)
summary(another_fit_b) # show results
```
#### Model C predictors: HardWork, SleepHours
```{r}
# Multiple Linear Regression Model C
another_fit_c <- lm(courseSuccess$GPA ~ HardWork + SleepHours, data=courseSuccess)
summary(another_fit_c) # show results
```

From the three models, we can pick the Model C since it has the most Adjusted R-squared and least Residual standard error.

### (f)
Comparing these two models from (d) and (e):

- (d) __GPA = 4.30299 - 0.18096 * Gender +  0.05842 * WatchTV + 0.20593 * HardWork - 0.25697 * SleepHours + 0.04283 *  PrevGPA__ 

- (e) __GPA = 4.10644 + 0.22662 * HardWork - 0.22621 * SleepHours__ 

- The Adjusted R-squared of (d) is __0.2839__ and Residual standard error is __0.3477__.
- The Adjusted R-squared of (e) is __0.3217__ and Residual standard error is __0.3384__.

We find that the (e) would be a better model. Compared with those in (d), HardWork in (e) has a stronger correlation and SleepHours in (e) has a weaker correlation to GPA. I wonder why would the pattern makes the (e) model overall better and are these two predictors somehow correlated. 

### (g)
```{r}
 plot(fitted(another_fit_c), residuals(another_fit_c))
```

As we plot out the residual vs fitted graph, we can see that the residuals bounce randomly around the 0 line. The residuals roughly form a "horizontal band" around the 0 line: [-0.5, 0.5]. However, we also see that there's a dot above 1, which stands out from the basic random pattern of residuals. This suggests that there is a outlier.