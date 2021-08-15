# Project: Wrangle WeRateDogs Twitter data 
### Introduction
The dataset that we will be wrangling (and analyzing and visualizing) is the tweet archive of Twitter user [@dog_rates](https://twitter.com/dog_rates), also known as WeRateDogs. WeRateDogs is a Twitter account that rates people's dogs with a humorous comment about the dog. 
here I will talk about the wrangling efforts of this project starting with gathering, then assessing, lastly cleaning the data

### Data Gathering
We gathered the data from different sources the first one which is the easiest is simply downloading the file twitter-archive-enhanced.csv and read it using pandas read_csv function,  this file came from @dog_rates, they downloaded their Twitter archive and sent it to Udacity via email exclusively for students to use in their projects. This archive contains basic tweet data (tweet ID, timestamp, text, etc.) for all 2000+ of their tweets as they stood on August 1, 2017.
The second data source was image predictions file but it was stored in udacity servers so we had to downloaded programmatically using requests library.
The third source was Twitter API, the twitter-archive-enhanced.csv is good but not enough we want the number of retweets and favorites, so we had to use the Twitter API, we used the tweets ids we got from twitter-archive with  get_status function from tweepy to get extra tweet info, we stored each tweet json info in separate line in text file named tweet_json.txt

Data Assessing
We used both visual assessment in Microsoft excel, and programing assessment using pandas functions like df.head(), df.info(), s.value_counts(), and techniques like masking the dataframe, we identified this issues :
Quality
      twitter_archive
1.	Missing tweets urls 
2.	Unneeded columns that dose not represent a variables in `twitter_archive` :(in_reply_to_status_id, in_reply_to_user_id, retweeted_status_id, retweeted_status_user_id, retweeted_status_timestamp)

3.	Missing dog stages 
4.	Not all the tweets from dog_rates 
5.	Some tweets dose not contain photos 
6.	Some of data are retweets not orginal tweets 
7.	Uncorrect values in rating_numerator and rating_denominator colmun 
8.	To much text in source column 
9.	Uncorrect dog names in name column 
10.	Erroneous datatypes (tweet_id, timestamp, source, and dog stages column) 

#### Tidiness
1.	doggo, floofer, pupper, puppo columns in `twitter_archive` should be in one column 
2.	api_data (retweet_count, favorite_count) should be part of the `twitter_archive` table
3.	image_predictions ('jpg_url','p1', 'p1_dog') should be part of the `twitter_archive` table

### Data Cleaning
We will talk about each issue above and how we deal with it, I will start with Tidiness issues.
     Tidiness
1.	doggo, floofer, pupper, puppo columns in `twitter_archive` should be in one column 
We create another column named not_specified for tweets that is not one of four stages, and we used the melt function to combine the columns into dog_stage and value column, then we removed extra rows when dog_stage dose not equal value column, lastly we removed the value column 

2.	Api_data (retweet_count, favorite_count) should be part of the `twitter_archive` table

We performed inner join for twitter_archive and api_data on tweet_id
3.	image_predictions ('jpg_url','p1', 'p1_dog') should be part of the twitter_archive` table
We performed inner join for twitter_archive and image_predictions
on tweet_id

  #### Quality
1.	**Missing tweets urls**
We created a list of tweets id that dose not have url, and we tried to extract the missing urls from tweet_json.txt file we gathered before, but we did not find any thing it might be deleted tweets so we just dropped those tweets

2.	**Unneeded columns that dose not represent a variables in `twitter_archive`**
we just dropped this columns 

3.	**Missing dog stages**
we created a mask of not_specified dogs, and we searched for each stage in the text of none specified dogs and we updated those rows 

4.	**Not all the tweets from dog_rates**
We dropped tweets that is not from @dog_rates

5.	**Some tweets dose not contain photos**
We dropped tweets that dose not have photos

6.	**Some of data are retweets not orginal tweets**
We dropped all records that is retweets

7.	**Uncorrect values in rating_numerator and rating_denominator colmun**
We extracted the rating from all tweets text using regular expression, this we split it into two columns and assign them to rating_numerator and rating_denominator

8.	**To much text in source column**
Replace the long tag with short source like (iPhone, Web Client)
9.	**Uncorrect dog names in name column**
We Made the uncorrect names equal to None, and we tried to find name pattrens other than "This is (dog_name)", we found this pattern “named (dog_name” , then we replaced None with names we found with this pattren

10.	**Erroneous datatypes (tweet_id, timestamp, source, and dog stages column)**
We Convert each column to correct datatype
