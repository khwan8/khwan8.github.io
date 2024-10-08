---
title: "MA331-Coursework"
excerpt: "Short description of portfolio item number 1<br/><img src='/images/500x300.png'>"
collection: portfolio
---

This is an item in your portfolio. It can be have images or nice text. If you name the file .md, it will be parsed as markdown. If you name the file .html, it will be parsed as HTML. 
<!-- ---
title: "Portfolio item number 1" 
#"MA331-Coursework"
subtitle: "Text analytics of the TED talks by Tim Berners-Lee and Brian Cox"
author: "2110866-Khwanchanok-Chumkhun"
output: html_document
--- -->

```{r setup, include=FALSE}
### Don't delete this setup code chunk from your file
knitr::opts_chunk$set(echo = FALSE,error=FALSE, warning=FALSE, message=FALSE,layout="l-body-outset",fig.align = 'center')   ## DON'T ALTER THIS: this is to prevent printing the code in your "html" file.

# Extend the list below to load all the packages required for your analyses here:
#============================================
===================================
library(knitr)
library(dsEssex)
library(tidyverse)
library(tidytext)
library(ggplot2)
library(ggrepel)
library(dplyr)
#=========================
data(ted_talks) # load the 'ted_talks' data
MyData <- ted_talks %>% filter(speaker %in% c("Tim Berners-Lee", "Brian Cox")) # filter only assigned speakers and then save to new variable named MyData
#Prepare data by transforming it into tidy type, remove stopwords and save in variable named TidyData
TidyData <- MyData %>% #set MyData as an input of next function
  unnest_tokens(word, text) %>%  #splitting text from MyData into individual word (Tokenisation)
  anti_join(get_stopwords())  #remove the list of irrelevant words (remove stopwords)
```

## Introduction
Text analysis is the methodology to investigate the insight emotions and opinions expressed in texts.
<br />
This report will utilize text analysis methodology to examine word frequencies and sentiment analyses in order to understand the meaning of 5 TED talk transcripts of 2 TED speakers, Brian Cox and Tim Berners-Lee.
<br />
In March 2008, Physicist Brian Cox introduced his work on  the biggest scientific experiment, the Large Hadron Collider (LHC) at CERN. After that, in February 2009, he gave a short talk to update the progress of CERN LHC project and the future for the largest science experiment.
<br />
The 20th anniversary of the World Wide Web in February 2009, Tim Berners-Lee, the World Wide Web inventor, gave a talk at TED for his new project, "Linked Data" and requested for the raw data now. A year later, in February 2010 at TED University, he presented some interesting result of his Linked Data project from the raw data he requested in his first talk. 
<br />
<br />

## Methods
Since the Tidy data approach is one of the effective approach to manipulate text data, then it will be used to word with text to facilitate or to prepare the data before processing word frequencies and sentiment analyses. 
First, the tokenization is applied to separate the talk transcript into a token which is a single word for this study. 
After getting the data arranged in the tidy text format, to ensure that the study will focus only to the meaningful data, the list of irrelevant data (stop-words) will be remove from the data set. 
Then others 'tidy' tools will be used to manipulate the data set to analyze the insight meaning of it:
<br />
I) The words which are used the most frequent by each speaker will be clarified, and subsequently it will be transformed into wide format and the scatter plot will be used to visualize the comparison of the word frequencies for the 2 speakers (see Figure1 in Results section). 
<br />
II) The Sentiment Analysis will be done using a lexicon based method. The implication or sentiment will be assigned to each individual word in tidy format using existing sentiment lexicons, then summarize the sentiment orientation of each word to be the representative sentiment of the whole text.
<br />
(1) First, this report will present the comparison of overall sentiment of the 2 speakers to show their emotions (sadness, surprise, positive, negative,...) compare to each other by using the lexicon called 'nrc'. The result will be displayed in term of bar graph of log-odds ratio as shown in Figure2 in Results section, in order to present which speaker is less or more associate in which sentiment. 
 <br />
