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

You can also embed plots, for example:

```{r training}
library(tidyverse)
#import features names
features <- read.table("data/UCI HAR Dataset/features.txt")
colnames (features) <- c("idfeature","feature") #give columns meaningful names

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
  group_by(idsubject,activity)%>%#group by subject and activity
  summarise_all(mean) %>%#calculate mean for each column
  rename_all(function(x)paste0 ("average_",x)) %>%#add 'average_' as prefix
  rename(idsubject = average_idsubject) %>% #rename backwards idsubject
  rename(activity = average_activity)#rename backwards activity

#write tidy data out
write.table(average_persubj_peractivity,file= "data/tidy_data.txt", row.names = FALSE)

#import data for QC purpose
df <- read.table("data/tidy_data.txt", header = TRUE, check.names = FALSE)
#check names are identical after import
identical(names(df), names(average_persubj_peractivity))
rm(df) #clean up
```

