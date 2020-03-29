---
title: Twitter-gegevens analyseren met Apache Hive - Azure HDInsight
description: Meer informatie over het gebruik van Apache Hive en Apache Hadoop op HDInsight om ruwe TWitter-gegevens om te zetten in een doorzoekbare Hive-tabel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435621"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analyseer Twitter-gegevens met Apache Hive en Apache Hadoop op HDInsight

Meer informatie over het gebruik van [Apache Hive](https://hive.apache.org/) om Twitter-gegevens te verwerken. Het resultaat is een lijst van Twitter-gebruikers die de meeste tweets die een bepaald woord bevatten verzonden.

> [!IMPORTANT]  
> De stappen in dit document zijn getest op HDInsight 3.6.

## <a name="get-the-data"></a>De gegevens ophalen

Met Twitter u de gegevens voor elke tweet ophalen als een Json-document (JavaScript Object Notation) via een REST API. [OAuth](https://oauth.net) is vereist voor verificatie naar de API.

### <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken

1. Log vanuit een webbrowser [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)in bij . Selecteer de link Aanmelden nu als je geen **Twitter-account** hebt.

2. Selecteer **Nieuwe app maken**.

3. Voer **naam**, **beschrijving**, **website**in . U een URL voor het veld **Website** vormen. In de volgende tabel worden enkele voorbeeldwaarden weergegeven die u wilt gebruiken:

   | Veld | Waarde |
   |--- |--- |
   | Name |MyHDInsightApp |
   | Beschrijving |MyHDInsightApp |
   | Website |`https://www.myhdinsightapp.com` |

4. Selecteer **Ja, ik ga akkoord**en selecteer vervolgens Uw **Twitter-toepassing maken**.

5. Selecteer het tabblad **Machtigingen.** De standaardmachtiging is **Alleen lezen**.

6. Selecteer het tabblad **Sleutels en Toegangstokens.**

7. Selecteer **Mijn toegangstoken maken**.

8. Selecteer **OAuth testen** in de rechterbovenhoek van de pagina.

9. Schrijf **consumentensleutel**, **Consumentengeheim**, **Access-token**en **Access-tokengeheim op**.

### <a name="download-tweets"></a>Tweets downloaden

De volgende Python-code downloadt 10.000 tweets van Twitter en slaat ze op in een bestand met de naam **tweets.txt**.

> [!NOTE]  
> De volgende stappen worden uitgevoerd op het HDInsight-cluster, omdat Python al is geïnstalleerd.

1. Gebruik [de ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdrachten om [Tweepy,](https://www.tweepy.org/) [Voortgangsbalk](https://pypi.python.org/pypi/progressbar/2.2)en andere vereiste pakketten te installeren:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Gebruik de volgende opdracht om een bestand met de naam **gettweets.py**te maken:

   ```bash
   nano gettweets.py
   ```

1. Bewerk de onderstaande `Your consumer secret`code `Your consumer key` `Your access token`door `Your access token secret` het vervangen van , , en met de relevante informatie uit uw twitter applicatie. Plak vervolgens de bewerkte code als de inhoud van het **gettweets.py** bestand.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Pas het filter van onderwerpen op de laatste regel aan om populaire zoekwoorden bij te houden. Met behulp van zoekwoorden populair op het moment dat u het script uitvoert zorgt voor een snellere vastlegging van gegevens.

1. Gebruik **Ctrl + X**en vervolgens **Y** om het bestand op te slaan.

1. Gebruik de volgende opdracht om het bestand uit te voeren en tweets te downloaden:

    ```bash
    python gettweets.py
    ```

    Er verschijnt een voortgangsindicator. Het telt tot 100% als de tweets worden gedownload.

   > [!NOTE]  
   > Als het lang duurt voordat de voortgangsbalk wordt gevorderd, moet u het filter wijzigen om trending onderwerpen bij te houden. Wanneer er veel tweets over het onderwerp in uw filter, u snel de 100 tweets die nodig zijn.

### <a name="upload-the-data"></a>De gegevens uploaden

Als u de gegevens wilt uploaden naar HDInsight-opslag, gebruikt u de volgende opdrachten:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Met deze opdrachten worden de gegevens opgeslagen op een locatie waaralle knooppunten in het cluster toegang toe hebben.

## <a name="run-the-hiveql-job"></a>De HiveQL-taak uitvoeren

1. Gebruik de volgende opdracht om een bestand te maken met [HiveQL-instructies:](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

   ```bash
   nano twitter.hql
   ```

    Gebruik de volgende tekst als de inhoud van het bestand:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Druk op **Ctrl + X**en druk vervolgens op **Y** om het bestand op te slaan.

1. Gebruik de volgende opdracht om de HiveQL in het bestand uit te voeren:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Dit commando draait het **twitter.hql** bestand. Zodra de query is voltooid, ziet u een `jdbc:hive2//localhost:10001/>` prompt.

1. Gebruik de volgende query om te controleren of gegevens zijn geïmporteerd vanuit de bijenlijnprompt:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Met deze query worden maximaal 10 tweets geretourneerd die het woord **Azure** in de berichttekst bevatten.

    > [!NOTE]  
    > Als u het filter `gettweets.py` in het script hebt gewijzigd, vervangt u **Azure** door een van de filters die u hebt gebruikt.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een ongestructureerde JSON-gegevensset omzetten in een gestructureerde [Apache Hive-tabel.](https://hive.apache.org/) Zie de volgende documenten voor meer informatie over Hive op HDInsight:

* [Aan de slag met HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vluchtvertragingsgegevens analyseren met HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
