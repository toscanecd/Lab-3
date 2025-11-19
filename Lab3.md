**Lab 3**

*1 - Find five random user nodes*

MATCH (u:User)
RETURN u
ORDER BY rand()
LIMIT 5;

*2 - Find five random FOLLOWS relationships*

MATCH (u1:User)-[r:FOLLOWS]->(u2:User)
RETURN u1, r, u2
ORDER BY rand()
LIMIT 5;

*3 - Find the text property of three random Tweet nodes*

MATCH (t:Tweet)
RETURN t.text
ORDER BY rand()
LIMIT 3;

*4 - Generate a Cypher statement to visualize sample RETWEETS relationships*

MATCH (author:User)-[:POSTED]->(orig:Tweet)
MATCH (rt:Tweet)-[:RETWEET_OF]->(orig)
MATCH (retweeter:User)-[:POSTED]->(rt)
RETURN author, orig, rt, retweeter
LIMIT 25;

*5 - Why using merge and not create ?*

Because create generates a new link every time, it can lead to duplicates. In contrast, merge checks if the link already exists and only creates a new link if it does not.

*6 - Calculate the ratio of missing values for the created at node property of the Tweet nodes*

MATCH (t:Tweet)
WITH count(t) AS total,
     count { (t) WHERE t.createdAt IS NULL } AS missing
RETURN missing,
       total,
       toFloat(missing) / total AS ratioMissing;

*7 - Count the number of relationships by their type. To count the number of relationships grouped by their type, you can start by describing a relationship pattern*

MATCH ()-[r]->()
RETURN type(r) AS relType,
       count(*) AS relCount
ORDER BY relCount DESC;

*8 - Compare the text of an original tweet and its retweet*

MATCH (rt:Tweet)-[:RETWEET_OF]->(orig:Tweet)
RETURN orig.text  AS originalText,
       rt.text    AS retweetText
LIMIT 10;

*9 - Calculate the distribution of tweets grouped by year created*

MATCH (t:Tweet)
WITH date(t.createdAt) AS d
RETURN d.year AS year,
       count(*) AS tweetCount
ORDER BY year;

*10 - Use the MATCH clause in combination with the WHERE clause to select all the tweets that were created in 2021*

MATCH (t:Tweet)
WHERE date(t.createdAt) >= date('2021-01-01')
  AND date(t.createdAt) <  date('2022-01-01')
RETURN t;

*11 - Return the top four days with the highest count of created tweets*

MATCH (t:Tweet)
WITH date(t.createdAt) AS day, count(*) AS tweetCount
RETURN day, tweetCount
ORDER BY tweetCount DESC
LIMIT 4;

*12 - Count the number of users who were mentioned but haven t published a single tweet*

MATCH (:Tweet)-[:MENTIONS]->(u:User)
WHERE NOT (u)-[:POSTED]->(:Tweet)
RETURN count(DISTINCT u) AS mentionedButNoTweet;

*13 - Find the top five users with the most distinct tweets retweeted*

MATCH (author:User)-[:POSTED]->(orig:Tweet)
MATCH (:Tweet)-[:RETWEET_OF]->(orig)
WITH author, count(DISTINCT orig) AS retweetedTweets
RETURN author.username AS username,
       retweetedTweets
ORDER BY retweetedTweets DESC
LIMIT 5;

*14 - Find the top five most mentioned users*

MATCH (:Tweet)-[:MENTIONS]->(u:User)
RETURN u.username AS username,
       count(*)   AS mentionCount
ORDER BY mentionCount DESC
LIMIT 5;

*15 - Find the 10 most followed Users*

MATCH (:User)-[:FOLLOWS]->(u:User)
RETURN u.username AS username,
       count(*)   AS followers
ORDER BY followers DESC
LIMIT 10;

*16 - Find the top 10 users who follow the most people*

MATCH (u:User)-[:FOLLOWS]->(:User)
RETURN u.username AS username,
       count(*)   AS followingCount
ORDER BY followingCount DESC
LIMIT 10;