(2) Then the study will investigate a bit more detail of each talk, the 'bing' lexicon will be used to clarify the positive or negative sentiment and the result will be presented as a table of percentage of positive and negative sentiment in overall of each talk (Table1).
 <br />
(3) Furthermore, again with 'bing' lexicon, the more detail analysis will be conducted. Instead of examine the sentiment of whole talk, each talk will be devised into fractions and studied sentiment of each fraction to understand how the speaker emotion changes toward more positive or negative sentiment over the trajectory of each talk. This study output will be demonstrated as histogram chart with the trajectory of talk in x axis and sentiment in y axis (See Results portion, Figure3). 
<br />
<br /> 

## Results
#### I. Word frequencies
<br />
This segment presents the comparison of the word frequencies for the 2 speakers which the result shows as following.
```{r}

# Count the word frequencies, filter only words which are used more than 15 times by 2 speakers, transform data from tidy type to wide type and save in variable named TidyCount
TidyCount<- TidyData%>% #set TidyData as an input of next function
  count(speaker, word) %>% #count words by speaker
  group_by(word) %>%  #group data by word
  filter(sum(n) > 15) %>%   #filter data that the sum of words using by 2 speakers over than 15
  ungroup() %>% # ungroup data
  pivot_wider(names_from = "speaker", values_from = "n",values_fill = 0) #Transform data back to wide type with the rows show the words and the columns will be speaker and filled values are the frequency of words used by each speaker and fill 0 in case data are missing.

# use TidyCount data to create scatter plot and save in variable named gg1
gg1 <-  TidyCount %>%  #set TidyCount as an input of next function
  ggplot(aes(`Tim Berners-Lee`, `Brian Cox`)) + #create initial plot layer and set data in `Tim Berners-Lee` column to be x axis and `Brian Cox` to be y axis.
  geom_abline(color = "#fc8d62", size = 1, alpha = 1, lty = 1) + #add a plot layer of a 45 degree straight line to be reference line
  theme_bw()+ #change plot theme to be black and white theme
  geom_point(color = "#66c2a5",alpha = 1, size = 2.5) +    # add a new plot layer with point and set format of it
  geom_text_repel(aes(label = word), max.overlaps = 30) + # use the special ggrepel geom for nicer text plotting
  labs(caption = "Figure1: Comparing the frequency of words used by Brian Cox and Tim Berners-Lee") + # add caption to be figure name
  theme(plot.caption = element_text(size = 11, face = "italic",hjust = 0)) # set format of the caption
gg1 #display gg1 plot

```

The result,in Figure1, shows that there are words which are extremely frequent used by Tim Berners-Lee but not by Brian Cox. These words can lead the understanding of overall meaning of the talks. As in this report, we can know that Tim Berners-Lee's speeches must be something related to data, while Brian Cox might talk about the universe.
Focusing at the plot, it is asymmetric, there are words from one side are more far away from the reference line than another side, because the frequencies of some word using by Tim Berners-Lee are immensely high comparing to the high frequent words of Brian Cox. There are 2 main reasons for this, the talks of Tim are longer than Brian. And the main purpose of Tim's talks is to request data for the web reforming so this is the reason why he keep repeating the words 'data' and 'web' many times.
But there are some words which are near by the reference line, for instance, 'way', 'one', 'back',... are used about equal frequencies by Brian Cox and Tim Berners-Lee. These are the words that are common and general use and cannot lead to story of the talks.
<br />
<br />   

