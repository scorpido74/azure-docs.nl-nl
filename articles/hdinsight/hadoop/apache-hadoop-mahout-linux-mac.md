---
title: Aanbevelingen genereren met Apache Mahout in Azure HDInsight
description: Meer informatie over het gebruik van de Apache Mahout machine learning-bibliotheek om filmaanbevelingen te genereren met HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767633"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Filmaanbevelingen genereren met Apache Mahout met Apache Hadoop in HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Meer informatie over het gebruik van de [Machine Learning-bibliotheek van Apache Mahout](https://mahout.apache.org) met Azure HDInsight om filmaanbevelingen te genereren.

Mahout is een [machine learning](https://en.wikipedia.org/wiki/Machine_learning) bibliotheek voor Apache Hadoop. Mahout bevat algoritmen voor het verwerken van gegevens, zoals filteren, classificatie en clustering. In dit artikel gebruikt u een aanbevelingsengine om filmaanbevelingen te genereren die zijn gebaseerd op films die uw vrienden hebben gezien.

## <a name="prerequisites"></a>Vereisten

Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout versiewerk

Zie [HDInsight-versies en Apache Hadoop-componenten](../hdinsight-component-versioning.md)voor meer informatie over de versie van Mahout in HDInsight.

## <a name="understanding-recommendations"></a>Aanbevelingen begrijpen

Een van de functies die wordt geleverd door Mahout is een aanbeveling motor. Deze engine accepteert gegevens in `userID` `itemId`het `prefValue` formaat van , en (de voorkeur voor het item). Mahout kan vervolgens een analyse voor co-occurrences uitvoeren om te bepalen: *gebruikers die een voorkeur hebben voor een item hebben ook een voorkeur voor deze andere items.* Mahout bepaalt vervolgens gebruikers met voorkeuren voor like-items, die kunnen worden gebruikt om aanbevelingen te doen.

De volgende werkstroom is een vereenvoudigd voorbeeld dat filmgegevens gebruikt:

* **Co-occurrence**: Joe, Alice, en Bob alle vond *Star Wars*, The Empire *Strikes Back*, en Return of *the Jedi*. Mahout bepaalt dat gebruikers die graag een van deze films ook net als de andere twee.

* **Co-occurrence**: Bob en Alice ook graag *The Phantom Menace*, Attack of the *Clones*, en *Revenge of the Sith*. Mahout bepaalt dat gebruikers die graag de vorige drie films ook graag deze drie films.

* **Gelijkenis aanbeveling:** Omdat Joe graag de eerste drie films, Mahout kijkt naar films die anderen met soortgelijke voorkeuren graag, maar Joe heeft niet gekeken (liked / beoordeeld). In dit geval, Mahout beveelt *The Phantom Menace*, Attack of the *Clones*, en *Revenge of the Sith*.

### <a name="understanding-the-data"></a>Inzicht in de gegevens

Handig is dat [GroupLens Research](https://grouplens.org/datasets/movielens/) classificatiegegevens voor films biedt in een formaat dat compatibel is met Mahout. Deze gegevens zijn beschikbaar op de `/HdiSamples/HdiSamples/MahoutMovieData`standaardopslag van uw cluster op .

Er zijn twee `moviedb.txt` `user-ratings.txt`bestanden, en. Het `user-ratings.txt` bestand wordt gebruikt tijdens de analyse. Het `moviedb.txt` wordt gebruikt om gebruiksvriendelijke tekstinformatie te verstrekken bij het bekijken van de resultaten.

De gegevens in `user-ratings.txt` heeft een `userID` `movieID`structuur `userRating`van `timestamp`, , en , die aangeeft hoe sterk elke gebruiker beoordeeld een film. Hier is een voorbeeld van de gegevens:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>De analyse uitvoeren

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdracht om de aanbevelingstaak uit te voeren:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Het kan enkele minuten duren voordat de taak is voltooid en kan meerdere mapreduce-taken uitvoeren.

## <a name="view-the-output"></a>De uitvoer weergeven

1. Zodra de taak is voltooid, gebruikt u de volgende opdracht om de gegenereerde uitvoer weer te geven:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    De uitvoer wordt als volgt weergegeven:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    De eerste kolom `userID`is de . De waarden in '[' en ']' zijn: `movieId``recommendationScore`.

2. U de uitvoer, samen met de moviedb.txt, gebruiken om meer informatie te geven over de aanbevelingen. Kopieer de bestanden vervolgens lokaal met de volgende opdrachten:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Met deze opdracht worden de uitvoergegevens gekopieerd naar een bestand met de naam **recommendations.txt** in de huidige map, samen met de filmgegevensbestanden.

3. Gebruik de volgende opdracht om een Python-script te maken dat filmnamen opzoekt voor de gegevens in de uitvoer aanbevelingen:

    ```bash
    nano show_recommendations.py
    ```

    Wanneer de editor wordt geopend, gebruikt u de volgende tekst als de inhoud van het bestand:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Druk op **Ctrl-X,** **Y**en voer uiteindelijk **In** om de gegevens op te slaan.

4. Voer het Python-script uit. Met de volgende opdracht wordt ervan uitgegaan dat u zich in de map bevindt waarin alle bestanden zijn gedownload:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Met deze opdracht wordt gekeken naar de aanbevelingen die zijn gegenereerd voor gebruikers-ID 4.

   * Het **user-ratings.txt-bestand** wordt gebruikt om films op te halen die zijn beoordeeld.

   * Het **moviedb.txt-bestand** wordt gebruikt om de namen van de films op te halen.

   * De **recommendations.txt** wordt gebruikt om de filmaanbevelingen voor deze gebruiker op te halen.

     De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Tijdelijke gegevens verwijderen

Mahout-taken verwijderen geen tijdelijke gegevens die worden gemaakt tijdens het verwerken van de taak. De `--tempDir` parameter wordt opgegeven in de voorbeeldtaak om de tijdelijke bestanden te isoleren in een specifiek pad voor eenvoudige verwijdering. Als u de tijdelijke bestanden wilt verwijderen, gebruikt u de volgende opdracht:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Als u de opdracht opnieuw wilt uitvoeren, moet u ook de uitvoermap verwijderen. Gebruik het volgende om deze map te verwijderen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Volgende stappen

Nu je hebt geleerd hoe je Mahout gebruiken, ontdek je andere manieren om met gegevens over HDInsight te werken:

* [Apache Hive met HDInsight](hdinsight-use-hive.md)
* [MapReduce met HDInsight](hdinsight-use-mapreduce.md)
