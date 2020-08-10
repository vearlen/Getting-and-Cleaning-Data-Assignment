## Download and save the data 

```{r download and save}
# DOWNLOAD AND UNZIP DATA -------------------------------------------------

#save url as variable
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

#create directory for input data
dir.create("data")

#download archive
download.file(url,destfile = "data/dataweek4.zip",method = "curl")

#unzip file
unzip("data/dataweek4.zip", exdir = "data")
```

## Read training data



```{r training}
library(tidyverse)
#import features names
features <- read.table("data/UCI HAR Dataset/features.txt")
#give columns meaningful names
colnames (features) <- c("idfeature","feature") 

#import activity names
activity_labels <- read.table("data/UCI HAR Dataset/activity_labels.txt")
colnames (activity_labels) <- c("idactivity","activity") #give columns meaningful names

#import train data
features_train <- read.table("data/UCI HAR Dataset/train/X_train.txt")
#give features names from feature list
colnames(features_train) <- features$feature

#import training activities
activity_train <- read.table("data/UCI HAR Dataset/train/y_train.txt")
colnames(activity_train) <- "idactivity"

#import training subjects
subjects_train <- read.table("data/UCI HAR Dataset/train/subject_train.txt")
colnames(subjects_train) <- "idsubject"

#bind activity id with names for train data
activity_train_name <- right_join(activity_train,activity_labels) %>% select(activity)

#training dataset with subject ID and activity name
features_subj_activ_train <- bind_cols(features_train,subjects_train, activity_train_name)

```

## Read test data
```{r test}
#import train data
features_test <- read.table("data/UCI HAR Dataset/test/X_test.txt")
#give features names from feature list
colnames(features_test) <- features$feature

#import training activities
activity_test <- read.table("data/UCI HAR Dataset/test/y_test.txt")
colnames(activity_test) <- "idactivity"

#import training subjects
subjects_test <- read.table("data/UCI HAR Dataset/test/subject_test.txt")
colnames(subjects_test) <- "idsubject"

#bind activity id with names for test data
activity_test_name <- right_join(activity_test,activity_labels) %>% select(activity)

#training dataset with subject ID and activity name
features_subj_activ_test <- bind_cols(features_test,subjects_test, activity_test_name)

```

## Question 1

```{r q1}
#check the colnames are identical
identical(names(features_subj_activ_test),names(features_subj_activ_train))
#merge test and training dataset
merge_features <- bind_rows(features_subj_activ_test, features_subj_activ_train)
```

## Question 2-4

```{r q2-4}

merged_features_selected <- merge_features %>%
  select(matches ('activity|mean|std|idsubject'))
```

## Question 5
```{r q5}
average_persubj_peractivity <- merged_features_selected %>%
  group_by(idsubject,activity)%>%   #group by subject and activity
  summarise_all(mean) %>%           #calculate mean for each column
  rename_all(function(x)paste0 ("average_",x)) %>%    #add 'average_' as prefix
  rename(idsubject = average_idsubject) %>%     #rename backwards idsubject
  rename(activity = average_activity)           #rename backwards activity

#write tidy data out
write.table(average_persubj_peractivity,file= "data/tidy_data.txt", row.names = FALSE)

#import data for QC purpose
df <- read.table("data/tidy_data.txt", header = TRUE, check.names = FALSE)
#check names are identical after import
identical(names(df), names(average_persubj_peractivity))
rm(df) #clean up
```
### Features information

#### Feature Selection 


The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz. 

Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag). 

Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals). 

These signals were used to estimate variables of the feature vector for each pattern:  
'-XYZ' is used to denote 3-axial signals in the X, Y and Z directions.

tBodyAcc-XYZ
tGravityAcc-XYZ
tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag

The set of variables that were estimated from these signals are: 

mean(): Mean value
std(): Standard deviation
mad(): Median absolute deviation 
max(): Largest value in array
min(): Smallest value in array
sma(): Signal magnitude area
energy(): Energy measure. Sum of the squares divided by the number of values. 
iqr(): Interquartile range 
entropy(): Signal entropy
arCoeff(): Autorregresion coefficients with Burg order equal to 4
correlation(): correlation coefficient between two signals
maxInds(): index of the frequency component with largest magnitude
meanFreq(): Weighted average of the frequency components to obtain a mean frequency
skewness(): skewness of the frequency domain signal 
kurtosis(): kurtosis of the frequency domain signal 
bandsEnergy(): Energy of a frequency interval within the 64 bins of the FFT of each window.
angle(): Angle between to vectors.

Additional vectors obtained by averaging the signals in a signal window sample. These are used on the angle() variable:

gravityMean
tBodyAccMean
tBodyAccJerkMean
tBodyGyroMean
tBodyGyroJerkMean

The complete list of variables of each feature vector is available in 'features.txt'


