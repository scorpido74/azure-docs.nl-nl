---
title: Filteractiviteit in Azure Data Factory
description: De activiteit Filter filtert de ingangen.
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
ms.openlocfilehash: 2c8ada22b4492bef0239b6f811d5a7bd58e58510
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417209"
---
# <a name="filter-activity-in-azure-data-factory"></a>Filteractiviteit in Azure Data Factory
U een filteractiviteit in een pijplijn gebruiken om een filterexpressie toe te passen op een invoerarray. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van `Filter` de activiteit. | Tekenreeks | Ja
type | Moet worden ingesteld op **filter**. | Tekenreeks | Ja
Voorwaarde | Voorwaarde die moet worden gebruikt voor het filteren van de invoer. | Expressie | Ja
Items | Invoerarray waarop het filter moet worden toegepast. | Expressie | Ja

## <a name="example"></a>Voorbeeld

In dit voorbeeld heeft de pijplijn twee activiteiten: **Filter** en **ForEach**. De filteractiviteit is geconfigureerd om de invoerarray te filteren voor items met een waarde groter dan 3. De Activiteit ForEach wordt vervolgens over de gefilterde waarden heen getint en stelt de variabele **test in** op de huidige waarde.

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
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Web Activity](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)
