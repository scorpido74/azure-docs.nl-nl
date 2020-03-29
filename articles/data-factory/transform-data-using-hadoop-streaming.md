---
title: Gegevens transformeren met Hadoop Streaming-activiteit
description: Hiermee wordt uitgelegd hoe u Hadoop-streamingactiviteit in Azure Data Factory gebruiken om gegevens te transformeren door Hadoop-streamingprogramma's uit te voeren op een Hadoop-cluster.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 1c12a10dfdf8e69cf05ab30d0e6aa48fea5803a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912904"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Gegevens transformeren met Hadoop-streamingactiviteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Huidige versie](transform-data-using-hadoop-streaming.md)

De HDInsight Streaming Activity in een Data [Factory-pijplijn](concepts-pipelines-activities.md) voert Hadoop Streaming-programma's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand HDInsight-cluster.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en doet u de [zelfstudie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

## <a name="json-sample"></a>JSON-monster
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
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

| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| name              | Naam van de activiteit                     | Ja      |
| description       | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type              | Voor Hadoop Streaming Activity is het activiteitstype HDInsightStreaming | Ja      |
| linkedServiceName | Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| Mapper            | Hiermee geeft u de naam op van de uitvoerbare mapper | Ja      |
| Reducer           | Hiermee geeft u de naam op van de verkleiner uitvoerbare | Ja      |
| combiner          | Hiermee geeft u de naam op van de uitvoerbare combiner | Nee       |
| fileLinkedService | Verwijzing naar een Azure Storage Linked Service die wordt gebruikt om de programma's Mapper, Combiner en Reducer op te slaan die moeten worden uitgevoerd. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage Linked Service gebruikt die is gedefinieerd in de HDInsight Linked Service. | Nee       |
| Filepath          | Geef een array van pad naar de Mapper-, Combiner- en Reducer-programma's die zijn opgeslagen in de Azure Storage die wordt aangeduid met bestandsLinkedService. Het pad is hoofdlettergevoelig. | Ja      |
| input             | Hiermee geeft u het WASB-pad op naar het invoerbestand voor de Mapper. | Ja      |
| output            | Hiermee geeft u het WASB-pad op naar het uitvoerbestand voor de Reducer. | Ja      |
| getDebugInfo      | Hiermee geeft u op wanneer de logboekbestanden worden gekopieerd naar de Azure Storage die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee       |
| Argumenten         | Hiermee geeft u een reeks argumenten op voor een Hadoop-taak. De argumenten worden doorgegeven als opdrachtregelargumenten voor elke taak. | Nee       |
| Definieert           | Geef parameters op als sleutel-/waardeparen voor verwijzingen in het Hive-script. | Nee       | 

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
