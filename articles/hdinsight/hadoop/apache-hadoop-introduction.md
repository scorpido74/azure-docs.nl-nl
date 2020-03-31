---
title: Wat zijn Apache Hadoop en MapReduce - Azure HDInsight
description: Een inleiding tot HDInsight, en de Apache Hadoop technologie stack en componenten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244877"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Wat is Apache Hadoop in Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) was het originele open-source framework voor gedistribueerde verwerking en analyse van sets met big data in clusters. Het Hadoop-ecosysteem omvat gerelateerde software en hulpprogramma's, waaronder Apache Hive, Apache HBase, Spark, Kafka en vele anderen.

Azure HDInsight is een volledig beheerde, full-spectrum, open-source analytics service in de cloud voor ondernemingen. Met het Clustertype Apache Hadoop in Azure HDInsight u HDFS, YARN-resourcebeheer en een eenvoudig MapReduce-programmeermodel gebruiken om batchgegevens parallel te verwerken en te analyseren.

Raadpleeg [Components and versions available with HDInsight](../hdinsight-component-versioning.md) (Onderdelen en versies die beschikbaar zijn in HDInsight) om na te gaan welke onderdelen van de Hadoop-technologiestack in HDInsight beschikbaar zijn. Zie de [pagina met Azure-functies voor HDInsight](https://azure.microsoft.com/services/hdinsight/) voor meer informatie over Hadoop in HDInsight.

## <a name="what-is-mapreduce"></a>Wat is MapReduce

Apache Hadoop MapReduce is een softwareframework voor het schrijven van taken die enorme hoeveelheden gegevens verwerken. Invoergegevens worden opgesplitst in onafhankelijke segmenten. Elk stuk wordt parallel verwerkt over de knooppunten in uw cluster. Een mapReduce-taak bestaat uit twee functies:

* **Mapper**: Verbruikt invoergegevens, analyseert deze (meestal met filter- en sorteerbewerkingen) en zendt tuples uit (sleutelwaardeparen)

* **Reducer**: verbruikt tuples die door de Mapper worden uitgestoten en voert een overzichtsbewerking uit die een kleiner, gecombineerd resultaat van de Mapper-gegevens maakt

Een basiswoordtellingmapHet taakvoorbeeld is geïllustreerd in het volgende diagram:

 ![Hdi. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

De uitvoer van deze taak is een telling van hoe vaak elk woord in de tekst heeft plaatsgevonden.

* De mapper neemt elke regel uit de invoertekst als invoertekst en breekt deze in woorden. Het zendt een sleutel/waardepaar uit telkens wanneer een woord van het woord wordt gevolgd door een 1. De uitvoer wordt gesorteerd voordat deze naar reducer wordt verzonden.
* De reducer somt deze afzonderlijke telt voor elk woord en zendt een enkele sleutel / waarde paar dat het woord, gevolgd door de som van de gebeurtenissen bevat.

MapReduce kan in verschillende talen worden geïmplementeerd. Java is de meest voorkomende implementatie en wordt gebruikt voor demonstratiedoeleinden in dit document.

## <a name="development-languages"></a>Ontwikkelingstalen

Talen of frameworks die zijn gebaseerd op Java en de Java Virtual Machine kunnen direct worden uitgevoerd als een MapReduce taak. Het voorbeeld dat in dit document wordt gebruikt, is een Java MapReduce-toepassing. Niet-Java-talen, zoals C#, Python of standalone executables, moeten **Hadoop streaming**gebruiken.

Hadoop streaming communiceert met de mapper en reducer via SODIN en SODOUT. De mapper en reducer lezen gegevens een regel tegelijk van SODIN en schrijven de uitvoer naar SOAOUT. Elke regel die door de mapper en reducer wordt gelezen of uitgezonden, moet in de vorm van een sleutel/waardepaar zijn, afgebakend door een tabteken:

    [key]/t[value]

Zie [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)voor meer informatie.

Zie het volgende document voor voorbeelden van het gebruik van Hadoop-streaming met HDInsight:

* [C# Map ontwikkelenMinder taken](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-cluster maken in HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
