---
title: "BellaBeat_casestudy"
author: "Minty Vo"
date: "29/01/2022"
output: html_document
---
*Welcome to my Bellabeat data analysis case study*

## About the company Bellabeat 
Bellabeat, is a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, and they have the potential to become a larger player in the global smart device market. Urška Sršen and Sando Mur founded Bellabeat, a high-tech company that manufactures health-focused smart products. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. 

## Scenario of the study 
In this study, I will focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. The insights will then help guide marketing strategy for the company

 I will follow the steps of the data analysis process: ask, prepare, process, analyze, share, and act. 

## Ask
 **Business task:** 
 
Smart devices are a big part of people’s everyday life. As a smart device manufacturer, Bellabeat can benefit from learning the trend of smart device usage and make data-driven business strategies to explore opportunities for growth.

 **Questions for the analysis:**
 
  1. What are some trends in smart device usage?
  
  2. How could these trends apply to Bellabeat customers?
  
  3. How could these trends help influence Bellabeat’s marketing strategy?
  
## Prepare

* Where is the data stored?

Data is publicly available on Kaggle: FitBit Fitness Tracker Data. 

* How is the data organized?

Data is stored in 18 csv files.

* Are there issues with bias or credibility in this data? Does the data ROCCC?

This Kaggle data set contains personal fitness tracker from 30 fitbit users. They were generated by respondents to a distributed survey via Amazon Mechanical Turk between 12.04.2016-12.05.2016.

 Reliable — LOW — Not reliable as it only has 30 respondents 
 Original — LOW — Third party provider (Amazon Mechanical Turk) 
 Comprehensive — MED — Parameters match most of Bellabeat product´s parameters  Current — LOW — Data is 6 years old and may not be relevant 
 Cited — LOW — Data collected from third party, hence unknown 
 “Overall, this dataset is considered “bad quality data” and it is not recommended to produce business recommendations based on this data”

* How are you addressing licensing, privacy, security, and accessibility?

30 eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. 

* How did you verify the data’s integrity?

As data is collected in a survey, we are unable to ascertain its integrity or accuracy.

* How does it help you answer your question?

This data explores smart device users’ daily habits. It includes information about daily activity, steps, sleep habits and heart rate, that can be used to explore users’ habits and find some trends.

* Are there any problems with the data?

Data was collected 6 years ago in 2016. Users’ daily activity, fitness and sleeping habits, diet and food consumption may have changed since then. Data may not be timely or relevant. Sample size of 30 FitBit users is not representative of the entire fitness population.

## Process
**Downloading the data**
 
The dataset was generated by respondents to a distributed survey via Amazon Mechanical Turk between 03.12.2016 and 05.12.2016. And include 18 CSV files. 

**Loading packages** 
I am going to install and load some R packages that will help in my analysis 

```{r message=FALSE, warning=FALSE}
# install.packages("tidyverse")
# install.packages("lubridate")
# install.packages("dplyr")
# install.packages("ggplot2")
# install.packages("tidyr")
# install.packages("here")
# install.packages("skimr")
# install.packages("janitor")
# install.packages ("RSQLite")
# install.packages("sqldf")
library(tidyverse)
library(lubridate)
library(dplyr)
library(ggplot2)
library(tidyr)
library(here)
library(skimr)
library(janitor)
library(RSQLite)
library(sqldf)
library(magrittr)
library(knitr)
```

Now I import all the dataset on RStudio and  use head(), colnames(), str() to quickly review the data 

```{r message=FALSE, warning=FALSE}
setwd("/Users/minty/Developer/Data_analysis_project")
daily_activity <- read_csv("dailyActivity_merged.csv")

head(daily_activity)

colnames(daily_activity)

str(daily_activity)


daily_calories <- read_csv("dailyCalories_merged.csv")

head(daily_calories)

colnames(daily_calories)


daily_intensities <- read_csv("dailyIntensities_merged.csv")

head(daily_intensities)

colnames(daily_intensities)


hourly_intensities <- read_csv("hourlyIntensities_merged.csv")

head(hourly_intensities)

colnames(hourly_intensities)


daily_steps <- read_csv("dailySteps_merged.csv")

head(daily_steps)

colnames(daily_steps)


heart_rate_sec <- read_csv("heartrate_seconds_merged.csv")

head(heart_rate_sec)

colnames(heart_rate_sec)


minute_METs <- read_csv("minuteMETsNarrow_merged.csv")

head(minute_METs)

colnames(minute_METs)


sleep_day <- read.csv("sleepDay_merged.csv")

head(sleep_day)

colnames(sleep_day)


weight_log <- read_csv("weightLogInfo_merged.csv")

head(weight_log)

colnames(weight_log)
```
 
 I’m going to Process, Clean and Organize the dataset for analysis. 
 I spotted some problems with the timestamp data. So before analysis, I need to convert it to date time format and split to date and time. 

