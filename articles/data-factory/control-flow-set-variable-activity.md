---
title: Variabele activiteit instellen in Azure Data Factory
description: Meer informatie over het gebruik van de activiteit Variabele instellen om de waarde in te stellen van een bestaande variabele die is gedefinieerd in een datafabriekpijplijn
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417977"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Variabele activiteit instellen in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de variabele activiteit instellen om de waarde in te stellen van een bestaande variabele van het type String, Bool of Array die is gedefinieerd in een pijplijn gegevensfabriek.

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
name | Naam van de activiteit in de pijplijn | ja
description | Tekst waarin wordt beschreven wat de activiteit doet | nee
type | Moet zijn ingesteld op **SetVariable** | ja
waarde | Tekenreeksletterlijke of expressieobjectwaarde waaraan de variabele wordt toegewezen | ja
variableName | Naam van de variabele die door deze activiteit wordt ingesteld | ja

## <a name="incrementing-a-variable"></a>Een variabele verhogen

Een veelvoorkomend scenario met variabelen in Azure Data Factory gebruikt een variabele als iterator binnen een activiteit tot of voor elke activiteit. In een ingestelde variabele activiteit u `value` niet verwijzen naar de variabele die in het veld wordt ingesteld. Als u deze beperking wilt oplossen, stelt u een tijdelijke variabele in en maakt u een variabele activiteit met een tweede set. De tweede ingestelde variabele activiteit stelt de waarde van de iterator in op de tijdelijke variabele. 

Hieronder is een voorbeeld van dit patroon:

![Incrementvariabele](media/control-flow-set-variable-activity/increment-variable.png "Incrementvariabele")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controlestroomactiviteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele toevoegen](control-flow-append-variable-activity.md)
