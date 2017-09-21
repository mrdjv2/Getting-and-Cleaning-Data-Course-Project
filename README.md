# Getting-and-Cleaning-Data-Course-Project

There is a tidy data set submitted, named "tidy_data" as well as the code that produces that data set and a codebook, describing the set.

# Code explanations

First we read all the data provided in the zip-archive:
```R 
  #read all the files
  features <- read.csv("features.txt", sep = "", header = FALSE)
  features<-features[2]
  activity_labels <- read.csv("activity_labels.txt", sep = "", header = FALSE)
  activity_labels <- activity_labels[2]
  
  test_set <- read.csv("test/X_test.txt", sep = "", header = FALSE)
  train_set <- read.csv("train/X_train.txt", sep = "", header = FALSE)
  
  test_labels <- read.csv("test/Y_test.txt", sep = "", header = FALSE)
  train_labels <- read.csv("train/Y_train.txt", sep = "", header = FALSE)
  
  test_person <- read.csv("test/subject_test.txt", sep = "", header = FALSE)
  train_person <- read.csv("train/subject_train.txt", sep = "", header = FALSE)
```

After reading the files they will be merged (or combined) using the rbind-function
```R
  #merge test and train
  merged_test_train_set<-rbind(test_set, train_set)
  merged_test_train_labels<-rbind(test_labels, train_labels)
  merged_test_train_person<-rbind(test_person, train_person)      
```   
To give descriptive names to the columns and extract (as requested) only those columns with a std or mean in them:

```R
    #add names (features) to the set
  names(merged_test_train_set) <- features[ ,1]
  
  #extract only those columns with "std" or "mean" in it
  merged_set_std_mean<-merged_test_train_set[,grep("mean|std" ,colnames(merged_test_train_set), ignore.case = TRUE)]
  
  #add names to the activities
  activities<- merge(merged_test_train_labels, activity_labels, by.merged_test_train_labels = "V1", by.activity_labels = "V1")[2]
```

Merging all the different sets to one big file:

```R
  #merge all data to a big set
  merged_final <- cbind(merged_test_train_person, activities, merged_set_std_mean)
  names(merged_final)[1:2] <- c("PersonID", "Activities")
```
and finally finding the averages per PersonID and activity by applying the mean-function to the groups we just created:

```R
   tidy_data<-group_by(merged_final, PersonID, Activities)
  tidy_data<-summarise_all(tidy_data, mean)
```            

