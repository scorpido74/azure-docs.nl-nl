---
title: Gegevens transformeren met Hadoop Hive-activiteit
description: Ontdek hoe u de Hive-activiteit in een Azure-gegevensfabriek gebruiken om Hive-query's uit te voeren op een on-demand/uw eigen HDInsight-cluster.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: b4af3f897a12c71d73962735d3fe68d95f138cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912909"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Gegevens transformeren met Hadoop Hive-activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-hive-activity.md)
> * [Huidige versie](transform-data-using-hadoop-hive.md)

De HDInsight Hive-activiteit in een Data [Factory-pijplijn](concepts-pipelines-activities.md) voert Hive-query's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand HDInsight-cluster.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en doet u de [zelfstudie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

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
## <a name="syntax-details"></a>Syntaxisdetails
| Eigenschap            | Beschrijving                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Naam van de activiteit                                         | Ja      |
| description         | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt                | Nee       |
| type                | Voor Hive-activiteit is het activiteitstype HDinsightHive        | Ja      |
| linkedServiceName   | Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| scriptLinkedService | Verwijzing naar een Azure Storage Linked Service die wordt gebruikt om het Hive-script op te slaan dat moet worden uitgevoerd. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage Linked Service gebruikt die is gedefinieerd in de HDInsight Linked Service. | Nee       |
| scriptPath          | Geef het pad op naar het scriptbestand dat is opgeslagen in de Azure Storage die wordt aangeduid door scriptLinkedService. De bestandsnaam is hoofdlettergevoelig. | Ja      |
| getDebugInfo        | Hiermee geeft u op wanneer de logboekbestanden worden gekopieerd naar de Azure Storage die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee       |
| Argumenten           | Hiermee geeft u een reeks argumenten op voor een Hadoop-taak. De argumenten worden doorgegeven als opdrachtregelargumenten voor elke taak. | Nee       |
| Definieert             | Geef parameters op als sleutel-/waardeparen voor verwijzingen in het Hive-script. | Nee       |
| queryTime-out        | Waarde van een time-out van query 's (in enkele minuten). Van toepassing wanneer het HDInsight-cluster is ingeschakeld met Enterprise Security Package. | Nee       |

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
