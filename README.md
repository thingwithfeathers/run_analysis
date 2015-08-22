
##Read in the data downloaded from this URL: https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
##Protectively, use stringsAsFactors=FALSE. This treats the data as something that will need work. Source: 
##http://simplystatistics.org/2015/07/24/stringsasfactors-an-unauthorized-biography/, as per David Hood

X_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/X_test.txt", stringsAsFactors=FALSE)
X_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/X_train.txt", stringsAsFactors=FALSE)
subject_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/subject_test.txt", stringsAsFactors=FALSE)
subject_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/subject_train.txt")
features<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/features.txt")
y_train<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/train/y_train.txt")
y_test<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/test/y_test.txt", stringsAsFactors=FALSE)
activity_labels<-read.table("/Users/humbertsanders/Desktop/Coursera/data/UCIData/activity_labels.txt")

##Assign names to these columns -- two for the training data and two for the test data. These columns will ultimately become 
##the first two columns in the dataframe

colnames(subject_train)<-c("subject")
colnames(subject_test)<-c("subject")
colnames(y_train)<-c("activity")
colnames(y_test)<-c("activity")

####assign to features only the second column of the features object. This is where the names of the 561 measurements are stored. Clean up the data in various ways: Save the features as characters to avoid potential sorting problems. Replace an apparent typo in which the word "body" is mentioned twice. Make the names lowercase to be consistent with R principles. Remove the underlining from the activity names.

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

#Here, we will be creating two dataframes -- one for the training data and one for the test data -- and then binding both together into a single dataframe. The concept is to initially bind the training subject, training activity, and training data together by column; then to bind the test subject, activity, and data together by column, and then to stack those two dataframes on top of each other -- or bind by row -- using rbind. This works because the two dataframes each have the same number of columns so they fit together neatly like two boxes.

train<-cbind(subject_train, activitycolumntrain, X_train)
test<-cbind(subject_test, activitycolumntest, X_test)
dataframe<-rbind(train,test)

we are extracting the data on means and standard deviations and storing the data in two separate dataframes, one for each kind. Because the question was vague about whether to use just some or all of the data, I used everything that references the mean and the standard deviation. In a perfect world, there would be a conversation about which mean and standard deviation measures to use. The grepl() function is used to capture every single reference, and the [] around uppercase and lowercase letters are used in case there is a variation in whether the words are in uppercase or lowercase letters.

mean<-dataframe[grepl("[Mm]ean", names(dataframe))]
std<-dataframe[grepl("[Ss]td", names(dataframe))]

##Here, join the two together through the join package in plyr. I used this instead of merge() protectively, because merge() reorders the data, as per David Hood. Then, add the subject and activity columns, which should match up because join_all() does not seem to reorder data.

install.packages("plyr")
library(plyr)
dfList<-list(mean,std)
nearfinal<-join_all(dfList)
subjactiv<-dataframe[,1:2]
final<-cbind(subjactiv, nearfinal)

##creating a new dataframe to answer question 5. First, you will need to load the reshape2 package in order to be able to "melt" the data, or change it into a narrow format from a wide format. Then use the aggregate() function, which I stumbled across while trying to find a group_by()/summarize() approach. Here is the source: http://www.slideshare.net/jeffreybreen/grouping-summarizing-data-in-r

install.packages("reshape2")
library(reshape2)
meltedData<-melt(final, id.vars = c("subject", "activity"))
melted<-transform(meltedData, subject = factor(subject))
melted$variable<-as.numeric(melted$variable)
aggregate(value~activity+subject, data=melted, FUN="mean", narm=TRUE)
meltedData<-melt(final, id.vars = c("subject", "activity"))
melted<-transform(meltedData, subject = factor(subject))
melted$variable<-as.numeric(melted$variable)
aggregate(value~activity+subject, data=melted, FUN="mean", narm=TRUE)


