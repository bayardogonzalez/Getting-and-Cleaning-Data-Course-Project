# Getting-and-Cleaning-Data-Course-Project
Week 3 Course Project
The purpose of this assingment was to accomplish the following:
1) Download data that is available publicly
2) Merge data that exists in different txt files and understand how this type of data structure works
3) Extract only data that is useful for our analysis in this case mean and std dv
4) Label the data set with the correct values that exist in another txt file
5) create a tidy data set that can be used 

We used the following:
==================================================================
Human Activity Recognition Using Smartphones Dataset
Version 1.0
==================================================================


So first we have to download the plyr package:
We then need to download the files that we need
We then bind the files so that we can work with them:
bind the x train and x test
bind the y train and y test
bind the subject x and subject y


library(plyr)

### Step 1
## Merge the training and test sets to create one data set
###-------------------------------------------------------------------------------
x_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("UCI HAR Dataset/train/Y_train.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
x_test <- read.table("UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("UCI HAR Dataset/test/Y_test.txt")
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
### Here we want to create the data set for X
x_databind <- rbind(x_train, x_test)
 ### Here we want to create the data set for y
y_databind <- rbind(y_train, y_test)
### Here we wan to create data set for subject
subject_databind <- rbind(subject_train, subject_test)

## Step 2
## Extract only the measurements on the mean and standard deviation for each measurement
####--------------------------------------------------------------------------------
features <- read.table("UCI HAR Dataset/features.txt")
### We get only columns with mean() or std() in their names
mean_and_std_features <- grep("-(mean|std)\\(\\)", features[, 2])
### We then subset the columns we want
x_databind <- x_databind[, mean_and_std_features]
### We have to correct the names of the columns
names(x_databind) <- features[mean_and_std_features, 2]

## Step 3
## Use descriptive activity names to name the activities in the data set
###------------------------------------------------------------------------

activities <- read.table("UCI HAR Dataset/activity_labels.txt")
### update values with correct activity names
y_databind[, 1] <- activities[y_databind[, 1], 2]

### correct column name
names(y_databind) <- "activity"

# Step 4
## Appropriately label the data set with descriptive variable names
###----------------------------------------------------------------

###correct column name 
names(subject_databind) <- "subject"
### bind all the data in a single data set
all_databind <- cbind(x_databind, y_databind, subject_databind)
# Step 5
### Create a second, independent tidy data set with the average of each variable
### for each activity and each subject
###--------------------------------------------------------------------------
averages_alldata <- ddply(all_databind, .(subject, activity), function(x) colMeans(x[, 1:66]))

write.table(averages_alldata, "c:/Users/gonzalb4/Documents/averages_alldata.txt", row.name=FALSE)