```{r message=FALSE, warning=FALSE }
daily_activity$ActivityDate=as.POSIXct(daily_activity$ActivityDate, format="%m/%d/%Y", tz=Sys.timezone())
daily_activity$date <- format(daily_activity$ActivityDate, format = "%m/%d/%y")

sleep_day$SleepDay=as.POSIXct(sleep_day$SleepDay, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep_day$date <- format(sleep_day$SleepDay, format = "%m/%d/%y")
sleep_day$month <- format(sleep_day$SleepDay, format = "%B")

heart_rate_sec$Time=as.POSIXct(heart_rate_sec$Time, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
heart_rate_sec$date <- format(heart_rate_sec$Time, format = "%m/%d/%y")
heart_rate_sec$time <- format(heart_rate_sec$Time, format = "%H:M%:S%")

weight_log$Date=as.POSIXct(weight_log$Date, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
weight_log$date <- format(weight_log$Date, format = "%m/%d/%y")
weight_log$time <- format(weight_log$Date, format = "%H:%M:%S")

hourly_intensities$ActivityHour=as.POSIXct(hourly_intensities$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
hourly_intensities$time <- format(hourly_intensities$ActivityHour, format = "%H:%M:%S")
hourly_intensities$date <- format(hourly_intensities$ActivityHour, format = "%m/%d/%y")

```

## Analyze
The n_distinct() and nrow() functions are used to determine the number of unique values and the number of rows in a data frame, respectively.


```{r }
n_distinct(daily_activity$Id)

n_distinct(heart_rate_sec$Id)

n_distinct(minute_METs$Id)

n_distinct(sleep_day$Id)

n_distinct(weight_log$Id)

nrow(daily_activity)

nrow(heart_rate_sec)

nrow(minute_METs)

nrow(sleep_day)

nrow(weight_log)
```
The heart rate and weight log data frames contain a very low number of participants based on the n_distinct() outputs. Thus, reliable recommendations and conclusions cannot be made solely from these data frames.

The summary() function is used to pull key statistics about the data frames.
```{r }
daily_activity %>%
  select(TotalSteps,TotalDistance,SedentaryMinutes,LightlyActiveMinutes, FairlyActiveMinutes, VeryActiveMinutes,Calories)  %>%
  summary()
```
This summary shows the average user is taking 7638 steps a day, missing the recommended 10,000 steps for health by the CDC. On average, users are getting 21.16 minutes of very active or vigorous activity a day, this equates to 148.12 minutes a week. The CDC recommends 75 minutes of vigorous activity a week, so the typical Fitbit user is doing well in this area and achieving additional health benefits. In contrast, participants are averaging 991.2 minutes, or 16.52 hours of sedentary time a day! This is a significant amount of time and can lead to other health issues because the body functions best upright. Scientists have determined that 40 minutes of moderate to vigorous activity a day will balance out the effects of sitting up to 10 hours a day. Furthermore, this summary shows the average user is burning 2304 calories a day. Studies show the average person in the population burns 1800 calories a day, but burning 3500 is needed to lose a pound of weight. The Fitbit users in this case are burning more than the norm, and are on track to lose a few pounds a week if they so choose.


```{r }
heart_rate_sec %>%
  select(Value) %>%
  summary()
```
Despite the low number of users in the heart rate data frame, the average heart rate of 77 beats per minute (bpm) fits within the “normal” range. Normal is considered to be between 50 to 80 bpm for men, and 53 to 82 bpm for women. However, research suggests that it is more important for individuals to determine what is a normal and healthy heart rate for them, and not compare to population levels. This is because resting heart rates between different people can vary by as much as 70 bpm. Changes in resting heartrate over days can be a sign of infection, menstrual cycle effects, or other acute triggers. Thus, making heartrate a vital health characteristic to monitor.
```{r }
minute_METs %>%
  select(METs) %>%
summary()
```
The summary of minute METs shows the average user has a MET of 14.47. A MET is the ratio of your working metabolic rate relative to your resting metabolic rate. One MET is the energy you use when at rest. This means an activity with a MET of four, would require a person to exert four times the energy they do when they are sitting. Therefore, a user averaging 14.47 MET throughout the day is considerably high, which leads to the assumption that the Fitbit is not calculating this data point correctly. Due to this, the minute MET data frame will not be used further in this analysis.

