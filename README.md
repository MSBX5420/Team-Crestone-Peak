## **Team Crestone Peak**
- Jason Engel
- Zach Johnson
- Natalie Garibaldi
- Julian Beaupre
- Yubing Yang


### Project requirements
- **Dataset**: [Dataset](https://www.kaggle.com/ryanxjhan/cbc-news-coronavirus-articles-march-26)
  - Context
  >"Has the news media been overreacting or under-reacting during the development of COVID-19? What are the media's main focuses? How is the news correlated to public reactions or policy changes? You might find many insights with more than 3,500 CBC news articles."

  - Content
  
       It contains the authors, the title, the publish date, the description about the story, the main story, and the url.

- **Environment**: AWS EMR
- **Programming Language**: Spark with Python
- **Functional Requirements**:
  - Ingest: Ingest dataset to S3 Bucket
  - Basic statistics in Jupyter Notebook
  - Others: We want to use sentiment analysis on the text from different news stations, and potentially create a multiclassifcation problem having different emotions in our sentiment analysis (eg. angry, happy). We could then see the trends and predict how certain news stations react to events like this on a time based and text based scale. We can also look into the relationships between words and try to add context to further help the model understand the text as best as possible.
- **Performance Requirements**:
  - Your applications should shows horizontal scale (i.e. when we adding more nodes the cluster, the processing should be faster also)
  

### Project Design
[Design Doc](https://github.com/MSBX5420/Team-Crestone-Peak/blob/design-doc.md)


[Code](https://github.com/MSBX5420/Team-Crestone-Peak/blob/master/code/analysis.ipynb)


[Code](https://github.com/MSBX5420/Team-Crestone-Peak/blob/master/project/analysis.ipynb)

**Preprocessing/Cleaning**


**Parsing**


**Models**


**Deployment**


**Conclusion**



