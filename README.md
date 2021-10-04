 Project-4
 
 library(data.table)
library(dplyr)

##########Reading data from folder###################################

featureNames <- read.table("UCI HAR Dataset/features.txt")
activityLabels <- read.table("UCI HAR Dataset/activity_labels.txt", header = FALSE)

subjectTrain <- read.table("UCI HAR Dataset/train/subject_train.txt", header = FALSE)
activityTrain <- read.table("UCI HAR Dataset/train/y_train.txt", header = FALSE)
featuresTrain <- read.table("UCI HAR Dataset/train/X_train.txt", header = FALSE)

subjectTest <- read.table("UCI HAR Dataset/test/subject_test.txt", header = FALSE)
activityTest <- read.table("UCI HAR Dataset/test/y_test.txt", header = FALSE)
featuresTest <- read.table("UCI HAR Dataset/test/X_test.txt", header = FALSE)

subject <- rbind(subjectTrain, subjectTest)
activity <- rbind(activityTrain, activityTest)
features <- rbind(featuresTrain, featuresTest)

##############Merging data to create one big dataset###################
subject <- rbind(subjectTrain, subjectTest)
activity <- rbind(activityTrain, activityTest)
features <- rbind(featuresTrain, featuresTest)

colnames(features) <- t(featureNames[2])
colnames(activity) <- "Activity"
colnames(subject) <- "Subject"
completeData <- cbind(features,activity,subject)

##############Extraction of measurements (Mean/Median)###################
selectMeanStd <- featureNames[grep("*mean\\(\\)*|*std\\(\\)*", featureNames[ , 2]), ]
completeData <- completeData[ , selectMeanStd[ ,1]]


################Name Change - Descriptive variable names#################
names(completeData)<-gsub("Acc", "Accelerometer", names(completeData))
names(completeData)<-gsub("Gyro", "Gyroscope", names(completeData))
names(completeData)<-gsub("BodyBody", "Body", names(completeData))
names(completeData)<-gsub("Mag", "Magnitude", names(completeData))
names(completeData)<-gsub("^t", "Time", names(completeData))
names(completeData)<-gsub("^f", "Frequency", names(completeData))
names(completeData)<-gsub("tBody", "TimeBody", names(completeData))
names(completeData)<-gsub("-mean()", "Mean", names(completeData), ignore.case = TRUE)
names(completeData)<-gsub("-std()", "STD", names(completeData), ignore.case = TRUE)
names(completeData)<-gsub("-freq()", "Frequency", names(completeData), ignore.case = TRUE)
names(completeData)<-gsub("angle", "Angle", names(completeData))
names(completeData)<-gsub("gravity", "Gravity", names(completeData))

###############Creating another tidydata set################

TidyData <- summarise_all(group_by(completeData), funs(mean))
write.table(TidyData, "./UCI HAR Dataset/SummaryData.txt", row.names = FALSE)

