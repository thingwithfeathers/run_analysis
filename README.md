X_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/X_test.txt", stringsAsFactors=FALSE)
X_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/X_train.txt", stringsAsFactors=FALSE)
subject_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/subject_test.txt", stringsAsFactors=FALSE)
subject_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/subject_train.txt")
features<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/features.txt")
y_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/y_train.txt")
y_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/y_test.txt", stringsAsFactors=FALSE)
activity_labels<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/activity_labels.txt")
colnames(subject_train)<-c("subject")
colnames(subject_test)<-c("subject")
colnames(y_train)<-c("activity")
colnames(y_test)<-c("activity")
features<-features$V2
features<-as.character(features)
features<-gsub("BodyBody", "Body", features)
tolower(names)(features)
activity_labels<-sub("_", "", activity_labels$V2)
activity_labels<-tolower(activity_labels)
activitycolumntest<-activity_labels[y_test$activity]
activitycolumntrain<-activity_labels[y_train$activity]
activitycolumntest<-as.data.frame(activitycolumntest)
activitycolumntrain<-as.data.frame(activitycolumntrain)
colnames(activitycolumntest)<-c("activity")
colnames(activitycolumntrain)<-c("activity")
colnames(X_test)<-features
colnames(X_train)<-features
train<-cbind(subject_train, activitycolumntrain, X_train)
test<-cbind(subject_test, activitycolumntest, X_test)
dataframe<-rbind(train,test)
mean<-dataframe[grepl("[Mm]ean", names(dataframe))]
std<-dataframe[grepl("[Ss]td", names(dataframe))]
install.packages("plyr")
library(plyr)
dfList<-list(mean,std)
nearfinal<-join_all(dfList)
subjactiv<-dataframe[,1:2]
final<-cbind(subjactiv, nearfinal)

##creating a new dataframe to answer question 5. 

meltedData<-melt(final, id.vars = c("subject", "activity"))
melted<-transform(meltedData, subject = factor(subject))
melted$variable<-as.numeric(melted$variable)
aggregate(value~activity+subject, data=melted, FUN="mean", narm=TRUE)

