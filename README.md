# Getting and Cleaning Data

## Course Project

You should create one R script called run_analysis.R that does the following.

1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive activity names.
5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.



#Including required librairies 
if (!require("data.table")) {
  install.packages("data.table")
}

if (!require("reshape2")) {
  install.packages("reshape2")
}

library(data.table)
library(reshape2)

## Reading the needed data 
This section is for reading the data from the intial repo 
~~~~
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")

x_test <- read.table("UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("UCI HAR Dataset/test/Y_test.txt")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("UCI HAR Dataset/train/y_train.txt")
~~~~

~~~~
Features <- read.table("UCI HAR Dataset/features.txt")
names(Features) <- c("feature_id","feature")

names(x_test)<-Features$feature
names(x_train)<-Features$feature

names(subject_test)<-"ID"
names(subject_train)<-"ID"
~~~~
Getting the train dataSet and the test DataSet
~~~~~~
train <- cbind(subject_train, y_train, x_train)
test <- cbind(subject_test, y_test, x_test)
~~~~~~

## 1-Creating a Merge of train and test dataSets
Getting the combination of both datasets

~~~~

combined <- rbind(train,test)
~~~~


## 2. Extracts only the measurements on the mean and standard deviation for each measurement.
~~~~~~
meansdcol = grepl("mean\\(\\)", names(combined)) | grepl("std\\(\\)", names(combined))
#Keeping the first and second columns
meansdcol[1:2] <- TRUE 
Extracted <- combined[ , meansdcol]
~~~~~~
## 3Uses descriptive activity names to name the activities in the data set
## 4Appropriately labels the data set with descriptive activity names.
~~~~~~

activity_names<-read.table("UCI HAR Dataset/activity_labels.txt")
names(activity_names)<-c("Activity_ID","Activity")
names(combined)[2]<-"Activity"
names(Extracted)[2]<-"Activity"
combined$Activity <-factor(combined$Activity,labels = activity_names$Activity)
Extracted$Activity <-factor(combined$Activity,labels = activity_names$Activity)
~~~~~~
## 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.
~~~~~~
meltdata = melt(combined,id=c("ID","Activity"))
tidy_data = dcast(meltdata,ID+Activity ~ variable,mean)

write.table(tidy_data, file = "./tidy_data.txt")

~~~~~~
Thank you for reviewing 
