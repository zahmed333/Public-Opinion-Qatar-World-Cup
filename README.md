# Public-Opinion-Qatar-World-Cup
## Authors

[**_Josh Kalenga_**](https://www.linkedin.com/in/joshua-kalenga-99b3b2187/) [**_Zulnorain Ahmed_**](https://www.linkedin.com/in/zul-ahmed/) [**_Ben Eibl_**](https://www.linkedin.com/in/joshua-kalenga-99b3b2187/)

This is from the Blog post on Medium: [**_Click Here!_**](https://medium.com/@zahmed333/measuring-public-opinion-of-qatar-world-cup-through-sentiment-analysis-53937b950715)
![](https://miro.medium.com/max/1400/1*exRGcU_8B950kQnhm6WbPg.png)

[Workers walk toward the construction site of Lusail stadium, which was built for the 2022 men’s soccer World Cup, during a stadium tour in Doha, Qatar, on Dec. 20, 2019](https://www.nbcnews.com/news/world/qatar-world-cup-exploited-migrant-workers-human-rights-rcna2889)

## Introduction

## Background

Announced in 2010 was the 2022 FIFA World Cup’s location which was in Qatar. Since then, numerous news outlets have reported human rights abuse sentiments towards Qatar. Some of these reports include visas and passports confiscated and wages being withheld from workers so they can continue working on the massive stadium. Since the bid announcement of the World Cup location, 6,500 migrant workers from South Asia have died, but Qatar has told people that those people were working on projects unrelated to the stadium. It is confirmed that between the 2014 to 2020 construction, 37 of the laborers working on the stadium passed away. Since then, Qatar has made changes to its worker protections stopping them to work in extreme heat environments. This report brought about a lot of discussions of the ethics of the World Cup and FIFA which is why we’re choosing to measure public opinion of the World Cup.

## The Big Question

Given all this information, what is it that we hope to answer with gathering public opinions?  
**How have sentiments expressed towards the 2022 Qatar World Cup changed over time (since it was announced to present day?)**

We will attempt to answer this question and show our step-by-step process behind it. We will measure public opinion of the Qatar World Cup through Sentiment Analysis, Word Occurrence, and Feature Importance.

## Data Gathering

## Data Source and Reasoning:

What we ended up choosing to use as a source for measuring public opinion was Reddit. The reasons were that it was an open-source tool that had a readily available API to leverage and was easier to utilize compared to platforms like Twitter. To add on, Reddit was created in 2005 and popularized around 2010. That date is perfect because that year was also when the World Cup date was announced, which meant it would have data ready to scrape and analyze. Furthermore, we chose to scrape all of Reddit to make sure we didn’t have any held biases from certain subreddits, and to get as much data as possible for each period.

Although the Reddit API allows us to search through posts, for our purposes we chose to use Pushshift.io API because it allowed us to search deeply within Reddit and within a certain time frame. Furthermore, Pushshift allows us to be very specific with the parameters we want to set.

## How?

Since we knew what data we wanted, we simply plugged those facts into the API. We knew that we wanted to search all of Reddit with the term: “Qatar World Cup.” We did this for every period we wanted which was the top 100 posts every year from 2010 to the present day.

```
start_time = int(start_date.timestamp())end_time = int(end_date.timestamp())search_term = "world cup"url_test = f"https://api.pushshift.io/reddit/submission/search/?after={start_time}&before={end_time}&q={search_term}&sort_type=score&sort=desc&size=1000"data = rq.get(url_test).json()print(len(data['data']))
```

This code utilizes the pushshift.io API to retrieve the world cup sentiment between those years. After that, we convert the JSON return from the API to create a list of strings that we can read for ourselves. The start date would be the start of the year and then the end of each year. Since we are also doing a monthly report, for each year, the start date is the start of the month to the end of the month for all 12 months of the 12 years.

## Sentiment Analysis

## API Reasoning

For conducting sentiment analysis we chose to go with the **vaderSentiment** API. We had a multitude of reasons to choose this:  
1\. It is an API that we have all used before for our Big Data class.  
2\. Vader Sentiment Analysis is lexicon and rule-based.  
3\. It is specifically tuned for social media which makes it very useful in our case.  
4\. Most importantly, it is open source.

vaderSentiment returns a score between -1 to 1. A score closer to -1 means that a post has a negative sentiment in regards to the topic. A score closer to 1 means that a post has a positive sentiment in regards to the topic.

## How?

We take the list of strings we received from the data gathering and input each of the postings into vaderSentiment. Then, we take every score and get a weighted average which is one score that is displayed for the year. It is important to note that these averages are weighted. Furthermore, we also include a standard deviation between the sentiment scores we get. This weight is determined by the score of upvotes that account for how many people agree with the posts made with that certain sentiment. This weight is important because it accounts for people not reposting certain topics. Therefore, we get a more accurate sentiment between our periods.

```
list_avg_weighted_comp_sent = []list_sd_weighted_comp_sent = []list_num_posts = []list_year_name = []avg_weighted_comp_sent = (df["sentiment_score_compound"] * df["score"]).mean()sd_weighted_comp_sent = (df["sentiment_score_compound"] * df["score"]).std()        um_posts = len(df)        list_avg_weighted_comp_sent.append(avg_weighted_comp_sent)list_sd_weighted_comp_sent.append(sd_weighted_comp_sent)       list_num_posts.append(num_posts)list_year_name.append(f.replace(".csv", "").split("/")[3])
```

This process is repeated for every year to get the yearly/monthly report for the Sentiment measured.

## Word Occurrence

This was very simple to calculate because we counted the amount of posts that came up for each of the terms. Furthermore, we got the percent of those by dividing with total posts, which allowed us to get that percentage that we needed.

## Random Forrest Model

Random forest is a type of supervised learning algorithm that is used for classification and regression tasks. It is an ensemble learning method that creates multiple decision trees and combines their outputs to make a final prediction.

The key feature of random forest is that it randomly selects a subset of features from the available data to build each decision tree. This helps to reduce the overfitting of individual trees and improve the overall accuracy of the model.

## Feature Importance

This is calculated as the decrease in node impurity weighted by the chances of reaching that certain node. This means that a scale closer to 1 is more closely related.

The following is the logic we used to calculate the feature importance:

Input:  
TFIDF vector representing each post

Variable to Predict:  
“Virality”: sum of post ‘score’ and number of comments  
Split into two categories:  
\- Not viral (score < 2)  
\- Viral (score > 50)

Output:  
Whether a post belongs to category 0 (not viral) or category 1 (viral)

## Confusion matrix

We calculated the accuracy of how well are the models are predicting.  
Specifically it is determined from models predicting score+num of comments.

Even if the accuracies are not super high, then there are other factors like year, time, and context.

![](https://miro.medium.com/max/1400/1*9eqsGTnr1qno-Mg0wEWMDQ.png)

Russia

![](https://miro.medium.com/max/1364/1*95LwRWKktmtnEmVPzGImzA.png)

Qatar

## Results

![](https://miro.medium.com/max/1400/1*xr521mPHkyakfgL4JleE5w.png)

Word Occurrence Graph

This is a word occurrence graph for the legend of words listed at the top left of the graph.

![](https://miro.medium.com/max/1400/1*kPvx8OnxcJlb_j2iR4snVw.png)

Sentiment Analysis

This graph was scaled on a MinMax scaling between 0–1 for the sentiment scores of the search term “Qatar World Cup”.

```
 normalized_score = (df["score"]-df["score"].min())/(df["score"].max()-df["score"].min())
```

![](https://miro.medium.com/max/1400/1*9g8yDufbRGJuoOiMN8d8fw.png)

Feature Importance based on the Random Forrest Model.

![](https://miro.medium.com/max/1400/1*ZA2DMvR5B_GdP9mYPBDPmA.png)

Confusion Matrix from Random Forrest model for **Russia** (0.74 Model Accuracy)

![](https://miro.medium.com/max/1400/1*9AyVwTYBsrOepdO9vGd8tA.png)

Confusion Matrix from Random Forrest model for **Qatar** (0.69 Model Accuracy)

## Insights

## Word Occurrence

This graph shows us a lot about the timeline of events and how much these events were discussed. One general trend that we can observe is that the word corruption is on average the lowest occurring word. Corruption is a strong and niche word to use for what is happening in Qatar, therefore, it would probably have the least mentions as it is not a direct word for what is happening. It is also important to note that there are still mentions of this, just very low amounts. Whereas, soccer is the opposite which has the most occurrences. The reasons for this could be that the topic is still linked to soccer. Furthermore, Reddit appeals to a majorly english speaking audience which means it would focus on soccer rather than a term like football.

World Cup 2018 awarded to Russia and 2022 to Qatar:  
This event doesn’t have much to say because it is just the beginning. However, the fact that scandal is up there shows the doubts made by the public about the Qatar bid for the World Cup.

2013 Guardian Report: _Revealed: Qatar’s World Cup ‘slaves’_:  
With this report, here is where we see the birth of many different terms. This was the first report that highlights the abuse and exploitation of these workers, including unsafe working environments, lack of proper housing, and withholding of wages. The report calls for action to be taken to improve the situation for these workers and ensure they are treated fairly. With this in mind, it seems that these terms are taking about 4% of total occurrence.

2014/2015: First reports on Migrant worker deaths in the Guardian and Washington Post:  
This was the point where all the other occurrences took flight and had their own percentages. Maybe at this point was where a lot of discussions started happening and that’s where these word occurrences took off.

2016: Guardian: Legal action filed against Fifa over treatment of Qatar migrant workers:  
This is definitely an interesting point to analyze because all the word occurrences are at an all-time high. The reasoning behind this could be that it shows that something serious may come out about the migrant worker deaths that had previously reported up to this points

2018: World Cup in Russia:  
It seems that this year is the year that people don’t care as much about the 2022 World Cup because of the World Cup that happened in 2018. This World Cup probably brought about general attention to the other side. Therefore, the 2018 World Cup took attention away from the human rights issues that were happening.

2021: Guardian Report about 6,000 dead workers since 2010:  
Here is where we see a lot more occurrences of our tracked terms again. This can be because it marks another very important article that exposes statistics for death which brought about a lot of discussion. This is the time that we learned about what was happening as well.

2022: World Cup in Qatar:  
The results are a bit surprising because we expected mentions to be high since the World Cup is right now. However, the report shows that the occurrence was very low.The main reason that we think that the occurrence is so low is because of the fact that World Cup is still ongoing.

## Sentiment Analysis

There is a large increase in 2012 and we don’t really know the reasoning behind that. It could be another related to soccer in general. However, right after that small increase there is an extremely sharp decrease around 2015 which was the first reports of the deaths for migrant workers in Qatar. From there, there isn’t much except the stabilization of the sentiment towards neutral which can come from the other World Cups occurring and excitement about the current one.

The orange line is a representation of Russia and it show that there is a fairly neutral sentiment across the board. The one small increase that we see is near the 2018 World Cup where it goes slightly up.

## Feature Importance

Within both feature importance graphs we can see that the own country has the strongest importance which we can use as a control for the graph. On Russia’s graph we can see that there’s not much mention other than the logistics and technicality of the graph. However, it is an entirely different story for Qatar: terms like ‘migrant’ or ‘die’ hold a medium level of importance over the words which is interesting since that means we can chalk up some correlation between the two topics.

## Confusion Matrix

It shows that our models for random forrest were very accurate considering that there are a random assortment of things that can affect the feature importance since it is on Reddit and it is such a broad topic. It shows that the performance of our algorithm is well for both of our topics of Russia and Qatar.

## Improvements

Maybe some improvements we could’ve added would be making a live tracker for the Qatar World Cup. It would be really interesting to see what the sentiment could be hour-by-hour or day-by-day. However, given the time constraint of this project, we could not do it. We would have to figure out some important logistics like hosting, and making sure it works all the time, which would be too much.

Another thing that we could’ve added is adding to the sentiment analysis by using a toxicity model. Toxicity would be a good way to measure online sentiment because it is mostly expressed on social media platforms like Twitter and Reddit. However, we had the time constraint of the project blocking this from happening. Furthermore, a lot of toxicity models are very sensitive to curse words which can immediately turn a sentence straight to -1 for the analysis even when they don’t mean it in a toxic manner.

## Future Implications

This program that we have made is great for getting a general public opinion of almost anything on reddit. This is because all you would have to change is the search term and the APIs still work the same. A good example I can think about is getting what people may think about a certain companies. However, it is also important to note that there may be biases when posting on the internet. They are much more likely to report something negative than positive because it is a form of letting out emotions.

## Conclusion

Despite our improvements that we could’ve made, this is still a solid project that we have created. You can take almost any search term, and get in-depth results about the public opnion of it. There can be many use cases of this. Whether someone wants to know the sentiment for a brand they’re about to invest in. This can also be an important tool for researching topics in general.

Our sentiment analysis of the 2022 FIFA Qatar World Cup shows the majority online comments reflect excitement and anticipation for the tournament right now through the occurrence of words. However, there are also a significant number of negative sentiments, mostly centered around the issue of human rights and the treatment of migrant workers in Qatar discussed in this post. Overall, it seems that the World Cup is generating a mixed response among the public.
