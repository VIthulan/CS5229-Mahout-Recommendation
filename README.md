# CS5229-Mahout-Recommendation
This repo contains the server for the recommendation system

# Tutorial
https://aws.amazon.com/blogs/big-data/building-a-recommender-with-apache-mahout-on-amazon-elastic-mapreduce-emr/

# Steps
## Building a Recommender
To demonstrate how to build an analytic job with Mahout on EMR, we’ll build a movie recommender. We will start with ratings given to movie titles by users in the MovieLens data set, which was compiled by the GroupLens team, and will use the “recommenditembased” example to find most-recommended movies for each user.

## Get the MovieLens data
```
wget http://files.grouplens.org/datasets/movielens/ml-1m.zip
unzip ml-1m.zip
```
Convert ratings.dat, trade “::” for “,”, and take only the first three columns:

```cat ml-1m/ratings.dat | sed 's/::/,/g' | cut -f1-3 -d, > ratings.csv```
## Put ratings file into HDFS:
```hadoop fs -put ratings.csv /ratings.csv```

## Run the recommender job:
```mahout recommenditembased --input /ratings.csv --output recommendations --numRecommendations 10 --outputPathForSimilarityMatrix similarity-matrix --similarityClassname SIMILARITY_COSINE```

### Look for the results in the part-files containing the recommendations:
        hadoop fs -ls recommendations
        hadoop fs -cat recommendations/part-r-00000 | head
You should see a lookup file that looks something like this (your recommendations will be different since they are all 5.0-valued and we are only picking ten):

## Hosting the service
        sudo easy_install twisted
        sudo easy_install klein
        sudo easy_install redis
        
### Install Redis and start up the server.
        wget http://download.redis.io/releases/redis-2.8.7.tar.gz
        tar xzf redis-2.8.7.tar.gz
        cd redis-2.8.7
        make
        ./src/redis-server &

`Download the server.py `

### Start the server
`twistd -noy server.py &`

## Testing the system
`curl localhost:8080/37`

Output will be something similar to
```
[hadoop@ip-172-31-42-23 ~]$ curl localhost:8081/37
The recommendations for user 37 are :> Movie Name: Man with Two Brains, The (1983), Genre: Comedy, Recommendation Confidence: 5.0
 Movie Name: Money Pit, The (1986), Genre: Comedy, Recommendation Confidence: 5.0
 Movie Name: Austin Powers: International Man of Mystery (1997), Genre: Comedy, Recommendation Confidence: 5.0
 Movie Name: Strictly Ballroom (1992), Genre: Comedy|Romance, Recommendation Confidence: 5.0
 Movie Name: Time Bandits (1981), Genre: Adventure|Fantasy|Sci-Fi, Recommendation Confidence: 5.0
 Movie Name: Like Water for Chocolate (Como agua para chocolate) (1992), Genre: Drama|Romance, Recommendation Confidence: 5.0
 Movie Name: River Runs Through It, A (1992), Genre: Drama, Recommendation Confidence: 5.0
 Movie Name: Snow White and the Seven Dwarfs (1937), Genre: Animation|Children's|Musical, Recommendation Confidence: 5.0
 Movie Name: Mister Roberts (1955), Genre: Comedy|Drama|War, Recommendation Confidence: 5.0
 Movie Name: Broadcast News (1987), Genre: Comedy|Drama|Romance, Recommendation Confidence: 5.0
```

