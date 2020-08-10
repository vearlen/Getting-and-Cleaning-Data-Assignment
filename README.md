# Getting-and-Cleaning-Data-Assignment
Repository for final assignment on getting and cleaning data course on coursera.org from Johns Hopkins University 

#### Here is the description of the assignment:
Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names.
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

All steps are described in run_analysis.R file. 

In general the workflow is the following:


* import train/test data
* import labels for activities
* import id for subjects
* add activity labels based on id, add subjects based on id/position
* merge datasets train + test
* select columns with mean and standard deviation only
*on that stage I have descriptive names and labels already*
* group_by subject and activity and calculate mean
 

The feature names used for observations are described in text file, accessible after download here:*'data/UCI HAR Dataset/features_info.txt'* or in CodeBook.md file.

