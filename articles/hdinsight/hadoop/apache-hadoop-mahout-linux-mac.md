---
title: Aanbevelingen genereren met Apache mahout en HDInsight (SSH)-Azure
description: Meer informatie over het gebruik van de Apache mahout machine learning-bibliotheek om film aanbevelingen te genereren met HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a3919cf84714b69776222fa35d3163e0915869f7
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881973"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Tips voor het genereren van films met Apache mahout met Apache Hadoop in HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Meer informatie over het gebruik van de [Apache Mahout](https://mahout.apache.org) machine learning-bibliotheek met Azure HDInsight om film aanbevelingen te genereren.

Mahout is een [machine learning](https://en.wikipedia.org/wiki/Machine_learning) -bibliotheek voor Apache Hadoop. Mahout bevat algoritmen voor het verwerken van gegevens, zoals filteren, classificatie en clustering. In dit artikel gebruikt u een aanbevelings Engine om film aanbevelingen te genereren die zijn gebaseerd op films die uw vrienden hebben gezien.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Versie beheer van Apache mahout

Zie [hdinsight-versies en Apache Hadoop onderdelen](../hdinsight-component-versioning.md)voor meer informatie over de versie van mahout in hdinsight.

## <a name="recommendations"></a>Uitleg over aanbevelingen

Een van de functies die door mahout wordt gegeven, is een aanbevelings engine. Deze engine accepteert gegevens in de indeling van `userID`, `itemId`en `prefValue` (de voor keur voor het item). Mahout kan vervolgens analyse van co-exemplaren uitvoeren om te bepalen: *gebruikers die een voor keur voor een item hebben, hebben ook een voor keur voor deze andere items*. Mahout bepaalt vervolgens gebruikers met voor keuren zoals-item, die kunnen worden gebruikt om aanbevelingen te doen.

De volgende werk stroom is een vereenvoudigd voor beeld waarin film gegevens worden gebruikt:

* **Co-exemplaar**: Joe, Alice en Bob zijn allemaal leukste *ster-oorlogen*, *het Empire keert terug*en *retourneert de Jedi*. Mahout bepaalt dat gebruikers die graag een van deze films ook als de andere hebben.

* **Co-exemplaar**: Bob en Alice vinden ook *het fantoom Menace*, de *aanval van de klonen en het terugvallen* *van de Sith*. Mahout bepaalt dat gebruikers die de vorige drie films leuk vinden, ook als deze drie films.

* **Aanbeveling voor gelijkenis**: Omdat Joe de eerste drie films leuk vindt, kijkt mahout naar films die andere voor keuren leuk vinden, maar is Jan niet gekeken (leuk/geclassificeerd). In dit geval raadt mahout aan *het fantoom Menace*, de *aanval van de klonen*en *het re van de Sith*.

### <a name="understanding-the-data"></a>Informatie over de gegevens

[GroupLens Research](https://grouplens.org/datasets/movielens/) biedt classificatie gegevens voor films in een indeling die compatibel is met mahout. Deze gegevens zijn beschikbaar op de standaard opslag van uw cluster `/HdiSamples/HdiSamples/MahoutMovieData`op.

Er zijn twee bestanden `moviedb.txt` en. `user-ratings.txt` Het `user-ratings.txt` bestand wordt gebruikt tijdens de analyse. De `moviedb.txt` wordt gebruikt om gebruikers vriendelijke tekst informatie te bieden bij het weer geven van de resultaten.

De gegevens in User-ratings. txt hebben de `userID`structuur, `movieID`, `userRating`en `timestamp`, waarmee wordt aangegeven hoe hoog elke gebruiker een film heeft geclassificeerd. Hier volgt een voor beeld van de gegevens:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>De analyse uitvoeren

Gebruik vanuit een SSH-verbinding met het cluster de volgende opdracht om de aanbevelings taak uit te voeren:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Het kan enkele minuten duren voordat de taak is voltooid en er kunnen meerdere MapReduce-taken worden uitgevoerd.

## <a name="view-the-output"></a>De uitvoer weer geven

1. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de gegenereerde uitvoer weer te geven:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    De uitvoer ziet er als volgt uit:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    De eerste kolom is de `userID`. De waarden die zijn opgenomen in ' [' en '] `movieId`'`recommendationScore`zijn:.

2. U kunt de uitvoer, samen met de moviedb. txt, gebruiken om meer informatie te geven over de aanbevelingen. Kopieer eerst de bestanden lokaal met behulp van de volgende opdrachten:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Met deze opdracht worden de uitvoer gegevens gekopieerd naar een bestand met de naam **aanbevelingen. txt** in de huidige map, samen met de film gegevens bestanden.

3. Gebruik de volgende opdracht om een python-script te maken waarmee film namen worden gezocht voor de gegevens in de uitvoer van de aanbevelingen:

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

    Druk op **CTRL-X**, **Y**en tenslotte **Enter** om de gegevens op te slaan.

4. Voer het python-script uit. Bij de volgende opdracht wordt ervan uitgegaan dat u zich in de map bevindt waarin alle bestanden zijn gedownload:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Met deze opdracht wordt gekeken naar de aanbevelingen die zijn gegenereerd voor gebruikers-ID 4.

   * Het **User-ratings. txt** -bestand wordt gebruikt voor het ophalen van films die zijn geclassificeerd.

   * Het **moviedb. txt** -bestand wordt gebruikt om de namen van de films op te halen.

   * De **aanbevelingen. txt** worden gebruikt voor het ophalen van de video aanbevelingen voor deze gebruiker.

     De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

       Zeven jaar in Tibet (1997), score = 5.0 Indiana Jansen en de laatste Crusade (1989), score = 5.0 Jaws (1975), score = 5.0 Sense en Sensibility (1995), score = 5,0 Onafhankelijkheidsdag (ID4) (1996), score = 5.0 mijn beste vriend (1997), score = 5.0 Jerry Maguire (1996), Score = 5.0 Scream 2 (1997), score = 5.0 tijd om af te breken, A (1996), score = 5.0

## <a name="delete-temporary-data"></a>Tijdelijke gegevens verwijderen

Mahout-taken verwijderen geen tijdelijke gegevens die tijdens het verwerken van de taak worden gemaakt. De `--tempDir` para meter wordt opgegeven in de voorbeeld taak om de tijdelijke bestanden in een specifiek pad te isoleren, zodat u ze eenvoudig kunt verwijderen. Als u de tijdelijke bestanden wilt verwijderen, gebruikt u de volgende opdracht:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Als u de opdracht opnieuw wilt uitvoeren, moet u ook de uitvoer Directory verwijderen. Gebruik de volgende om deze map te verwijderen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u mahout kunt gebruiken, kunt u andere manieren ontdekken voor het werken met gegevens op HDInsight:

* [Apache Hive met HDInsight](hdinsight-use-hive.md)
* [Apache-Pig met HDInsight](hdinsight-use-pig.md)
* [MapReduce met HDInsight](hdinsight-use-mapreduce.md)