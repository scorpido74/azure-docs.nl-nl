---
title: Gegevens transformeren met de Hadoop Pig-activiteit
description: Ontdek hoe u de varkensactiviteit in een Azure-gegevensfabriek gebruiken om Pig-scripts uit te voeren op een on-demand/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 4064d62a6dc826b23ff1f51e9f61e48d362ae695
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912941"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Gegevens transformeren met Hadoop Pig-activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-pig-activity.md)
> * [Huidige versie](transform-data-using-hadoop-pig.md)

De HDInsight Pig-activiteit in een Data [Factory-pijplijn](concepts-pipelines-activities.md) voert Pig-query's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand HDInsight-cluster.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en doet u de [zelfstudie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntaxis

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
## <a name="syntax-details"></a>Syntaxisdetails

| Eigenschap            | Beschrijving                              | Vereist |
| ------------------- | ---------------------------------------- | -------- |
| name                | Naam van de activiteit                     | Ja      |
| description         | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type                | Voor Hive Activity is het activiteitstype HDinsightPig | Ja      |
| linkedServiceName   | Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| scriptLinkedService | Verwijzing naar een Azure Storage Linked Service die wordt gebruikt om het Pig-script op te slaan dat moet worden uitgevoerd. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage Linked Service gebruikt die is gedefinieerd in de HDInsight Linked Service. | Nee       |
| scriptPath          | Geef het pad op naar het scriptbestand dat is opgeslagen in de Azure Storage die wordt aangeduid door scriptLinkedService. De bestandsnaam is hoofdlettergevoelig. | Nee       |
| getDebugInfo        | Hiermee geeft u op wanneer de logboekbestanden worden gekopieerd naar de Azure Storage die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee       |
| Argumenten           | Hiermee geeft u een reeks argumenten op voor een Hadoop-taak. De argumenten worden doorgegeven als opdrachtregelargumenten voor elke taak. | Nee       |
| Definieert             | Geef parameters op als sleutel-/waardeparen voor verwijzingen in het script Varken. | Nee       |

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
