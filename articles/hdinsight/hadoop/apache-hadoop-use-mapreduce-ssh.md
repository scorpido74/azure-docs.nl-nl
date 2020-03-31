---
title: MapReduce en SSH-verbinding met Apache Hadoop - Azure HDInsight
description: Meer informatie over het gebruik van SSH om MapReduce-taken uit te voeren met Apache Hadoop op HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934705"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>MapGebruikenReduce met Apache Hadoop op HDInsight met SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Meer informatie over het indienen van MapReduce-taken vanuit een Secure Shell-verbinding (SSH) naar HDInsight.

> [!NOTE]
> Als u al bekend bent met het gebruik van Linux-gebaseerde Apache Hadoop servers, maar je bent nieuw bij HDInsight, zie [Linux-gebaseerde HDInsight tips](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Vereisten

Een Apache Hadoop cluster op HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Hadoop-opdrachten gebruiken

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nadat u bent verbonden met het HDInsight-cluster, gebruikt u de volgende opdracht om een taak MapReduce te starten:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Met deze `wordcount` opdracht wordt de klasse `hadoop-mapreduce-examples.jar` gestart, die in het bestand staat. Het gebruikt `/example/data/gutenberg/davinci.txt` het document als invoer `/example/data/WordCountOutput`en de uitvoer wordt opgeslagen bij .

    > [!NOTE]
    > Zie [MapReduce gebruiken in Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over deze mapReduce-taak en de voorbeeldgegevens.

    De taak zendt details uit terwijl deze worden verwerkt en retourneert informatie die vergelijkbaar is met de volgende tekst wanneer de taak is voltooid:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de uitvoerbestanden weer te geven:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Met deze opdracht `_SUCCESS` worden `part-r-00000`twee bestanden weergegeven en . Het `part-r-00000` bestand bevat de uitvoer voor deze taak.

    > [!NOTE]  
    > Sommige mapReduce-taken kunnen de resultaten splitsen over meerdere **deel-r-#####--bestanden.** Gebruik dan het achtervoegsel ##### om de volgorde van de bestanden aan te geven.

1. Als u de uitvoer wilt weergeven, gebruikt u de volgende opdracht:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Met deze opdracht wordt een lijst weergegeven met de woorden die zich in het **wasbs://example/data/gutenberg/davinci.txt-bestand** bevinden en het aantal keren dat elk woord is opgetreden. De volgende tekst is een voorbeeld van de gegevens in het bestand:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Volgende stappen

Zoals u zien, bieden Hadoop-opdrachten een eenvoudige manier om MapReduce-taken uit te voeren in een HDInsight-cluster en vervolgens de taakuitvoer weer te geven. Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [MapgebruikenReduce op HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
