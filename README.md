# Bellabeat-Case-Study

## Introduction
Bellabeat is a high-tech company that manufactures health-focused smart products. Bellabeat collects data on activity, sleep, stress, and reproductive health and uses these data to empower women with knowledge about their own health and habits.

Here in this project, An analysis of the different datasets will be conducted and recommendation based on findings from the data will be presented.

Tools like R and Tableau is used to perform different tasks of the project.

install.packages("tidyverse")
install.packages("sqldf")

#importing required libraries
library(tidyverse)
library(sqldf)

setwd("/Users/rkpre/Documents")

#importing the datasets
#DailyActivity Table
dailyActivity <- read_csv("dailyActivity_merged.csv")
head(dailyActivity)

#Changing ActivityDate into Date
dailyActivity<-rename(dailyActivity, Date = ActivityDate)

#Sorting by Date
dailyActivity<-dailyActivity[order(as.Date(dailyActivity$Date, format="%m/%d/%Y")),]
head(dailyActivity)

#SleepDay Table(separate time and date) 
sleepDay<-read_csv("sleepDay_merged.csv")
head(sleepDay)

#Seperate Activity hour into date and time
sleepDay<-separate(sleepDay,SleepDay,c("Date", "Time"),sep=" ")

#Sorting by Date
sleepDay<-sleepDay[order(as.Date(sleepDay$Date, format="%m/%d/%Y")),]
head(sleepDay)

#weightLogInfo Table(need to seperate date and time.)
weightLogInfo<-read_csv("weightLogInfo_merged.csv")
head(weightLogInfo)

#Seperate Activity hour into date and time
weightLogInfo<-separate(weightLogInfo,Date,c("Date", "Time"),sep=" ")

#Sorting by Date
weightLogInfo<-weightLogInfo[order(as.Date(weightLogInfo$Date, format="%m/%d/%Y")),]
head(weightLogInfo)


#dailyIntensities
dailyIntensities <- read_csv("dailyIntensities_merged.csv")
head(dailyIntensities)

#Changing ActivityDate into Date
dailyIntensities<-rename(dailyIntensities, Date = ActivityDay)

#Sorting by Date
dailyIntensities<-dailyIntensities[order(as.Date(dailyIntensities$Date, format="%m/%d/%Y")),]
head(dailyIntensities)

#dailySteps Table
dailySteps <- read_csv("dailySteps_merged.csv")
head(dailySteps)

#Changing ActivityDate into Date
dailySteps<-rename(dailySteps, Date = ActivityDay)

head(dailySteps)

#Sorting by Date
dailySteps<-dailySteps[order(as.Date(dailySteps$Date, format = "%m/%d/%Y")),]
head(dailySteps)

#dailyCalories Table
dailyCalories <- read_csv("dailyCalories_merged.csv")
head(dailyCalories)

#Changing ActivityDate into Date
dailyCalories <- rename(dailyCalories, Date= ActivityDay)
head(dailyCalories)

#Sorting by Date
dailyCalories <- dailyCalories[order(as.Date(dailyCalories$Date, format = "%m/%d/%Y")),]
head(dailyCalories)

#Checking for Null Value in daily Activity
map(dailyActivity, ~sum(is.na(.)))
map(sleepDay, ~sum(is.na(.)))
map(weightLogInfo, ~sum(is.na(.)))
map(dailyIntensities, ~sum(is.na(.)))
map(dailySteps, ~sum(is.na(.)))
map(dailyCalories, ~sum(is.na(.)))

#Replacing the Null Value found in Fat Column of WeightLogInfo with mean
weightLogInfo$Fat[is.na(weightLogInfo$Fat)] <- mean(weightLogInfo$Fat, na.rm = TRUE)
head(weightLogInfo)




#Merging Daily Activity with dailyIntensities, dailysteps,  SleepDay and WeightLogInfo
activity<-sqldf("SELECT
da.Id,
da.Date,
da.TotalSteps,
da.TotalDistance,
da.TrackerDistance,
da.LoggedActivitiesDistance,
da.VeryActiveDistance,
da.ModeratelyActiveDistance,
da.LightActiveDistance,
da.SedentaryActiveDistance,
da.VeryActiveMinutes,
da.FairlyActiveMinutes,
da.LightlyActiveMinutes,
da.SedentaryMinutes,
da.Calories,
sd.TotalSleepRecords,
sd.TotalMinutesAsleep,
sd.TotalTimeInBed
FROM 
dailyActivity da, sleepDay sd 
WHERE
da.Date=sd.Date AND da.Id=sd.Id")

final_activity_data<-sqldf("SELECT
a.Id,
a.Date,
a.TotalSteps,
a.TotalDistance,
a.TrackerDistance,
a.LoggedActivitiesDistance,
a.VeryActiveDistance,
a.ModeratelyActiveDistance,
a.LightActiveDistance,
a.SedentaryActiveDistance,
a.VeryActiveMinutes,
a.FairlyActiveMinutes,
a.LightlyActiveMinutes,
a.SedentaryMinutes,
a.Calories,
a.TotalSleepRecords,
a.TotalMinutesAsleep, 
a.TotalTimeInBed,
w.WeightKg,
w.Fat,
w.BMI
FROM 
activity a, weightLogInfo w 
WHERE
a.Date=w.Date AND a.Id=w.Id")

final_activity_data<-sqldf("SELECT
a.Id,
a.Date,
a.TotalSteps,
a.TotalDistance,
a.TrackerDistance,
a.LoggedActivitiesDistance,
a.VeryActiveDistance,
a.ModeratelyActiveDistance,
a.LightActiveDistance,
a.SedentaryActiveDistance,
a.VeryActiveMinutes,
a.FairlyActiveMinutes,
a.LightlyActiveMinutes,
a.SedentaryMinutes,
a.Calories,
a.TotalSleepRecords,
a.TotalMinutesAsleep, 
a.TotalTimeInBed,
a.WeightKg,
a.Fat,
a.BMI,
ds.StepTotal
FROM 
final_activity_data a, dailySteps ds 
WHERE
a.Date=ds.Date AND a.Id=ds.Id")

final_activity_data<-sqldf("SELECT
a.Id,
a.Date,
a.TotalSteps,
a.TotalDistance,
a.TrackerDistance,
a.LoggedActivitiesDistance,
a.VeryActiveDistance,
a.ModeratelyActiveDistance,
a.LightActiveDistance,
a.SedentaryActiveDistance,
a.VeryActiveMinutes,
a.FairlyActiveMinutes,
a.LightlyActiveMinutes,
a.SedentaryMinutes,
a.Calories,
a.TotalSleepRecords,
a.TotalMinutesAsleep, 
a.TotalTimeInBed,
a.WeightKg,
a.Fat,
a.BMI,
a.StepTotal,
di.SedentaryMinutes,
di.LightlyActiveMinutes,
di.FairlyActiveMinutes,
di.VeryActiveMinutes,
di.SedentaryActiveDistance,
di.LightActiveDistance,
di.ModeratelyActiveDistance,
di.VeryActiveDistance
FROM
final_activity_data a, dailyIntensities di
WHERE
a.Date=di.Date AND a.Id=di.Id")

write.csv(final_activity_data,"D:\\final_activity_data.csv")

![Dashboard bella](https://user-images.githubusercontent.com/53640666/161537430-e19cf027-58b7-4c34-8e63-0a24d755f41a.png)
