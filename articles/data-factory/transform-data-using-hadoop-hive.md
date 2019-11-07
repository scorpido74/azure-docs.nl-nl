---
title: Gegevens transformeren met behulp van Hadoop Hive-activiteit in Azure Data Factory
description: Meer informatie over hoe u de Hive-activiteit in een Azure-data factory kunt gebruiken om Hive-query's uit te voeren op een op aanvraag/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c423192624ecc76e839f9fee434956f4d57aefdc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683907"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hadoop Hive-activiteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-hive-activity.md)
> * [Huidige versie](transform-data-using-hadoop-hive.md)

De HDInsight Hive-activiteit in een Data Factory [pijp lijn](concepts-pipelines-activities.md) voert Hive-query's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of HDInsight [-cluster op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](introduction.md) en voer de [volgende zelf studie uit: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Eigenschap            | Beschrijving                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| naam                | Naam van de activiteit                                         | Ja      |
| description         | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt                | Nee       |
| type                | Voor Hive-activiteit is het type activiteit HDinsightHive        | Ja      |
| linkedServiceName   | Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Ja      |
| scriptLinkedService | Verwijzing naar een Azure Storage gekoppelde service die wordt gebruikt voor het opslaan van het Hive-script dat moet worden uitgevoerd. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage gekoppelde service gebruikt die is gedefinieerd in de gekoppelde HDInsight-service. | Nee       |
| scriptPath          | Geef het pad op naar het script bestand dat is opgeslagen in de Azure Storage waarnaar wordt verwezen door scriptLinkedService. De bestands naam is hoofdletter gevoelig. | Ja      |
| getDebugInfo        | Hiermee geeft u op wanneer de logboek bestanden worden gekopieerd naar de Azure Storage gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | Nee       |
| opmerkingen           | Hiermee wordt een matrix met argumenten voor een Hadoop-taak opgegeven. De argumenten worden door gegeven als opdracht regel argumenten voor elke taak. | Nee       |
| compliant             | Geef para meters op als sleutel/waarde-paren voor het verwijzen in het Hive-script. | Nee       |
| queryTimeout        | Time-outwaarde (in minuten) van de query. Van toepassing wanneer het HDInsight-cluster Enterprise Security Package ingeschakeld. | Nee       |

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor batch uitvoering Machine Learning](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
