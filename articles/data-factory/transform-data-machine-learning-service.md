---
title: Azure Machine Learning-pijplijnen uitvoeren
description: Meer informatie over het uitvoeren van uw Azure Machine Learning-pijplijnen in uw Azure Data Factory-pijplijnen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418997"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning-pijplijnen uitvoeren in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Voer uw Azure Machine Learning-pijplijnen uit als een stap in uw Azure Data Factory-pijplijnen. Met de machine learning-uitvoerpijplijnactiviteit kunnen batchvoorspellingsscenario's worden ingeschakeld, zoals het identificeren van mogelijke leenstandaardinstellingen, het bepalen van het sentiment en het analyseren van gedragspatronen van klanten.

De onderstaande video bevat een zes minuten durende introductie en demonstratie van deze functie.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de activiteit in de pijplijn | Tekenreeks | Ja
type | Type activiteit is 'AzureMLExecutePipeline' | Tekenreeks | Ja
linkedServiceName | Gekoppelde service aan Azure Machine Learning | Gekoppelde serviceverwijzing | Ja
mlPipelineId | ID van de gepubliceerde Azure Machine Learning-pijplijn | Tekenreeks (of expressie met resultaatType van tekenreeks) | Ja
experimentNaam | De naam van het geschiedenisexperiment van de machine learning-pijplijn uitvoeren | Tekenreeks (of expressie met resultaatType van tekenreeks) | Nee
mlPipelineParameters | Sleutel, Waardeparen die moeten worden doorgegeven aan het gepubliceerde eindpunt van de Azure Machine Learning-pijplijn. Sleutels moeten overeenkomen met de namen van pijplijnparameters die zijn gedefinieerd in de gepubliceerde Machine Learning-pijplijn | Object met sleutelwaardeparen (of Expressie met resultaatType-object) | Nee
mlParentRunId | De bovenliggende Azure Machine Learning-pijplijn run-id | Tekenreeks (of expressie met resultaatType van tekenreeks) | Nee
continueOnStepFailure | Of u de uitvoering van andere stappen in de Machine Learning-pijplijn wilt voortzetten als een stap mislukt | booleaans | Nee

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren:

* [Gegevensstroomactiviteit uitvoeren](control-flow-execute-data-flow-activity.md)
* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
