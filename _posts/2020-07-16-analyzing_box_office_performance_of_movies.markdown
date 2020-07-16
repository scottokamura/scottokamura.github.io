---
layout: post
title:      "Analyzing Box Office Performance of Movies"
date:       2020-07-16 22:56:28 +0000
permalink:  analyzing_box_office_performance_of_movies
---


### Journey through Module 1

> Microsoft sees all the big companies creating original video content, and they want to get in on the fun. They have decided to create a new movie studio, but the problem is they don’t know anything about creating movies. They have hired you to help them better understand the movie industry. Your team is charged with doing data analysis and creating a presentation that explores what type of films are currently doing the best at the box office. You must then translate those findings into actionable insights that the CEO can use when deciding what type of films they should be creating.

The first major project in this data science course was to find what type of films are currently doing well at the box office and to present the findings to a non-technical audience. In order to complete this task, we were given data sets from multiple sources including Rotten Tomatoes and IMDb that contained information on thousands of movies like budgets and gross international sales.

This project was the first time I was introduced to an open-ended data analysis problem. My recommendations for Microsoft would be completely my own, based off of the data I analyzed from the different databases. Right from the beginning, I struggled to find a starting point. It felt as if I was given no practice or preparation for a project of this scope. It took me almost a whole day to finally start writing code. My struggle to start was completely in my own head and I was overthinking everything. Once I started importing packages and saving different dataframes, I felt a lot of my insecurities and unease fade away. I still wasn't 100% sure what approach I was going to take with my presentation, but I knew which relationships and queries I was interested in investigating and I knew that I was taught how to complete all the necessary steps.

After taking a look at all the different sources of data we were given, I was able to quickly piece together the steps I would need to take in order to analyze the information that I was interested in. I know that in order to be a "successful" movie, it needs to sell a lot of tickets domestically and internationally. It also needs to have actually made a profit. I began combining columns from their respective dataframes using sqlite3. I chose this method of joining tablulated data because during the Python-SQL lessons, I found that the queries needed for sqlite3 made more logical sense to me compared to the lines of code needed in Python to do the same task. 

As I was joining together the columns of interest, I kept finding more things that I wanted to see and compare. At first, I was only interested in the amount of money each movie made. Once I had those columns, I wanted to see who directed each of those movies. If a particular director showed up multiple times in a list of the most successful movies, that director must be doing something right. I also wanted to see who the writers of those movies were and make the same comparison as I did with the directors. With this information, I was thinking of possibly analyzing if there were any director/writer duos that did particularly well as this would have been an incredibly useful statistic for a new movie company looking to hire their first director/writer pair.

Joining the directors and writers to the main dataframe that I was working with proved to be a bit more difficult of a task than I had originally planned for. All of the cast and crew credits were entered using an alphanumeric system rather than each person's full name. So in order to identify each director and writer for the movie I was interested in analyzing, I needed to grab the name that was associated with a certain alphanumeric code. This became increasingly difficult when I saw that most of the movies had multiple people credited as a director or writer. I initially planned on interating through each movie and the lists of writers/directors and matching it to their respective names. After a few failed attempts, I realized that the most important name for the crew, just like the actors, is the top billed one as they are the lead/head of that position. By stripping away all of the other alphanumeric codes besides the first one in each list, the task became significantly simpler but still provided meaningful and valuable data on profitable crew members.

As I was organizing the dataframe by the most profitable movies worldwide, I noticed that some of the movies were very old and wouldn't accurately represent the types of movies that are successful in recent times. I decided to filter the list by movies that were released in 2010 or later in order to get accurate data from the last 10 years. I thought 10 years would be a good time period to investigate as the popular culture scene began favoring superhero movies, remakes/reboots, and sequels of established franchises. I sorted my lists by worldwide gross sales at first, thinking that would be the best measure of success. However, I quickly realized that I would need more information than what was given to me directly from the data. I needed to calculate important benchmarks of success such as profit and return on investment, a characteristic I believe to be the most important for a new movie studio. 

Before even trying to calculate the values of profit and return on investment, I needed to change the type of data that the dollar values were input as. Worldwide gross, domestic gross, and production budget were all entered as objects because of the dollar sign as well as the commas. Stripping the signs away was necessary in order to convert the objects into integers. This was done using:
```
dfbudgets['worldwide_gross'] = dfbudgets['worldwide_gross'].str.replace('$','') #stripping symbols and commas from 
dfbudgets['worldwide_gross'] = dfbudgets['worldwide_gross'].str.replace(',','') #objects to change into integers
dfbudgets['worldwide_gross'] = dfbudgets['worldwide_gross'].str.strip()
dfbudgets['worldwide_gross'] = dfbudgets['worldwide_gross'].astype(int)
```
My first attempt at this worked but was a little long. My second and third attempts for domestic gross and production budget was done simply using:
```
dfbudgets['domestic_gross'] = dfbudgets['domestic_gross'].str.replace('$','').str.replace(',','').astype(int)
```
The code is exactly the same but using a lot less typing and lines. With the numbers now entered as int64 data, I could begin using mathematical operations on them to calculate various values.