```{r }
sleep_day %>%
  select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>% 
  summary()
```
The summary of the sleep data frame displays the average user sleeps once per day for 419.5 minutes, or roughly 7 hours. This falls within the CDC’s recommendations for adults in order to get the proper amount of rest. The average participant is spending 458.6 minutes in bed, or 7.64 hours. This means the typical user is spending 38.6 minutes awake in bed. According to Health Central, people should not spend more than 1 hour in bed awake. This is to prevent a mental link being formed between being awake and being in bed, which can lead to insomnia.


```{r }
weight_log %>% 
  select(WeightPounds,BMI) %>% 
  summary()
```
While this data frame has a low number of participants, the average BMI is 25.19. This is considered an overweight BMI. However, BMI can be a screening tool and does not diagnose the body fatness or health of an individual.

## Share
The ggplot() function of R Studio was used to create data visualizations that depict patterns and trends found in the data frames.

```{r }
ggplot(data=daily_activity,aes(x=VeryActiveMinutes, y=Calories)) + geom_point()+ stat_smooth(method = lm) +labs(title="The Relationship between Very Active Minutes and Total Daily Calories Burned")
```

Figure 1 depicts a positive relationship between very active minutes and total daily calories burned. This means that the more vigorous physical activity the participant did, the more calories they burned.

```{r }
ggplot(data=daily_activity,aes(x=TotalSteps, y=Calories)) + geom_point() + stat_smooth(method = lm) + labs(title = "The Relationship between the Total Daily Steps and Total Daily Calries Burned")

```

Figure 2 displays a positive relationship between total daily steps taken and total calories burned. This means the more steps the Fitbit users took, the more calories they burned.

```{r}
ggplot(data=sleep_day,aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) + geom_point() + stat_smooth(method = lm) + labs(title ="The Relationship between Total Minutes Asleep and Total Time in Bed")
```

Figure 3 depicts a positive relationship between total minutes asleep and total time in bed. For the most part, the time participants spent asleep and the time they spent in bed was very similar.

```{r}
d_activity_hr_intensities <- merge(daily_activity, hourly_intensities, by=c('Id','date'))
glimpse(d_activity_hr_intensities)

# Create new df with average intensity, grouped by the time, and na values dropped
hourly_intensity <- d_activity_hr_intensities %>%
  group_by(time) %>%
  drop_na() %>%
  summarise(mean_total_int = mean(TotalIntensity))

# Plot relationship between average intensity and time of day
bar_intensity <- ggplot(data=hourly_intensity, aes(x=time, y=mean_total_int)) + 
  geom_bar(stat = "identity", fill='#66b2b2') +
  theme(axis.text.x = element_text(angle = 90)) +
  labs(title="Average Total Intensity vs. Time", y="Average Intensity", x="Time of Day")

# Merge daily activity and weight log by id and date, and save as new df
weight_bmi <- merge(daily_activity, weight_log, by=c('Id', 'date'))
weight_bmi <- weight_bmi %>% select(-c("IsManualReport","LogId","WeightKg"))
glimpse(weight_bmi)

# Create new df with average weight in pounds, average BMI, grouped by id, and na values dropped
avg_weight_bmi <- weight_bmi %>%
  group_by(Id) %>%
  summarise (mean_weight = mean(WeightPounds), 
             mean_bmi = mean(BMI))

head(avg_weight_bmi)
max(weight_bmi$BMI)
min(weight_bmi$BMI)

# Create new df and classify weight type by BMI
bmi_health_table <- avg_weight_bmi %>%
  mutate(bmi_health = case_when(
    mean_bmi < 18.5 ~ "Underweight",
    mean_bmi >= 18.5 & mean_bmi < 24.9 ~ "Normal Weight", 
    mean_bmi >= 25 & mean_bmi < 29.9 ~ "Overweight", 
    mean_bmi >= 30 ~ "Obese"
  ))

# Create new df with percentage of each type of weight type
pct_bmi_health <- bmi_health_table %>%
  group_by(bmi_health) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(bmi_health) %>%
  summarise(total_percent = scales::percent(total / totals))

# Create vector for each weight type
pct_bmi_health$bmi_health <- factor(pct_bmi_health$bmi_health , levels = c("Obese", "Overweight", "Normal Weight", "Underweight"))

# Plot pie chart of percentage distribution of weight type
pie_pbh <- pct_bmi_health %>%
  ggplot(aes(x="",y=total_percent, fill=bmi_health)) +
  geom_bar(stat = "identity", width = 1)+
  coord_polar("y", start=0)+
  theme_minimal()+
  theme(axis.title.x= element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(), 
        panel.grid = element_blank(), 
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, size=14, face = "bold")) +
  scale_fill_manual(values = c("#8173bd","#ffa600","#009d9f"),
                    labels = c("Obese", "Overweight", "Normal Weight", "Underweight")) +
  geom_text(aes(label = total_percent),color="white",
            position = position_stack(vjust = 0.5))+
  labs(title="Weight type distribution")
  plot(pie_pbh)
```

