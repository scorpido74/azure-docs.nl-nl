---
title: Een interactieve Spark-shell gebruiken in azure HDInsight
description: Een interactieve Spark-shell biedt een proces voor lezen-uitvoeren-afdrukken voor het uitvoeren van Spark-opdrachten een voor een tegelijk en de resultaten te zien.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435216"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark uitvoeren vanuit de Spark-shell

Een interactieve [Apache Spark](https://spark.apache.org/) shell biedt een repl-omgeving (read-execute-print-lus) voor het uitvoeren van Spark-opdrachten een voor een tegelijk en de resultaten te bekijken. Dit proces is handig voor het ontwikkelen en opsporen van fouten. Spark biedt één shell voor elk van de ondersteunde talen: scala, python en R.

## <a name="run-an-apache-spark-shell"></a>Een Apache Spark shell uitvoeren

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark biedt shells voor scala (Spark-shell) en python (pyspark). Voer in uw SSH-sessie een van de volgende opdrachten in:

    ```bash
    spark-shell
    pyspark
    ```

    U kunt nu Spark-opdrachten invoeren in de juiste taal.

1. Enkele eenvoudige voorbeeld opdrachten:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-en SparkContext-instanties

Standaard worden instanties van SparkSession en SparkContext automatisch voor u geïnstantieerd wanneer u de Spark-shell uitvoert.

Voer `spark`in om toegang te krijgen tot het SparkSession-exemplaar. Voer `sc`in om toegang te krijgen tot het SparkContext-exemplaar.

## <a name="important-shell-parameters"></a>Belang rijke shell parameters

De Spark shell-opdracht (`spark-shell`of `pyspark`) ondersteunt veel opdracht regel parameters. Als u een volledige lijst met para meters wilt weer geven, start u de Spark-shell met de switch `--help`. Sommige van deze para meters zijn mogelijk alleen van toepassing op `spark-submit`, die door de Spark-shell worden ingepakt.

| schakelen | description | Voorbeeld |
| --- | --- | --- |
| --master MASTER_URL | Hiermee geeft u de hoofd-URL op. In HDInsight is deze waarde altijd `yarn`. | `--master yarn`|
| --potten JAR_LIST | Een door komma's gescheiden lijst met lokale potten die moeten worden toegevoegd aan het stuur programma en de namen van de uitvoerder. In HDInsight bestaat deze lijst uit paden naar het standaard bestands systeem in Azure Storage of Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pakketten MAVEN_COORDS | Een door komma's gescheiden lijst met maven-coördinaten van potten die moeten worden vermeld op het stuur programma en de uitvoerder classpath. Zoekt in de lokale maven opslag plaats en vervolgens naar maven Central, en eventuele extra externe opslag plaatsen die zijn opgegeven bij `--repositories`. De notatie voor de coördinaten is *groupid*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-bestanden lijst | Alleen voor python: een door komma's gescheiden lijst met zip-, eier-of py-bestanden die op de PYTHONPATH moet worden geplaatst. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Volgende stappen

- Zie [Inleiding tot Apache Spark in azure HDInsight](apache-spark-overview.md) voor een overzicht.
- Zie [een Apache Spark cluster in azure HDInsight maken](apache-spark-jupyter-spark-sql.md) voor gebruik met Spark-clusters en SparkSQL.
- Zie [Wat is Apache Spark Structured streaming?](apache-spark-streaming-overview.md) om toepassingen te schrijven die streaminggegevens verwerken met Spark.
