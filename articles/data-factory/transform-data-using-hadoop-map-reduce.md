---
title: Gegevens transformeren met Hadoop MapReduce activity
description: Meer informatie over het verwerken van gegevens door Hadoop Mapte uitvoerenVerlaag programma's op een Azure HDInsight-cluster vanuit een Azure-gegevensfabriek.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912868"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Gegevens transformeren met Hadoop MapVerminder activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-map-reduce.md)
> * [Huidige versie](transform-data-using-hadoop-map-reduce.md)

De HDInsight MapReduce activiteit in een Data [Factory-pijplijn](concepts-pipelines-activities.md) roept MapReduce-programma op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand HDInsight-cluster.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en doet u de zelfstudie: [Zelfstudie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest.

Zie [Pig](transform-data-using-hadoop-pig.md) en [Hive](transform-data-using-hadoop-hive.md) voor meer informatie over het uitvoeren van Pig/Hive-scripts op een HDInsight-cluster vanuit een pijplijn met HDInsight Pig- en Hive-activiteiten.

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Syntaxisdetails

| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| name              | Naam van de activiteit                     | Ja      |
| description       | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type              | Voor MapReduce Activity is het activiteitstype HDinsightMapReduce | Ja      |
| linkedServiceName | Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| Classname         | Naam van de klasse die moet worden uitgevoerd         | Ja      |
| jarLinkedService  | Verwijzing naar een Azure Storage Linked Service die wordt gebruikt om de Jar-bestanden op te slaan. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage Linked Service gebruikt die is gedefinieerd in de HDInsight Linked Service. | Nee       |
| jarFilePath       | Geef het pad naar de Jar-bestanden die zijn opgeslagen in de Azure Storage die door jarLinkedService wordt verwezen. De bestandsnaam is hoofdlettergevoelig. | Ja      |
| jarlibs           | Tekenreeksarray van het pad naar de Jar-bibliotheekbestanden waarnaar wordt verwezen door de taak die is opgeslagen in de Azure Storage die is gedefinieerd in jarLinkedService. De bestandsnaam is hoofdlettergevoelig. | Nee       |
| getDebugInfo      | Hiermee geeft u op wanneer de logboekbestanden worden gekopieerd naar de Azure Storage die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door jarLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee       |
| Argumenten         | Hiermee geeft u een reeks argumenten op voor een Hadoop-taak. De argumenten worden doorgegeven als opdrachtregelargumenten voor elke taak. | Nee       |
| Definieert           | Geef parameters op als sleutel-/waardeparen voor verwijzingen in het Hive-script. | Nee       |



## <a name="example"></a>Voorbeeld
U de HDInsight MapReduce Activity gebruiken om elk MapReduce-jarbestand op een HDInsight-cluster uit te voeren. In de volgende VOORBEELD-JSON-definitie van een pijplijn is de HDInsight-activiteit geconfigureerd om een Mahout JAR-bestand uit te voeren.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
U alle argumenten opgeven voor het programma MapReduce in de sectie **Argumenten.** Bij runtime ziet u een paar extra argumenten (bijvoorbeeld: mapreduce.job.tags) uit het MapReduce-framework. Als u uw argumenten wilt onderscheiden met de argumenten MapReduce, u overwegen zowel de optie als de waarde te gebruiken als argumenten zoals weergegeven in het volgende voorbeeld (-s, --invoer, --uitvoer enz., zijn opties die onmiddellijk worden gevolgd door hun waarden).

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
