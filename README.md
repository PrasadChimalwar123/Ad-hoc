# DataScience
Organizing my data science work.
---
title: "Market Basket Analysis"
author: "Prasad Chimalwar"
date: "5 January 2020"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:



Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.

Let us first understand what are related concepts for Market Basket Analysis

A great implementation is given in python here.


1) Support: It is calculated to check how much popular a given item is. It is measured by the proportion of transactions in which an itemset appears. For example, there are 100 people who bought something from grocery store today, amoung those 100 people, there are 10 people who bought bananas. Hence, the support of people who bought bananas will be (10/100 = 10%).

2) Confidence: It is calculated to check how likely if item X is purchased when item Y is purchased. This is measured by the proportion of transactions with item X, in which item Y also appears. Suppose, there are 10 people who bought apple(out of 100), and from those 10 people, 6 people also bought yogurt. Hence, the confidence of (apple -> yogurt) is: (apple -> yogurt) / apple [i.e. 6/10 = 0.6].

3) Lift: It is calculated to measure how likely item Y is purchased when item X is purchased, while controlling for how popular item Y is. The formula for lift is: (lift = support (X ->Y) / (support(X) * support(Y)).

Lets implement this and get lift for our rules.

```{r}
#Get The library
library(arules)
dataset = read.csv('E:\\Data Analytics\\projects\\Market Basket Analysis\\Groceries data.csv', header = FALSE)
View(dataset)

#Need sperific data format for arules packages, so quickly dropping 1st row and 1st column of this dataset, don't need 1st row and 1st column.

df = dataset[-1,-1] # this is reqired structure

#Lets save this as we need to read this file.
write.table(df,'E:\\Data Analytics\\projects\\Market Basket Analysis\\input_data.csv', row.names = FALSE, col.names = FALSE, sep = ',')

#Get the saved data
df = read.transactions('E:\\Data Analytics\\projects\\Market Basket Analysis\\input_data.csv', sep = ',', rm.duplicates = TRUE)
smry <- summary(df) #just checking the frequency
smry

itemFrequencyPlot(df, topN = 50) #plot top 50 items
```
So Far so good, we shaped our data and did basic eda, can wrangle around more. 
# Major Drawback :- Multiple entry of a items in restricted to one.
So if someone buys Milk, milk, milk, cheese, cheese, oil. , the duplicates will
be removed and basket will have only milk, cheese and oil.

#The goal here is to find associations between items, and not their  association of 
recurrent occurence


Training the Apriori Algorithms

```{r}
rules = apriori(data = df, parameter = list(support = 0.004, confidence = 0.4))
```

```{r}
# Visualising the results
check <- as.data.frame(inspect(sort(rules, by = 'lift')))
#View(check)
inspect(sort(rules, by = 'lift'))
```

