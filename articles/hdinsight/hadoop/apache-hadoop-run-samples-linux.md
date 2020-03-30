---
title: Apache Hadoop-kaart uitvoerenVerlaag voorbeelden op HDInsight - Azure
description: Aan de slag met MapReduce-voorbeelden in potbestanden die zijn opgenomen in HDInsight. Gebruik SSH om verbinding te maken met het cluster en gebruik vervolgens de opdracht Hadoop om voorbeeldtaken uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435756"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>De voorbeelden van MapReduce uitvoeren in HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Meer informatie over het uitvoeren van de mapVerlaag voorbeelden die zijn opgenomen bij Apache Hadoop op HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>De voorbeelden mapReduce

De monsters bevinden zich op `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`het HDInsight-cluster op . Broncode voor deze monsters is opgenomen `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`op het HDInsight cluster op .

De volgende voorbeelden zijn opgenomen in dit archief:

|Voorbeeld |Beschrijving |
|---|---|
|geaggregeerde woordtelling|Telt de woorden in de invoerbestanden.|
|aggregatewordhist|Berekent het histogram van de woorden in de invoerbestanden.|
|bbp|Gebruikt Bailey-Borwein-Plouffe om exacte cijfers van Pi te berekenen.|
|dbcount dbcount|Telt de logboeken voor paginaweergave die zijn opgeslagen in een database.|
|distbbp distbbp|Gebruikt een FORMULE van het BBP-type om exacte stukjes Pi te berekenen.|
|Grep|Telt de overeenkomsten van een regex in de invoer.|
|join|Hiermee voert u een join uit via gesorteerde, even verdeelde gegevenssets.|
|multifilewc|Telt woorden uit verschillende bestanden.|
|pentomino pentomino|Tegelleggen programma om oplossingen te vinden voor pentomino problemen.|
|Pi|Schat Pi met behulp van een quasi-Monte Carlo methode.|
|randomtextwriter|Hiermee schrijft u 10 GB aan willekeurige tekstuele gegevens per knooppunt.|
|randomwriter randomwriter|Hiermee schrijft u 10 GB aan willekeurige gegevens per knooppunt.|
|secundaire sortering|Hiermee definieert u een secundaire sortering in de reducefase.|
|sorteren|Sorteert de gegevens geschreven door de willekeurige schrijver.|
|Sudoku|Een sudoku oplosser.|
|teragen|Gegevens genereren voor de terasort.|
|terasort terasort|Voer de terasort.|
|teravalideren|Het controleren van de resultaten van terasort.|
|woordtelling|Telt de woorden in de invoerbestanden.|
|woordehouding|Telt de gemiddelde lengte van de woorden in de invoerbestanden.|
|woordmediaal|Telt de mediaanlengte van de woorden in de invoerbestanden.|
|woordstandaardafwijking|Telt de standaarddeviatie van de lengte van de woorden in de invoerbestanden.|

## <a name="run-the-wordcount-example"></a>Het woordtellingsvoorbeeld uitvoeren

1. Maak verbinding met HDInsight met Behulp van SSH. Vervang `CLUSTER` de naam van uw cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Gebruik in de SSH-sessie de volgende opdracht om de voorbeelden weer te geven:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Met deze opdracht genereert u de lijst met voorbeelden uit de vorige sectie van dit document.

3. Gebruik de volgende opdracht om hulp te krijgen voor een specifiek voorbeeld. In dit geval wordt het voorbeeld van het **aantal woorden** geteld:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    U ontvangt het volgende bericht:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Dit bericht geeft aan dat u verschillende invoerpaden voor de brondocumenten bieden. Het uiteindelijke pad is waar de uitvoer (aantal woorden in de brondocumenten) wordt opgeslagen.