This pie chart shows us that half of the participants were overweight. 

## Act

Bellabeat has been successful since its foundation by empowering women though providing data on their activity, sleep, stress, hydration levels, and reproductive health. Based on analyzing how Fitbit consumers use and respond to features, recommendations can be made to promote further growth for Bellabeat.

The Bellabeat app should be completely transformed and revamped. Rather than simply providing data on user’s health, the app should further encourage users to meet fitness goals and become a social media platform.

The CDC recommends working out with a friend in order to feel more motivated, be more adventurous in trying workouts, and to become consistent. The CDC even recommends the use of a social media workout app to connect with friends and reach your goals. The Bellabeat app could become that social media workout app that women turn to, by creating a sisterhood of supportive women ready to prioritize their health.

**Recommendations for Bellabeat app:**

1. Enable social networking so users can post their favorite workouts, wellness tips, healthy meals, etc.

2. Enable users to add friends and view each other’s activity.

3. Create weekly fitness and wellness challenges to encourage use.

4. Have health and fitness companies pay for advertising.

5. Recommend users to get 10,000 steps a day and enable alert notifications to encourage users to meet goal.

6. Recommend users to get at least 7 hours of sleep a night and enable alert notifications to encourage users to meet this.

7. Recommend users get 75 minutes of vigorous activity a week and enable alert notifications to entourage users to meet this.

8. Encourage users to enter in weight and height to track BMI.

9. If users are interested in losing weight, enable notifications to keep users on track to burn necessary calories to meet goal.

10. Enable alert notifications if user’s resting heart rate varies significantly from their normal.

11. Enable notifications to encourage activity if a user has spent an hour in bed awake.

12. Enable notifications to encourage activity if a user has been sedentary for an extended period of time.

**Recommendations for Bellabeat membership:**

1. Offer 30-day free trial subscription.

2. Offer reduced subscription fee when a member refers a friend.

3. Offer discounts for Bellabeat smart device products with membership.

4. Partner with health & fitness companies and offer discounts for members.

**Recommendations for Bellabeat products:**

1. Heavily market Spring as Fitbit does not track hydration levels.

2. Offer a bundle deal for the Spring and Leaf together.

*Works Cited:*

“3 Reasons to Work out with a Friend.” Centers for Disease Control and Prevention, Centers for Disease Control and Prevention, 23 Apr. 2021,

https://www.cdc.gov/diabetes/library/spotlights/workout-buddy.html

“About Adult Bmi.” Centers for Disease Control and Prevention, Centers for Disease Control and Prevention, 17 Sept. 2020,

https://www.cdc.gov/healthyweight/assessing/bmi/adult_bmi/index.html

“CDC — How Much Sleep Do I Need? — Sleep and Sleep Disorders.” Centers for Disease Control and Prevention, Centers for Disease Control and Prevention, 2 Mar. 2017,

https://www.cdc.gov/sleep/about_sleep/how_much_sleep.html

“The Dangers of Sitting: Why Sitting Is the New Smoking.” The Dangers of Sitting: Why Sitting Is the New Smoking — Better Health Channel, 22 Aug. 2020,

https://www.betterhealth.vic.gov.au/health/healthyliving/the-dangers-of-sitting

Gornall, Lucy. “How to Lose Weight: How Many Calories Should i Eat to Lose Weight?” GoodtoKnow, 12 Aug. 2020,

https://www.goodto.com/wellbeing/diets-exercise/what-is-calorie-how-many-lose-weigt-425557

Grey, Heather. “Heart Rates Can Vary by 70 Bpm: What That Means for Your Health.” Healthline, Healthline Media, 9 Feb. 2020,

https://www.healthline.com/health-news/what-your-heart-rate-says-about-your-health

“How Much Physical Activity Do Adults Need?” Centers for Disease Control and Prevention, Centers for Disease Control and Prevention, 7 Oct. 2020,

https://www.cdc.gov/physicalactivity/basics/adults/index.htm

Nield, David. “Scientists Figured out How Much Exercise You Need to ‘Offset’ a Day of Sitting.” ScienceAlert, 26 Nov. 2020,

https://www.sciencealert.com/getting-a-sweat-on-for-30-40-minutes-could-offset-a-day-of-sitting-down

Reed, Martin. “Spend Less Time In Bed If You Want More Sleep.” Healthcentral.com, 7 May 2017,

https://www.healthcentral.com/article/spend-less-time-in-bed-if-you-want-more-sleep

Roland, James. “What Are Mets, and How Are They Calculated?” Healthline, Healthline Media, 21 Oct. 2019, 

https://www.healthline.com/health/what-are-mets#calculation



