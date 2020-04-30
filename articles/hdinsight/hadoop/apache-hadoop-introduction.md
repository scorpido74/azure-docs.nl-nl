---
title: Wat zijn Apache Hadoop en MapReduce-Azure HDInsight
description: Een inleiding tot HDInsight en de Apache Hadoop technologie stack en-onderdelen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78244877"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Wat is Apache Hadoop in azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) was het originele open-source framework voor gedistribueerde verwerking en analyse van sets met big data in clusters. Het Hadoop-ecosysteem bevat gerelateerde software en hulpprogram ma's, waaronder Apache Hive, Apache HBase, Spark, Kafka en vele andere.

Azure HDInsight is een volledig beheerde, open-source analyse service in de Cloud voor ondernemingen. Met het cluster type Apache Hadoop in azure HDInsight kunt u HDFS, het resource beheer voor GARENs en een eenvoudig MapReduce-programmeer model gebruiken om batch gegevens parallel te verwerken en te analyseren.

Raadpleeg [Components and versions available with HDInsight](../hdinsight-component-versioning.md) (Onderdelen en versies die beschikbaar zijn in HDInsight) om na te gaan welke onderdelen van de Hadoop-technologiestack in HDInsight beschikbaar zijn. Zie de [pagina met Azure-functies voor HDInsight](https://azure.microsoft.com/services/hdinsight/) voor meer informatie over Hadoop in HDInsight.

## <a name="what-is-mapreduce"></a>Wat is MapReduce

Apache Hadoop MapReduce is een software raamwerk voor het schrijven van taken waarmee enorme hoeveel heden gegevens worden verwerkt. Invoer gegevens worden in onafhankelijke segmenten gesplitst. Elk segment wordt parallel verwerkt over de knoop punten in uw cluster. Een MapReduce-taak bestaat uit twee functies:

* **Mapper**: gebruikt invoer gegevens, analyseert deze (meestal met filter-en sorteer bewerkingen) en verzendt Tuples (sleutel-waardeparen)

* **Versmaller**: door de Mapper gegenereerde Tuples worden gebruikt en een samenvattings bewerking wordt uitgevoerd waarmee een kleiner, gecombineerd resultaat van de toewijzings gegevens wordt gemaakt

In het volgende diagram ziet u een voor beeld van een basis aantal woorden MapReduce-taak:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

De uitvoer van deze taak is het aantal keer dat elk woord is opgetreden in de tekst.

* De Mapper neemt elke regel uit de invoer tekst als invoer en splitst deze in woorden. Telkens wanneer een woord in het woord wordt uitgevoerd, wordt een sleutel/waarde-paar verzonden, gevolgd door een 1. De uitvoer wordt gesorteerd voordat deze naar de reductie functie wordt verzonden.
* De verkorter telt het aantal afzonderlijke woorden voor elk woord en verzendt één sleutel/waarde-paar dat het woord bevat, gevolgd door de som van de bijbehorende exemplaren.

MapReduce kan in verschillende talen worden geïmplementeerd. Java is de meest voorkomende implementatie en wordt gebruikt voor demonstratie doeleinden in dit document.

## <a name="development-languages"></a>Ontwikkelings talen

Talen of frameworks die zijn gebaseerd op Java en de Java Virtual Machine kunnen rechtstreeks als een MapReduce-taak worden uitgevoerd. Het voor beeld dat in dit document wordt gebruikt, is een Java MapReduce-toepassing. Niet-Java-talen, zoals C#, python of zelfstandige uitvoer bare bestanden, moeten **Hadoop streaming**gebruiken.

Hadoop streaming communiceert met de Mapper en de verkorter dan STDIN en STDOUT. De Mapper en reducer lezen gegevens een regel tegelijk van STDIN en schrijven de uitvoer naar STDOUT. Elke regel die wordt gelezen of verzonden door de Mapper en de verkleinder moet de indeling hebben van een sleutel/waarde-paar, gescheiden door een tabteken:

    [key]/t[value]

Zie [Hadoop streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)voor meer informatie.

Raadpleeg het volgende document voor voor beelden van het gebruik van Hadoop streaming met HDInsight:

* [C# MapReduce-taken ontwikkelen](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-cluster maken in HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