After I had the worldwide gross, net profit, and return on investment values calculated, I started creating plots with matplotlib and seaborn to visualize the data. My very first plot took some time and effort to create, but eventually did not make it to the final presentation. Making this plot look nice and presentable was a fun challenge because I was trying to emulate similar plots that I've seen on the internet. 

```
fig, ax = plt.subplots(figsize=(20,14))

ax.barh(df['primary_title'], df['domestic_gross'],label='Domestic Gross')
ax.barh(df['primary_title'],df['worldwide_gross']-df['domestic_gross'],label='Worldwide Gross',left=df['domestic_gross'])
ax.set_title('Total Gross Sales of Top 20 Movies Since 2010', fontdict={'fontsize':20})
plt.xlabel(xlabel='Gross Ticket Sales in Billions (USD)',fontsize='20')
plt.ylabel(ylabel='Movie Title',fontdict={'size':20})
ax.tick_params(axis="y", labelsize=16)
plt.gca().invert_yaxis()
ax.legend(prop={'size':20})
for i, v in enumerate(df['worldwide_gross']):
    ax.text(v - 300000000, i + .25, str(f'${v:,}'), color='mediumblue', fontsize=22)
```

This was the first of MANY bar plots to come. After going through this process once, I decided that I would instead use seaborn visualization techniques since it took less work to make similar plots as matplotlib. 

```
plt.figure(figsize=(15,10))
profit_plot = sns.barplot('profit','primary_title',data=df50,orient='h')
profit_plot.set_xlabel('Net Profit in Billions (USD)')
profit_plot.set_ylabel('Movie')
profit_plot.set_title('Net Profit of Top 50 Worldwide Grossing Movies');
```
This plot showed the net profit of the top 50 worldwide grossing movies, looked a lot more colorful and appealing to the audience, and took a lot less work to code. I know there are ways to shorten this even more but at the moment while I was completing this notebook, this is the general pattern I followed for each bar plot.

Towards the latter half of my notebook, I realized that all I had was bar plots. I understood that they are one of the most common ways to visualize data and would keep things simple for a non-technical audience, but I wanted to incorporate various plots to keep the presentation a little more interesting. My first attempt at this was to make a word cloud using WordCloud. I wanted to see what sorts of descriptive words that critics were using to describe good movies compared the words used to describe bad movies.
```
from wordcloud import WordCloud,STOPWORDS
```
I separated all of the Rotten Tomatoes reviews in the csv file by rotten and fresh while also dropping any review that wasn't uploaded by a "top critic". This was to avoid having any random internet troll's review from being included in my word cloud. 

__It didn't even matter in the end.__

The initial word cloud that was generated from the fresh reviews contained an abundance of common words and abbreviations that needed to be dropped from the count.

```
fresh_string = fresh_string.replace('film','').replace('nan','').replace('the','').replace('film','')
fresh_string = fresh_string.replace('The','').replace('movie','').replace('one','').replace('This','').replace(
    'of','').replace('is','').replace('to','').replace('in','')
fresh_string = fresh_string.replace('th','').replace('sry','').replace('ir','').replace('ha','').replace(
    'doe','').replace('way','').replace('wi','').replace('cracter','character').replace('wt','')
```
Even after all of this cleaning, I was still left with "make", "character", and "performance" as the most common words. This would not add any value to my presentation since they don't help to describe the movies very well. I ran into the same issue with the rotten reviews with "character", "make", and "comedy" as the largest words in the cloud. If I were to do this again, I would need to subtract out the words mentioned in one set of reviews from the other. Another method would be to figure out how to get the program to look for only adjectives.

The other visualization choice I made was to use scatter plots to illustrate the trends in movie ticket sales over the last 10 years. This would strengthen my argument that it would be a good idea to enter the movie industry or not. Depending on which chart you looked at, you would think it was a great idea or horrible. Due to the current coronavirus pandemic, the movie industry isn't looking all too well. However, before 2019, there was a steady increase in movie ticket sales from 2010 to 2018, which would be a very promising statistic for a new movie studio.

I also used a scatter plot to illustrate the relationship between the amount of money spent to produce a movie and the return on investment on that film. The data points were then colored based on how much profit they made. This plot would show the budget range that would have the highest probability of achieving a 400% or better return on investment. This would lead to my recommendation to the studio to spend between $100 - 200 million on production costs. Movies in that range had a 15% chance to have an ROI of 400+% while movies under $100 million only had a 10% chance.

```
plt.figure(figsize=(16,9))
sns.set_style('whitegrid')
g = sns.scatterplot('production_budget','roi',data=df_roi_genre,hue='profit',palette='plasma_r')
xlabels = ['{}'.format(x) for x in g.get_xticks()/1000000]
g.set_xticklabels(xlabels)
g.legend_.remove()
g.set(xlabel='Production Budget in Millions (USD)',ylabel='Return on Investment (%)',
      title='Return on Investment with Increasing Production Budget');
```


My final presentation came in just under 8 minutes. I felt really good about what I was able to present, even though not all of my visualizations made it to the final product. I learned a lot about how to clean datasets and make visual plots that would simplify a lot of numbers and figures into easy to digest plots. Going through the entire process myself was challenging yet incredibly fun to do. I hope I can look back on this after the last module of the program and laugh at how poorly this entire thing was done. That would mean I must have learned more and improved my data science skills to the point where I can laugh at myself.

