  install.packages('ISLR')
  library(ISLR)

#youtubedata2 <- read.csv("C:/Users/Cristian/College/DEngineering/Project/DE Project/USvideos.csv", header = TRUE)#
youtubedata1 <- read.csv(file.choose(), header = TRUE)
#youtubedata3 <- read.delim(file.choose(), header = T)#
#youtubedata4 <- read.table(file.choose(), header = T, sep=",")#

#Viewing Data#
library(tidyverse)
View(youtubedata1)

dim(youtubedata1)
names(youtubedata1)


youtubedata1[10,]
youtubedata1[10,1:4]
youtubedata1[10,"title"]
youtubedata1[head(order(as.numeric(likes), decreasing = TRUE)), c('title','likes')]


#Understanding Data#
str(youtubedata1)
summary(youtubedata1)

#Categorical Variables#
attach(youtubedata1)  
class(comments_disabled)
levels(comments_disabled)
summary(comments_disabled)
table(comments_disabled)
table(comments_disabled)/length(video_id)
table(comments_disabled, ratings_disabled)

#Numerical Variables#
class(likes)
summary(likes)
quantile(likes)
quantile(likes, probs = c(0, .68, .95, .995))
mean(views, trim=0.10)
var(views)
sd(views)
range(views)

cor(views, likes)


###JSON###
install.packages("rjson")
install.packages("jsonlite")

library(rjson)
library(jsonlite)
library(tibble)

USCategory=fromJSON("US_category_id.json",flatten=TRUE)
Categories=as.data.frame(USCategory)

View(Categories)
dim(Categories)
names(Categories)

NewDF=Categories[ ,c("items.id","items.snippet.title")]
View(NewDF)
print(NewDF)

colnames(NewDF) = c('category_id', 'categories')
USA <- merge(NewDF, youtubedata1, by = c("category_id"))
View(USA)

#Visualizing Categorical Data#
attach(USA)
library("dplyr")
CategoryTotal=select(USA,views,categories)
test <- USA %>% group_by(categories) %>% summarize(total_views=sum(as.numeric(views)))
test1=test[(order(-test$total_views)),c("categories","total_views")]
test1 = data.frame(Categories =c(1:10),total_views = c(1:10))
View(test)
C=test1$categories[1:10]
V=test1$total_views[1:10]
barplot(V,names.arg = C,xlab = "Categories",ylab = "Total Views",col = "blue",
        main = "top 10 Categories",border = "red")



#Visualizing Quantitative Data#

stem(views)

plot(views ~ likes)
plot(views/1000000 ~ dislikes, main = 'Youtube Data: Views & Dislikes Scatterplot', xlab='Dislikes', ylab = 'Views in Millions', las= 1, pch = 2, col=4)
abline(lm(views/1000000~dislikes), col = 3)

hist(views)
minviews <- min(views)
maxviews <- max(views)
sdviews <- sd(views)
hist(views/sdviews, main="Probability Density Histogram", xlab = "Standard Deviations", las=1, freq = F)


###Analysis###

#Linear Regression#
modeltesting <- lm(likes~categories+views+comment_count+dislikes, data = USA)
summary(modeltesting)

#Multivariate Regression#
install.packages("dummies")
library("dummies")
USA1=USA[,c(likes,categories,views,comment_count)]
#Create dummy variables for categories#
USA11=dummy.data.frame(USA1)  


