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
Jorge L. Reyes-Ortiz, Davide Anguita, Alessandro Ghio, Luca Oneto.
Smartlab - Non Linear Complex Systems Laboratory
DITEN - Università degli Studi di Genova.
Via Opera Pia 11A, I-16145, Genoa, Italy.
activityrecognition@smartlab.ws
www.smartlab.ws
==================================================================

The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details. 

For each record it is provided:
======================================

- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.
- Triaxial Angular velocity from the gyroscope. 
- A 561-feature vector with time and frequency domain variables. 
- Its activity label. 
- An identifier of the subject who carried out the experiment.
- The dataset includes the following files:
=========================================

- 'README.txt'
- 'features_info.txt': Shows information about the variables used on the feature vector.
- 'features.txt': List of all features.
- 'activity_labels.txt': Links the class labels with their activity name.
- 'train/X_train.txt': Training set.
- 'train/y_train.txt': Training labels.
- 'test/X_test.txt': Test set.
- 'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent. 

- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 

- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. 

- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration. 

- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second. 

So first we have to download the plyr package:
We then need to download the files that we need
We then bind the files so that we can work with them:
bind the x train and x test
bind the y train and y test
bind the subject x and subject y


library(plyr)

## Step 1
## Merge the training and test sets to create one data set
##-------------------------------------------------------------------------------
x_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("UCI HAR Dataset/train/Y_train.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
x_test <- read.table("UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("UCI HAR Dataset/test/Y_test.txt")
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
## Here we want to create the data set for X
x_databind <- rbind(x_train, x_test)
 ## Here we want to create the data set for y
y_databind <- rbind(y_train, y_test)
## Here we wan to create data set for subject
subject_databind <- rbind(subject_train, subject_test)

## Step 2
## Extract only the measurements on the mean and standard deviation for each measurement
##--------------------------------------------------------------------------------
features <- read.table("UCI HAR Dataset/features.txt")
## We get only columns with mean() or std() in their names
mean_and_std_features <- grep("-(mean|std)\\(\\)", features[, 2])
## We then subset the columns we want
x_databind <- x_databind[, mean_and_std_features]
## We have to correct the names of the columns
names(x_databind) <- features[mean_and_std_features, 2]

## Step 3
## Use descriptive activity names to name the activities in the data set
##------------------------------------------------------------------------

activities <- read.table("UCI HAR Dataset/activity_labels.txt")

## update values with correct activity names
y_databind[, 1] <- activities[y_databind[, 1], 2]

## correct column name
names(y_databind) <- "activity"

## Step 4
## Appropriately label the data set with descriptive variable names
#------------------------------------------------------------------

## correct column name
names(subject_databind) <- "subject"
## bind all the data in a single data set
all_databind <- cbind(x_databind, y_databind, subject_databind)
## Step 5
## Create a second, independent tidy data set with the average of each variable
## for each activity and each subject
#--------------------------------------------------------------------------
averages_alldata <- ddply(all_databind, .(subject, activity), function(x) colMeans(x[, 1:66]))

write.table(averages_alldata, "c:/Users/gonzalb4/Documents/averages_alldata.txt", row.name=FALSE)
