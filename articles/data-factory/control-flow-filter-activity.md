---
title: Activiteit filteren in Azure Data Factory | Microsoft Docs
description: Met de filter activiteit worden de invoer filters gefilterd.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: a7e2e735baa7e40b4170d3397327e90fc1a5d2d5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141676"
---
# <a name="filter-activity-in-azure-data-factory"></a>Activiteit in Azure Data Factory filteren
U kunt een filter activiteit in een pijp lijn gebruiken om een filter expressie toe te passen op een invoer matrix. 

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Description | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | De naam van `Filter` de activiteit. | Tekenreeks | Ja
Type | Moet worden ingesteld op **filter**. | Tekenreeks | Ja
condition | De voor waarde die moet worden gebruikt voor het filteren van de invoer. | Expressie | Ja
items | Invoer matrix waarop het filter moet worden toegepast. | Expressie | Ja

## <a name="example"></a>Voorbeeld

In dit voor beeld heeft de pijp lijn twee activiteiten: **Filter** en **foreach**. De filter activiteit is zo geconfigureerd dat de invoer matrix wordt gefilterd op items met een waarde die groter is dan 3. De ForEach-activiteit doorloopt vervolgens de gefilterde waarden en wacht op het aantal seconden dat is opgegeven door de huidige waarde.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