4. Gebruik het volgende om alle woorden te tellen in de notitieblokken van Leonardo da Vinci, die als voorbeeldgegevens bij uw cluster worden geleverd:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Input voor deze taak `/example/data/gutenberg/davinci.txt`wordt gelezen van . Uitvoer voor dit voorbeeld `/example/data/davinciwordcount`wordt opgeslagen in . Beide paden bevinden zich op standaardopslag voor het cluster, niet het lokale bestandssysteem.

   > [!NOTE]  
   > Zoals vermeld in de help voor het wordcount-voorbeeld, u ook meerdere invoerbestanden opgeven. Bijvoorbeeld, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` zou tellen woorden in zowel davinci.txt en ulysses.txt.

5. Zodra de taak is voltooid, gebruikt u de volgende opdracht om de uitvoer weer te geven:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Met deze opdracht worden alle uitvoerbestanden die door de taak zijn geproduceerd, samengevoegd. Het toont de uitvoer naar de console. De uitvoer lijkt op het volgende:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Elke regel vertegenwoordigt een woord en hoe vaak het zich heeft voorgedaan in de invoergegevens.

## <a name="the-sudoku-example"></a>Het voorbeeld van Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) is een logische puzzel die bestaat uit negen 3x3 rasters. Sommige cellen in het raster hebben getallen, terwijl andere leeg zijn, en het doel is om op te lossen voor de lege cellen. De vorige link heeft meer informatie over de puzzel, maar het doel van dit monster is op te lossen voor de lege cellen. Dus onze input moet een bestand dat in de volgende indeling:

* Negen rijen van negen kolommen
* Elke kolom kan een `?` getal bevatten of (dat een lege cel aangeeft)
* Cellen worden gescheiden door een spatie

Er is een bepaalde manier om Sudoku puzzels te construeren; u een getal niet herhalen in een kolom of rij. Er is een voorbeeld op de HDInsight cluster die goed is geconstrueerd. Het bevindt `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` zich op en bevat de volgende tekst:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Als u dit voorbeeldprobleem wilt uitvoeren via het voorbeeld Sudoku, gebruikt u de volgende opdracht:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

De resultaten lijken op de volgende tekst:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Pi (π) voorbeeld

De pi-steekproef gebruikt een statistische (quasi-Monte Carlo) methode om de waarde van pi te schatten. Punten worden willekeurig geplaatst in een eenheidsvierkant. Het plein bevat ook een cirkel. De kans dat de punten binnen de cirkel vallen is gelijk aan het gebied van de cirkel, pi/4. De waarde van pi kan worden geschat op basis van de waarde van 4R. R is de verhouding tussen het aantal punten dat zich binnen de cirkel bevindt met het totale aantal punten dat zich binnen het vierkant bevindt. Hoe groter de steekproef van de gebruikte punten, hoe beter de schatting is.

Gebruik de volgende opdracht om dit voorbeeld uit te voeren. Deze opdracht maakt gebruik van 16 kaarten met elk 10.000.000 monsters om de waarde van pi te schatten:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

De waarde die door deze opdracht wordt geretourneerd, is vergelijkbaar met **3.14159150000000000000000000000**. Voor verwijzingen zijn de eerste 10 decimalen van pi 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort voorbeeld

GraySort is een benchmark sortering. De statistiek is de sorteersnelheid (TB/minuut) die wordt bereikt tijdens het sorteren van grote hoeveelheden gegevens, meestal een minimum van 100 TB.

Dit voorbeeld maakt gebruik van een bescheiden 10 GB aan gegevens, zodat het relatief snel kan worden uitgevoerd. Het maakt gebruik van de MapReduce toepassingen ontwikkeld door Owen O'Malley en Arun Murthy. Deze toepassingen wonnen de jaarlijkse inflatiebenchmark voor algemene doeleinden ("Daytona") in 2009, met een snelheid van 0,578 TB/min (100 TB in 173 minuten). Zie voor meer informatie over deze en andere sorteerbenchmarks de [sortbenchmarksite.](https://sortbenchmark.org/)

In dit voorbeeld worden drie sets MapReduce-programma's gebruikt:

* **TeraGen**: een mapReduce-programma dat rijen gegevens genereert om te sorteren

* **TeraSort**: Monsters van de invoergegevens en gebruikt MapReduce om de gegevens te sorteren in een totale volgorde

    TeraSort is een standaard MapReduce-sortering, met uitzondering van een aangepaste partitie. De partitioner maakt gebruik van een gesorteerde lijst van N-1 gesamplede sleutels die het sleutelbereik voor elke reduce definiëren. In het bijzonder worden alle sleutels zodanig verzonden dat monster[i-1] <= < monster[i] worden verzonden om i te verminderen. Deze partitioner garandeert dat de output van reduce i allemaal minder zijn dan de output van reduce i+1.

* **TeraValidate:** een MapReduce-programma dat valideert dat de uitvoer wereldwijd wordt gesorteerd

    Het maakt één kaart per bestand in de uitvoermap en elke kaart zorgt ervoor dat elke toets kleiner is dan of gelijk is aan de vorige. De kaartfunctie genereert records van de eerste en laatste toetsen van elk bestand. De reduce functie zorgt ervoor dat de eerste sleutel van bestand i groter is dan de laatste sleutel van bestand i-1. Eventuele problemen worden gemeld als een uitvoer van de verminderingsfase, met de toetsen die niet in orde zijn.

Gebruik de volgende stappen om gegevens te genereren, de uitvoer te sorteren en vervolgens te valideren:

1. Genereer 10 GB aan gegevens, die worden opgeslagen `/example/data/10GB-sort-input`in de standaardopslag van het HDInsight-cluster op:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    De `-Dmapred.map.tasks` vertelt Hadoop hoeveel kaarttaken u moet gebruiken voor deze taak. De laatste twee parameters instrueren de taak om 10 `/example/data/10GB-sort-input`GB aan gegevens te maken en op te slaan op .

2. Gebruik de volgende opdracht om de gegevens te sorteren:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    De `-Dmapred.reduce.tasks` vertelt Hadoop hoeveel taken te verminderen om te gebruiken voor de taak. De laatste twee parameters zijn slechts de invoer- en uitvoerlocaties voor gegevens.

3. Gebruik het volgende om de gegevens die door de sortering worden gegenereerd, te valideren:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel heb je geleerd hoe je de voorbeelden uitvoert die zijn opgenomen in de Linux-gebaseerde HDInsight-clusters. Zie de volgende onderwerpen voor zelfstudies over het gebruik van Pig, Hive en MapReduce met HDInsight:

* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [MapGebruikenReduce met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)