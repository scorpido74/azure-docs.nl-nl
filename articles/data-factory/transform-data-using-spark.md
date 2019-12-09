---
title: Gegevens transformeren met Spark-activiteit
description: Meer informatie over het transformeren van gegevens door Spark-Program ma's uit te voeren vanuit een Azure data factory-pijp lijn met behulp van de Spark-activiteit.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912975"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Spark-activiteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-spark.md)
> * [Huidige versie](transform-data-using-spark.md)

Met de Spark-activiteit in een Data Factory [pijp lijn](concepts-pipelines-activities.md) wordt een Spark-programma uitgevoerd op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of HDInsight [-cluster op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten. Wanneer u een met Spark gekoppelde on-demand-service gebruikt, wordt Data Factory automatisch een Spark-cluster gemaakt zodat u de gegevens kunt verwerken en vervolgens het cluster verwijdert zodra de verwerking is voltooid. 


## <a name="spark-activity-properties"></a>Eigenschappen van Spark-activiteit
Hier volgt een voor beeld van de JSON-definitie van een Spark-activiteit:    

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

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt:

| Eigenschap              | Beschrijving                              | Verplicht |
| --------------------- | ---------------------------------------- | -------- |
| name                  | De naam van de activiteit in de pijp lijn.    | Ja      |
| description           | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                  | Voor Spark-activiteiten is het type activiteit HDInsightSpark. | Ja      |
| linkedServiceName     | De naam van de gekoppelde HDInsight Spark-service waarop het Spark-programma wordt uitgevoerd. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Ja      |
| SparkJobLinkedService | De Azure Storage gekoppelde service die het Spark-taak bestand, de afhankelijkheden en de logboeken bevat.  Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag gebruikt die aan het HDInsight-cluster is gekoppeld. De waarde van deze eigenschap kan alleen een Azure Storage gekoppelde service zijn. | Nee       |
| rootPath              | De Azure Blob-container en de map waarin het Spark-bestand zich bevindt. De bestands naam is hoofdletter gevoelig. Raadpleeg de sectie mappen structuur (volgende sectie) voor meer informatie over de structuur van deze map. | Ja      |
| entryFilePath         | Relatief pad naar de hoofdmap van de Spark-code/-pakket. Het invoer bestand moet een python-bestand of een jar-bestand zijn. | Ja      |
| className             | Hoofd klasse java/Spark van de toepassing      | Nee       |
| arguments             | Een lijst met opdracht regel argumenten voor het Spark-programma. | Nee       |
| proxyUser             | Het gebruikers account dat moet worden geïmiteerd voor het uitvoeren van het Spark-programma | Nee       |
| sparkConfig           | Geef waarden op voor Spark-configuratie-eigenschappen die worden vermeld in het onderwerp: [Spark-configuratie-eigenschappen van toepassing](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nee       |
| getDebugInfo          | Hiermee geeft u op wanneer de Spark-logboek bestanden worden gekopieerd naar de Azure-opslag die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door sparkJobLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | Nee       |

## <a name="folder-structure"></a>Mapstructuur
Spark-taken zijn meer uitbreidbaar dan Pig/Hive-taken. U kunt voor Spark-taken meerdere afhankelijkheden opgeven, zoals jar-pakketten (die in het Java-KLASSENPAD zijn geplaatst), python-bestanden (die zijn geplaatst op de PYTHONPATH) en andere bestanden.

Maak de volgende mapstructuur in de Azure Blob-opslag waarnaar wordt verwezen door de gekoppelde HDInsight-service. Upload vervolgens afhankelijke bestanden naar de juiste submappen in de hoofdmap die wordt vertegenwoordigd door **entryFilePath**. Upload bijvoorbeeld python-bestanden naar de pyFiles-submap en JAR-bestanden naar de submap potten van de hoofdmap. Tijdens runtime verwacht Data Factory service de volgende mapstructuur in de Azure Blob-opslag:     

| Pad                  | Beschrijving                              | Verplicht | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | Het pad naar de hoofdmap van de Spark-taak in de gekoppelde opslag service | Ja      | Map |
| door de gebruiker gedefinieerde &gt; &lt; | Het pad naar het invoer bestand van de Spark-taak | Ja      | Bestand   |
| ./jars                | Alle bestanden in deze map worden geüpload en geplaatst in het Java-klassenpad van het cluster | Nee       | Map |
| ./pyFiles             | Alle bestanden in deze map worden geüpload en op de PYTHONPATH van het cluster geplaatst | Nee       | Map |
| ./files               | Alle bestanden in deze map worden geüpload en op de werk directory van de uitvoerder geplaatst | Nee       | Map |
| ./archives            | Alle bestanden in deze map zijn niet-gecomprimeerd | Nee       | Map |
| ./logs                | De map die logboeken van het Spark-cluster bevat. | Nee       | Map |

Hier volgt een voor beeld van een opslag met twee Spark-taak bestanden in de Azure-Blob Storage waarnaar wordt verwezen door de gekoppelde HDInsight-service.

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
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor batch uitvoering Machine Learning](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
