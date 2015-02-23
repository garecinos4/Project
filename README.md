Project 
=====================================


> The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. 
> 

> You should create one R script called run_analysis.R that does the following. 
> 
> 1. Merges the training and the test sets to create one data set.
> 2. Extracts only the measurements on the mean and standard deviation for each measurement.
> 3. Uses descriptive activity names to name the activities in the data set.
> 4. Appropriately labels the data set with descriptive activity names.
> 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

#Load the dplyr library.
library(dplyr)

#Step1: Merges the training and the test sets to create one data set.

* Load the data of the subject_train and subject_test. 
* Load the data of the x_train and x_test. 
* Load the data of the y_train and y_test.

train.temp1 <- read.table("train/X_train.txt")
test.temp1 <- read.table("test/X_test.txt")

train.temp2 <- read.table("train/subject_train.txt")
test.temp2 <- read.table("test/subject_test.txt")

train.temp3 <- read.table("train/y_train.txt")
test.temp3 <- read.table("test/y_test.txt")


* Merge the data.
x <- rbind(train.temp1, test.temp1)
subject <- rbind(train.temp2, test.temp2)
y <- rbind(train.temp3, test.temp3)

data <- cbind(x, y, subject)

#Step 2 Extracts only the measurements on the mean and standard deviation for each measurement.

* Load de features data  and find only the rows that contains mean() or std(), then load only the data of the columns that exists in the features dataframe, in a dataframe.

data_features<- read.table("features.txt")

features<-data_features[grep("mean\\(\\)|std\\(\\)", data_features$V2),]
features.data <- x[,features$V1]

#Step 3 Uses descriptive activity names to name the activities in the data set.

* Load the data of activity_labels.

data_activities <- read.table("activity_labels.txt")
 
activities <- inner_join(y, data_activities, by="V1")

activities.data <- activities$V2

##Step 4 Appropriately labels the data set with descriptive variable names. 

* Fix the names of the dataset, setting the names of features, activity and subject columns.

data <- cbind(features.data , activities.data, subject )
names(data) <- c(as.character(features$V2), "activity", "subject");

#Step 5 From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

* Create the final data, grupping the data by activity and subject, and calculating the mean with the summarise function.
data2 <- data;
final_data <- data2 %>% group_by(activity,subject) %>% summarise_each(funs(mean))
write.table(final_data, file="tidy_data.txt", row.names=FALSE) 