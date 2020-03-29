---
title: Gegevens transformeren met Spark-activiteit
description: Meer informatie over het transformeren van gegevens door Spark-programma's uit een Azure-pijplijn voor gegevensfabrieken uit te voeren met behulp van de Spark-activiteit.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: eb887a7d9081875c28964ddb1e3d1b2e609862fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912975"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Gegevens transformeren met Spark-activiteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-spark.md)
> * [Huidige versie](transform-data-using-spark.md)

De Spark-activiteit in een Data [Factory-pijplijn](concepts-pipelines-activities.md) voert een Spark-programma uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand HDInsight-cluster.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten. Wanneer u een on-demand Spark-gekoppelde service gebruikt, maakt Data Factory automatisch een Spark-cluster voor u just-in-time om de gegevens te verwerken en verwijdert u het cluster zodra de verwerking is voltooid. 


## <a name="spark-activity-properties"></a>Eigenschappen van spark-activiteit
Hier is de voorbeeld JSON definitie van een Spark Activiteit:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

In de volgende tabel worden de JSON-eigenschappen beschreven die worden gebruikt in de JSON-definitie:

| Eigenschap              | Beschrijving                              | Vereist |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Naam van de activiteit in de pijplijn.    | Ja      |
| description           | Tekst waarin wordt beschreven wat de activiteit doet.  | Nee       |
| type                  | Voor Spark Activity is het activiteitstype HDInsightSpark. | Ja      |
| linkedServiceName     | Naam van de HDInsight Spark Linked Service waarop het Spark-programma wordt uitgevoerd. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| SparkJobLinkedService | De gekoppelde Azure Storage-service met het Spark-taakbestand, afhankelijkheden en logboeken.  Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag die is gekoppeld aan het HDInsight-cluster gebruikt. De waarde van deze eigenschap kan alleen een gekoppelde Azure Storage-service zijn. | Nee       |
| rootPath              | De Azure Blob-container en -map die het Spark-bestand bevat. De bestandsnaam is hoofdlettergevoelig. Raadpleeg de sectie mapstructuur (volgende sectie) voor meer informatie over de structuur van deze map. | Ja      |
| entryFilePath         | Relatief pad naar de hoofdmap van de Spark-code/-pakket. Het invoerbestand moet een Python-bestand of een .jar-bestand zijn. | Ja      |
| Classname             | Java/Spark-hoofdklasse van toepassing      | Nee       |
| Argumenten             | Een lijst met opdrachtregelargumenten voor het Spark-programma. | Nee       |
| proxyGebruiker             | Het gebruikersaccount dat zich moet voordoen om het Spark-programma uit te voeren | Nee       |
| sparkConfig           | Waarden opgeven voor spark-configuratie-eigenschappen die in het onderwerp worden weergegeven: [Spark-configuratie - toepassingseigenschappen](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nee       |
| getDebugInfo          | Hiermee geeft u op wanneer de Spark-logboekbestanden worden gekopieerd naar de Azure-opslag die wordt gebruikt door hdinsight-cluster (of) die is opgegeven door sparkJobLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee       |

## <a name="folder-structure"></a>Mapstructuur
Spark banen zijn meer uitbreidbaar dan Pig / Hive banen. Voor Spark-taken u meerdere afhankelijkheden bieden, zoals potpakketten (geplaatst in het javaCLASSPATH), python-bestanden (geplaatst op het PYTHONPATH) en andere bestanden.

Maak de volgende mapstructuur in de Azure Blob-opslag waarnaar wordt verwezen door de gekoppelde HDInsight-service. Upload vervolgens afhankelijke bestanden naar de juiste submappen in de hoofdmap die wordt weergegeven door **entryFilePath**. Upload bijvoorbeeld python-bestanden naar de submap van pyFiles en jarbestanden naar de submap van de potvan de hoofdmap. Bij runtime verwacht de service Data Factory de volgende mapstructuur in de Azure Blob-opslag:     

| Pad                  | Beschrijving                              | Vereist | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(wortel)            | Het hoofdpad van de Spark-taak in de gekoppelde opslagservice | Ja      | Map |
| &lt;gebruiker gedefinieerd&gt; | Het pad dat naar het invoerbestand van de Spark-taak wijst | Ja      | File   |
| ./potten                | Alle bestanden onder deze map worden geüpload en geplaatst op het java-klassepad van het cluster | Nee       | Map |
| ./pyFiles             | Alle bestanden onder deze map worden geüpload en op het PYTHONPATH van het cluster geplaatst | Nee       | Map |
| ./bestanden               | Alle bestanden onder deze map worden geüpload en geplaatst op de werkmap executor | Nee       | Map |
| ./archieven            | Alle bestanden onder deze map zijn niet gecomprimeerd | Nee       | Map |
| ./logboeken                | De map met logboeken uit het Spark-cluster. | Nee       | Map |

Hier is een voorbeeld voor een opslag met twee Spark-taakbestanden in de Azure Blob Storage waarnaar wordt verwezen door de gekoppelde HDInsight-service.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