####  II. Sentiment analysis
<br />
(1) For sentiment analysis portion, the study starts by comparing of overall sentiment of the 2 speakers to show their emotions compare to each other and the result is as below.
```{r}

#nrc

# Prepare data by assign the sentiment into each word and save as variable named Sentiment_nrc
Sentiment_nrc <- TidyData %>% #set TidyData as an input of next function
  group_by(speaker) %>% #group data by speaker
  ungroup() %>% # ungroup data
  inner_join(get_sentiments("nrc")) # add new column and fill with sentiment in "nrc" lexicon (remain all row which data exist in both current data set and in "nrc" lexicon)

# Sentiment_nrc
nrc_count <- Sentiment_nrc %>% #set Sentiment_nrc as an input of next function
  count(speaker, sentiment) %>% #count sentiment by speaker
  pivot_wider(names_from = speaker, values_from = n, values_fill = 0) #Transform data back to wide type with the rows show the sentiment and the columns will be speaker and filled values are the frequency of sentiment used by each speaker and fill 0 in case data are missing.


#nrc_count

nrc_count %>%
  mutate(OR = dsEssex::compute_OR(`Tim Berners-Lee`, `Brian Cox`, correction = FALSE), log_OR = log(OR), sentiment = reorder(sentiment, log_OR)) %>% #Add new column named OR and fill with odds ratio of sentiment of `Tim Berners-Lee`, `Brian Cox` respectively. And another column named log_OR fill with log odds ratio of sentiment of `Tim Berners-Lee`, `Brian Cox` respectively.
  ggplot(aes(sentiment, log_OR, fill = log_OR < 0)) + #create initial plot layer and set data in sentiment column to be x axis and log_OR to be y axis.
  geom_col(show.legend = FALSE) + #add a plot layer of bar chart
  theme_bw()+ # set plot theme to be black and white
  ylab("Log odds ratio") + #add y axis label 
  coord_flip() + #Flip cartesian coordinates which display y conditional on x, to x conditional on y
  scale_fill_manual(name = "", values = c("#66c2a5", "#fc8d62")) +    #manually fill the colour of the bars
  labs(caption = "Figure2: The association between sentiments of Tim Berners-Lee's talks and Brian Cox's talks")+ # add caption to be figure name
  theme(plot.caption = element_text(size = 11, face = "italic",hjust = 0)) # set format of the caption

```
The aforementioned Figure2 shows the correlation between sentiments of Tim Berners-Lee's talks and Brian Cox's talks. There are 10 different sentiments ('nrc' lexicon) using to analyze each speaker sentiment as shown in the figure.
The positive value of log-odds ratio demonstrates that talk of Tim's talks are more associate in that sentiment more than Brian's talks and the higher the value shows that the sentiment is more strong. In this case, talks of Tim compound more sentiments of extreme disgust,  strong sadness, fear, negative and few trust than Brian's. 
Whereas talks of Brian are more anticipating, angry, positive, very joyful and extremely surprising.
<br />
<br />
(2) Now more detail investigation is done by analyzing positive and negative sentiment in overall of each talk and show in percentage of it as shown in table below.

```{r}

# Prepare data by assign the sentiment into each word and save variable as named bing.sentiment 
bing.sentiment <- TidyData %>% #set bing.sentiment as an input of next function 
  inner_join(get_sentiments("bing")) # add new column and fill with sentiment in "bing" lexicon (remain all row which data exist in both current data set and in "bing" lexicon)

#Count the sentiment by speaker and headline then save as variable named bing.counts
bing.counts <- bing.sentiment %>% #set bing.sentiment as an input of next function
  count(speaker, headline, sentiment) #Count the sentiment by speaker and headline

#Create table with sentiment to visualize the percentage of negative/positive sentiment in each talk
sentiment_table <- bing.counts %>%  #set bing.counts as an input of next function
  group_by(headline) %>% #group by headline
  mutate(Total = sum(n), Percent = round(n*100 / Total,2)) %>% # add new columns with the total number of words in each talk (headline), and percentage of each sentiment
  arrange(desc(speaker,headline)) # re-arrange the order by speaker then by headline
  kable(sentiment_table,caption = "Table1: The comparison of negative and positive sentiment of each talk") #visual the table

```
Table1 shows the number, together with the percentage of negative and positive words using in each talk (headline), and show them ordered by speaker then talks. From the result, it shows that all talks contain more positive words than negative. The  most positive talk is 'The year open data went worldwide'	by Tim Berners-Lee, the talk about his new project, "Linked Data" and he used this occasion to request the cooperate to realize his project (raw data now). So it's understandable why this talk is more positive comparing with others. On the other hand, the talk 'What went wrong at the LHC', which Brian Cox updates the issue that LHC project was facing, so it is also not surprising that this talk is the most negative.
<br />
<br />
(3) Now the study goes more deep, the sentiment (negative or positive) is showed over the trajectory of the story of the talks. And the results are presented in following Figure3.

