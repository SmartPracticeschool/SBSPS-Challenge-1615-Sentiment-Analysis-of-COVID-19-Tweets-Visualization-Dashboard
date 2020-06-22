# SBSPS-Challenge-1615-Sentiment-Analysis-of-COVID-19-Tweets-Visualization-Dashboard

#extraction of tweets through following code

import pandas as pd
import tweepy
import re
import xlsxwriter
import datetime
import time
consumer_key = 'y0xbgZ91ZFqIJ68BHVeUYrr5L'
consumer_secret = 'ClQqeBQv3KPSZj38QXWNlkx84ZS2CinqDWYM9U1fbZCWFbvaXV'
access_token = '1274399613189939200-8qbR2lD4l5zyE8uHiz5fgT6HkeBB3W'
access_token_secret = 'IFuNypvlMuGrxSs9uLaXJNnp3ouqWeCcrorBynrU6zoQB'

# Creating the authentication object
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
# Setting your access token and secret
auth.set_access_token(access_token, access_token_secret)
# Creating the API object while passing in auth information
api = tweepy.API(auth)
def datetime_from_utc_to_local(utc_datetime):
    now_timestamp = time.time()
    offset = datetime.datetime.fromtimestamp(now_timestamp) - datetime.datetime.utcfromtimestamp(now_timestamp)
    return utc_datetime + offset

workbook = xlsxwriter.Workbook('C:/Users/rosha/Desktop/covid19-tweets.xlsx')
worksheet = workbook.add_worksheet()
worksheet.write('A1', 'Source')
worksheet.write('B1', 'Channel')
worksheet.write('C1', 'Link')
worksheet.write('D1', 'Title')
worksheet.write('E1', 'Time of Post')
format = workbook.add_format({'text_wrap': True})
worksheet.set_column('C:D',30, format)
worksheet.set_column('A:B',20, format)
worksheet.set_column('E:E',30, format)

row = 1
col = 0




for tweet in tweepy.Cursor(api.home_timeline,exclude_retweets=True,
                        exclude_replies=True,
                        contributor_details=False,
                        include_entities=False).items(200):
    created_date_local = datetime_from_utc_to_local(tweet.created_at)
    print(f"{tweet.user.name} said: {tweet.text} at time:{created_date_local}")
    tweettext=tweet.text
    url=[]
    url=re.findall('http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\(\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+', tweettext)
    
    tweetuser=tweet.user.name
    tweettime=str(created_date_local)
    print(url)
    channel="twitter"
    data = ( [tweet.user.name, channel, url, tweettext, created_date_local],)
    for tweetuser, channel,url,tweet, tweettime in (data): 
        i=0
        worksheet.write(row, col, tweetuser) 
        worksheet.write(row,col + 1, channel)
        if url:
            worksheet.write(row,col + 2, url[i])
        worksheet.write(row,col + 3, tweettext)
        worksheet.write(row,col + 4, tweettime)
        i+=1
        row += 1
workbook.close()

worksheet = pd.read_excel("C:/Users/rosha/Desktop/covid19-tweets.xlsx")

pd.to_datetime(worksheet['Time of Post']).dt.strftime("%d %b, %Y - %I:%M %p")



    

 


 



