---
title: Gegevens transformeren met behulp van Hadoop Pig-activiteit
description: Meer informatie over hoe u de Pig-activiteit in een Azure data factory kunt gebruiken om Pig-scripts uit te voeren op een op aanvraag/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 54eff77daa7e69c39e9ec5d6f326f2f64c9fcafb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849226"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hadoop Pig-activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-pig-activity.md)
> * [Huidige versie](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De HDInsight-Pig-activiteit in een Data Factory [pijp lijn](concepts-pipelines-activities.md) voert Pig-query's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of HDInsight [-cluster op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](introduction.md) en voer de [volgende zelf studie uit: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Syntaxis Details

| Eigenschap            | Beschrijving                              | Vereist |
| ------------------- | ---------------------------------------- | -------- |
| naam                | Naam van de activiteit                     | Yes      |
| description         | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | No       |
| type                | Voor Hive-activiteit is het type activiteit HDinsightPig | Yes      |
| linkedServiceName   | Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Yes      |
| scriptLinkedService | Verwijzing naar een Azure Storage gekoppelde service die wordt gebruikt voor het opslaan van het Pig-script dat moet worden uitgevoerd. Hier worden alleen **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** -en **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** gekoppelde services ondersteund. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage gekoppelde service gebruikt die is gedefinieerd in de gekoppelde HDInsight-service. | No       |
| scriptPath          | Geef het pad op naar het script bestand dat is opgeslagen in de Azure Storage waarnaar wordt verwezen door scriptLinkedService. De bestands naam is hoofdletter gevoelig. | No       |
| getDebugInfo        | Hiermee geeft u op wanneer de logboek bestanden worden gekopieerd naar de Azure Storage gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | No       |
| opmerkingen           | Hiermee wordt een matrix met argumenten voor een Hadoop-taak opgegeven. De argumenten worden door gegeven als opdracht regel argumenten voor elke taak. | No       |
| compliant             | Geef para meters op als sleutel/waarde-paren voor het verwijzen binnen het Pig-script. | Nee       |

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor batch uitvoering Machine Learning](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