```{r}

# Add row no. to each word of the talk, assign sentiment and save as sentimenttt
sentimenttt <- TidyData %>% #set TidyData as an input of next function
  group_by(headline) %>% #group by headline
  mutate(line_number = row_number()) %>% #add new column and input row no
  ungroup() %>% #ungroup
  inner_join(get_sentiments("bing")) # add new column and fill with sentiment in "bing" lexicon (remain all row which data exist in both current data set and in "bing" lexicon)

sentimenttt %>% #set sentimenttt as an input of next function
  count(headline, speaker, index = line_number %/% 15 , sentiment) %>% #count sentiment by headline and speaker in every 15 rows
  
  pivot_wider(names_from = sentiment, values_from = n, values_fill = 0) %>%  #Transform data back to wide type

  mutate(sentiment = positive - negative) %>%    # use mutate to compute net sentiment 
  
  
  ggplot(aes(index, sentiment, fill = sentiment)) + #create the initial layer of plot with index value in x-axis and sentiment in y-axis
  # make a bar chart with geom_col()
  geom_col() +
  # make small multiples for each title with facet_wrap() 
    facet_wrap(~speaker+headline, scales = "free_x") +scale_fill_gradient2(low = "red",high = "blue",midpoint = 0) +   
  labs(caption = "Figure3: Sentiment through the narratives of each talk")+
  theme(plot.caption = element_text(size = 11, face = "italic",hjust = 0))

```
The result illustrates how speaker's sentiment (positive or negative) change over period of time of each talk, it also shows how strong is that sentiment. The histogram graph, which is over 0, represents the positive sentiment of that narrative of the talk and the height of the histogram explains the intensity of the sentiment, the more graph is high, the more intense is the sentiment.
<br />
Overall result is the same as the one show in previous table. All talks are significantly more positive than negative.
The distribution of positive and negative sentiment of 1st talk of Brian Cox, 'CERN's supercollider'  disperse
through the talks, while the negative sentiment of his 2nd talk is concentrated in a period of time, it could be the timing that he presented the problem of his work. For 'A Magna Carta for the web' of Tim Berners-Lee, this talk is to warn and persuade to fight for the freedom to access data. This talk shows the 2nd negative sentiment in the previous analysis. Then it is normal that there are several strong negative histogram distributed over the trajectory of the talk. The remain topics of Tim shows the normal spread of negative sentiment through the timing of story.


## Discussion

As conclusion from this study, Tidy data approach is an effective method to work or to manipulate text data, while lexicon based method is also the one of efficient way for the sentiment analysis.
<br />
However, there are some limitations of working with lexicon based method which are :
<br />
(1) The limitation of vocabularies in current existing lexicon which is not cover 100% of words. And some words can direct to several sentiments or emotions. These affect to the accuracy of the analysis.
<br />
(2) Current existing lexicons are not cover all languages, so this method could not be apply in all situations.
<br />
(3) Small section or short text might not be able to get properly sentiment analysis. Especially when the words contains in the text is not match with the word in lexicon.  
<br />
The main challenges of this work are :
<br />
(1) For the person who does not have programming background, it is very challenge to work with the long code and to face and solve when any issue occurs.
<br />
(2) There are several solutions that can be used to visualize the data, the reasonable selection and interpretation of visualization is one of challenge point. The presented results should not be too few to unable to cover all and it should not be too many to make the presentation duplicated.
<br />
(3) One of speaker's talks are very difficult and inapplicable, It not easy to understand whether the analysis result is reasonable or not, since the insight meaning of the talks are not clearly understood.
<br />
<br />
For further investigation, since now the lexicon in R language is not support my native language which is 'Thai'. I would like to explore more on how to create lexicon for my native language and to be able to use it analyzing text in Thai.