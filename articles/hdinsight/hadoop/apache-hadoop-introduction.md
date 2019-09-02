---
title: Wat is de Apache Hadoop technologie stack? - Azure HDInsight
description: Een inleiding tot HDInsight en de Apache Hadoop technologie stack en-onderdelen.
keywords: azure hadoop, hadoop azure, hadoop-inleiding, hadoop-informatie, hadoop-technologiestack, inleiding tot hadoop, informatie over hadoop, wat is een hadoop-cluster, wat is hadoop-cluster, waar wordt hadoop voor gebruikt
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: d6127d4fba3d2255dee28cd41179f16394545328
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207222"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Wat is Apache Hadoop in azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) was het originele open-source framework voor gedistribueerde verwerking en analyse van sets met big data in clusters. Het Hadoop-ecosysteem bevat gerelateerde software en hulpprogram ma's, waaronder Apache Hive, Apache HBase, Spark, Kafka en vele andere.

Azure HDInsight is een volledig beheerde, open-source analyse service in de Cloud voor ondernemingen. Met het cluster type Apache Hadoop in azure HDInsight kunt u HDFS, het resource beheer voor GARENs en een eenvoudig MapReduce-programmeer model gebruiken om batch gegevens parallel te verwerken en te analyseren.

Zie [onderdelen en versies die beschikbaar zijn met hdinsight](../hdinsight-component-versioning.md)voor een overzicht van de beschik bare Hadoop-technologie stack onderdelen op hdinsight. Zie de [pagina met Azure-functies voor HDInsight](https://azure.microsoft.com/services/hdinsight/) voor meer informatie over Hadoop in HDInsight.

## <a id="whatis"></a>Wat is MapReduce

Apache Hadoop MapReduce is een software raamwerk voor het schrijven van taken waarmee enorme hoeveel heden gegevens worden verwerkt. Invoer gegevens worden in onafhankelijke segmenten gesplitst. Elk segment wordt parallel verwerkt over de knoop punten in uw cluster. Een MapReduce-taak bestaat uit twee functies:

* **Mapper**: Gebruikt invoer gegevens, analyseert deze (meestal met filter-en sorteer bewerkingen) en verstuurt Tuples (sleutel-waardeparen)

* **Reductier**: Verbruikt Tuples die worden verzonden door de Mapper en voert een samenvattings bewerking uit die een kleiner, gecombineerd resultaat van de toewijzings gegevens maakt

In het volgende diagram ziet u een voor beeld van een basis aantal woorden MapReduce-taak:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

De uitvoer van deze taak is het aantal keer dat elk woord is opgetreden in de tekst.

* De Mapper neemt elke regel uit de invoer tekst als invoer en splitst deze in woorden. Telkens wanneer een woord in het woord wordt uitgevoerd, wordt een sleutel/waarde-paar verzonden, gevolgd door een 1. De uitvoer wordt gesorteerd voordat deze naar de reductie functie wordt verzonden.
* De verkorter telt het aantal afzonderlijke woorden voor elk woord en verzendt één sleutel/waarde-paar dat het woord bevat, gevolgd door de som van de bijbehorende exemplaren.

MapReduce kan in verschillende talen worden geïmplementeerd. Java is de meest voorkomende implementatie en wordt gebruikt voor demonstratie doeleinden in dit document.

## <a name="development-languages"></a>Ontwikkelings talen

Talen of frameworks die zijn gebaseerd op Java en de Java Virtual Machine kunnen rechtstreeks als een MapReduce-taak worden uitgevoerd. Het voor beeld dat in dit document wordt gebruikt, is een Java MapReduce-toepassing. Niet-Java-talen, zoals C#python of zelfstandige uitvoer bare bestanden, moeten **Hadoop streaming**gebruiken.

Hadoop streaming communiceert met de Mapper en de verkorter dan STDIN en STDOUT. De Mapper en reducer lezen gegevens een regel tegelijk van STDIN en schrijven de uitvoer naar STDOUT. Elke regel die wordt gelezen of verzonden door de Mapper en de verkleinder moet de indeling hebben van een sleutel/waarde-paar, gescheiden door een tabteken:

    [key]/t[value]

Zie [Hadoop streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html)voor meer informatie.

Raadpleeg het volgende document voor voor beelden van het gebruik van Hadoop streaming met HDInsight:

* [MapReduce C# -taken ontwikkelen](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-cluster maken in HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
