---
title: MapReduce-en SSH-verbinding met Apache Hadoop-Azure HDInsight
description: Meer informatie over het gebruik van SSH voor het uitvoeren van MapReduce-taken met Apache Hadoop op HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044510"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>MapReduce gebruiken met Apache Hadoop op HDInsight met SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Meer informatie over het verzenden van MapReduce-taken van een SSH-verbinding (Secure Shell) naar HDInsight.

> [!NOTE]
> Zie [op Linux gebaseerde hdinsight-tips](../hdinsight-hadoop-linux-information.md)als u al bekend bent met het gebruik van Apache Hadoop servers die zijn gebaseerd op Linux, maar geen ervaring hebt met hdinsight.

## <a id="prereq"></a>Vereisten

* Een op Linux gebaseerd HDInsight-cluster (Hadoop in HDInsight)

* Een SSH-client. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

## <a id="ssh"></a>Verbinding maken met SSH

Maak verbinding met het cluster via SSH. Met de volgende opdracht maakt u bijvoorbeeld verbinding met een cluster met de naam **myhdinsight** als het **sshuser** -account:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Als u een certificaat sleutel voor SSH-verificatie gebruikt**, moet u mogelijk de locatie van de persoonlijke sleutel opgeven op het client systeem, bijvoorbeeld:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Als u een wacht woord voor SSH-verificatie gebruikt**, moet u het wacht woord opgeven wanneer u hierom wordt gevraagd.

Zie [SSH gebruiken met hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van SSH met hdinsight.

## <a id="hadoop"></a>Hadoop-opdrachten gebruiken

1. Nadat u verbinding hebt gemaakt met het HDInsight-cluster, gebruikt u de volgende opdracht om een MapReduce-taak te starten:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Met deze opdracht wordt de `wordcount`-klasse gestart, die deel uitmaakt van het `hadoop-mapreduce-examples.jar` bestand. Het `/example/data/gutenberg/davinci.txt` document als invoer wordt gebruikt en de uitvoer wordt opgeslagen op `/example/data/WordCountOutput`.

    > [!NOTE]
    > Zie [MapReduce gebruiken in Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over deze MapReduce-taak en de voorbeeld gegevens.

2. De taak verzendt details tijdens het proces en retourneert informatie die lijkt op de volgende tekst wanneer de taak is voltooid:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de uitvoer bestanden weer te geven:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Met deze opdracht worden twee bestanden `_SUCCESS` en `part-r-00000`weer gegeven. Het `part-r-00000` bestand bevat de uitvoer voor deze taak.

    > [!NOTE]  
    > Sommige MapReduce-taken kunnen de resultaten splitsen over meerdere **deel-r-# # # #** -bestanden. Als dit het geval is, gebruikt u het achtervoegsel # # # # # om de volg orde van de bestanden aan te geven.

4. Als u de uitvoer wilt weer geven, gebruikt u de volgende opdracht:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Met deze opdracht wordt een lijst weer gegeven met de woorden die zijn opgenomen in het **wasb://example/data/Gutenberg/DaVinci.txt** -bestand en het aantal keer dat elk woord is opgetreden. De volgende tekst is een voor beeld van de gegevens die in het bestand zijn opgenomen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Samenvatting

Zoals u ziet, bieden Hadoop-opdrachten een eenvoudige manier om MapReduce-taken uit te voeren in een HDInsight-cluster en vervolgens de taak uitvoer weer te geven.

## <a id="nextsteps"></a>Volgende stappen

Voor algemene informatie over MapReduce-taken in HDInsight:

* [MapReduce gebruiken in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [Apache Pig gebruiken met Apache Hadoop op HDInsight](hdinsight-use-pig.md)
