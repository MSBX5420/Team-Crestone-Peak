***

[Git-Repository](https://github.com/MSBX5420/Team-Crestone-Peak), [Requirements Doc](https://github.com/MSBX5420/Team-Crestone-Peak/blob/master/requirements-doc.md), [Design Doc](https://github.com/MSBX5420/Team-Crestone-Peak/blob/master/design-doc.md), [Code](https://github.com/MSBX5420/Team-Crestone-Peak/blob/master/code/analysis.ipynb)

***

### [Data](https://www.kaggle.com/ryanxjhan/cbc-news-coronavirus-articles-march-26)

- Content
  - Authors, the title, the publish date, the description about the story, the main story, and the url.
- Context
  >"Has the news media been overreacting or under-reacting during the development of COVID-19? What are the media's main focuses? How is the news correlated to public reactions or policy changes? You might find many insights with more than 3,500 CBC news articles."


### Software

- **Environment**: AWS EMR
- **Programming Language**: Spark with Python
- **Functional Requirements**:
  - Ingest: Ingest dataset to S3 Bucket
  - Basic statistics in Jupyter Notebook
  - Others: We want to use sentiment analysis on the text from different news stations, and potentially create a multiclassifcation problem having different emotions in our sentiment analysis (eg. angry, happy). We could then see the trends and predict how certain news stations react to events like this on a time based and text based scale. We can also look into the relationships between words and try to add context to further help the model understand the text as best as possible.
- **Performance Requirements**:
  - Your applications should shows horizontal scale (i.e. when we adding more nodes the cluster, the processing should be faster also)
  
  
  
  
## Project Design

**Preprocessing/Cleaning**

After loading in our csv in parquet format, the dataframe needed some slight modifications. First there were quotations that needed to be removed in the author column using regexp_replace. In the original csv, the publish_date column was a string, so we had to change this to DateType form. From here we had to check if there were any na values for the description as well as text columns before moving on.


**Parsing**

In terms of parsing the data, several actions were taken to have our file ready for deployment. We used nltk toolkit for this step. The functions we used for text preprocessing were sent_tokenize_funct, word_tokenize_funct, remove_stopwords_funct, remove_punct_funct, lemma_funct, join_tokens_funct, extract_phrase_funct. These functions were used to tokenize the words into smaller/more manageable lines of text, making it easier to eventually perform sentiment analysis on. All stopwords that do not contribute any meaningful insights to our analysis were also removed. The same is true for meaningless punctuation. Lemmatization was also used to group together the different inflected forms of a word so they could be analysed as a single item.

Tokens Code Snippet:
```python
def sent_tokenize_funct(x):
    return nltk.sent_tokenize(x)

def word_tokenize_funct(x):
    splitted = [word for line in x for word in line.split()]
    return splitted

def remove_stopwords_funct(x):
    from nltk.corpus import stopwords
    stop_words=set(stopwords.words('english'))
    filteredSentence = [w for w in x if not w in stop_words]
    return filteredSentence
```


**Deployment**

For our deployment, we first ingested the data into our local directory in the s3 class bucket. We then used our python script in jupyter notebook  to run the spark job on the dev cluster. The data was taken directly from the s3 bucket, which allowed us to submit the job with no issues. Deploying this model, we wanted to make it scalable, so we tested on both clusters (3 and 20 nodes). This was difficult to interpret because the clusters were both busy at varying times.



**Analysis**

Before we could create our models, we needed to assign values to the phrases in our data frame. Using SentimentIntensityAnalyzer we were able to assign scores to the phrases within our data based on sentiment. Phrases were assigned values of either Negative, Neutral and Positive based on the sentiment associated with them. This data was then converted from an rdd to a spark dataframe. From here we were able to count how many terms in our dataframe were associated with each of the three classifications. There were 239980 values for neutral, 43136 for positive, and 34255 associated with a negative classification. The next step was to extract the top 100 negative and top 100 positive keywords and convert these results into a pandas dataframe. The next step was to extract the top 20 words from each of these new data frames and visualize them with bar charts. Another way to visualize these results that we used were word clouds. We plotted the top 100 negative and top 100 positive words in word clouds that display which words were used most frequently in positive and negative contexts.


Sentiment Analysis Code Snippet:
```python
def sentiment_word_funct(x):
    from nltk.sentiment.vader import SentimentIntensityAnalyzer
    analyzer = SentimentIntensityAnalyzer() 
    senti_list_temp = []
    for i in x:
        y = ''.join(i) 
        vs = analyzer.polarity_scores(y)
        senti_list_temp.append((y, vs))
        senti_list_temp = [w for w in senti_list_temp if w]
    sentiment_list  = []
    for j in senti_list_temp:
        first = j[0]
        second = j[1]
    
        for (k,v) in second.items():
            if k == 'compound':
                if v < 0.0:
                    sentiment_list.append((first, "Negative"))
                elif v == 0.0:
                    sentiment_list.append((first, "Neutral"))
                else:
                    sentiment_list.append((first, "Positive"))
    return sentiment_list
```
 

**Conclusion**

After our job was ran, we found that the top negative words were “death,” “difficulty,” and “risk.” The top positive words were “help,” “hand,” and “number.” These are interesting words to surface when looking at negative and positive sentiment because “help” isn’t necessarily a positive word. One can be asking for help, which can be seen in a negative light, or one can be thankful for help, which has a more positive meaning behind it. Same goes for “hand” and “number.” Both words can be used in several scenarios positively or negatively. Other words such as “death” and “risk” are words that tend to be used more negatively. Being able to see what words are used in negative and positive settings is interesting especially in a situation like the Coronavirus because we can see and better understand how people react in a crisis. If we were able to dive into it more, we would most likely see more emotions in the words used. We could also use a time series analysis to possibly witness the six stages of grief through the words people use over a period of time. This would require a more in-depth multi-class sentiment analysis  model but would be very interesting.

<p align="center">
:---:|:---:
| ![](/plots/top20KeywordsFreq.png){: width="800px"} | ![](/plots/wordCloud.png){: width="800px"} |


:---:|:---:
| ![](/plots/top20NegativeKeywordsFreq.png){: width="800px"}  |  ![](/plots/top20PositiveKeywordsFreq.png){: width="800px"} |



:---:|:---:
| ![](/plots/wordCloud_Negative.png){: width="800px"}  |  ![](/plots/wordCloud_Positive.png){: width="800px"} |
</p>
