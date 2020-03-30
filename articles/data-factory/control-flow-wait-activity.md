---
title: Wachtactiviteit in Azure Data Factory
description: Met de activiteit Wachten wordt de uitvoering van de pijplijn voor de opgegeven periode onderbroken.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678248"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Wachtactiviteit uitvoeren in Azure Data Factory
Als u een Wait Activity in een pijplijn gebruikt, wacht de pijplijn tot de opgegeven periode voorbij is voordat de volgende activiteiten worden uitgevoerd. 

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van `Wait` de activiteit. | Tekenreeks | Ja
type | Moet ingesteld zijn op **Wait.** | Tekenreeks | Ja
waitTimeInSeconden | Het aantal seconden dat de pijplijn wacht voordat de verwerking wordt voortgezet. | Geheel getal | Ja

## <a name="example"></a>Voorbeeld

> [!NOTE]
> In deze sectie worden JSON-definities en voorbeeld-PowerShell-opdrachten voor het uitvoeren van de pijplijn opgenomen. Zie [zelfstudie: maak een gegevensfabriek met Azure PowerShell](quickstart-create-data-factory-powershell.md)voor een stapsgewijze handleiding voor het maken van een Data Factory-pijplijn met azure PowerShell- en JSON-definities.

### <a name="pipeline-with-wait-activity"></a>Pijplijn met wachtactiviteit
In dit voorbeeld heeft de pijplijn twee activiteiten: **Tot** en **wacht.** De activiteit Wachten is geconfigureerd om een seconde te wachten. De pijplijn voert de webactiviteit uit in een lus met een wachttijd van één seconde tussen elke run. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
