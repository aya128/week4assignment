# week4assignment
library(data.table)
library(dplyr)
featureNames <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/features.txt")
activityLabels<- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/activity_labels.txt", header = FALSE)
subjectTrain <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/subject_train.txt", header = FALSE)
activityTrain <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/y_train.txt", header = FALSE)
featuresTrain <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/X_train.txt", header = FALSE)
subjectTest <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/subject_test.txt", header = FALSE)
activityTest <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/y_test.txt", header = FALSE)
featuresTest <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/X_test.txt", header = FALSE)
subject <- rbind(subjectTrain, subjectTest)
activity <- rbind(activityTrain, activityTest)
features <- rbind(featuresTrain, featuresTest)
colnames(features) <- t(featureNames[2])
colnames(activity) <- "activity"
colnames(subject) <- "Subject"
completeData <- cbind(features,activity,subject)
columnsWithMeanSTD <- grep(".*Mean.*|.*Std.*", names(completeData), ignore.case=TRUE)
requiredColumns <- c(columnsWithMeanSTD, 562, 563)
dim(completeData)
extractedData <- completeData[,requiredColumns]
dim(extractedData)
extractedData$activity <- as.character(extractedData$activity)
for (i in 1:6){
  extractedData$activity[extractedData$activity == i] <- as.character(activityLabels[i,2])
}
extractedData$activity <- as.factor(extractedData$activity)
names(extractedData)
names(extractedData)<-gsub("Acc", "Accelerometer", names(extractedData))
names(extractedData)<-gsub("Gyro", "Gyroscope", names(extractedData))
names(extractedData)<-gsub("BodyBody", "Body", names(extractedData))
names(extractedData)<-gsub("Mag", "Magnitude", names(extractedData))
names(extractedData)<-gsub("^t", "Time", names(extractedData))
names(extractedData)<-gsub("^f", "Frequency", names(extractedData))
names(extractedData)<-gsub("tBody", "TimeBody", names(extractedData))
names(extractedData)<-gsub("-mean()", "Mean", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("-std()", "STD", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("-freq()", "Frequency", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("angle", "Angle", names(extractedData))
names(extractedData)<-gsub("gravity", "Gravity", names(extractedData))
names(extractedData)
extractedData$Subject <- as.factor(extractedData$Subject)
extractedData <- data.table(extractedData)
tidyData <- aggregate(. ~Subject + activity, extractedData, mean)
tidyData <- tidyData[order(tidyData$Subject,tidyData$activity),]
write.table(tidyData, file = "Tidy.txt", row.names = FALSE)


