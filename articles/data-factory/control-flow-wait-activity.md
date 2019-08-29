---
title: Wachtende activiteit in Azure Data Factory | Microsoft Docs
description: De wait-activiteit pauzeert de uitvoering van de pijp lijn voor de opgegeven periode.
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
ms.openlocfilehash: 48a722979e61209a855dd1fec22fcdcc756ae1ce
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142425"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Wacht activiteit uitvoeren in Azure Data Factory
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

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Description | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | De naam van `Wait` de activiteit. | Tekenreeks | Ja
Type | Moet worden ingesteld op **wait**. | Tekenreeks | Ja
waitTimeInSeconds | Het aantal seconden dat de pijp lijn wacht voordat de verwerking doorgaat. | Integer | Ja

## <a name="example"></a>Voorbeeld

> [!NOTE]
> Deze sectie bevat JSON-definities en voor beelden van Power shell-opdrachten voor het uitvoeren van de pijp lijn. Zie [zelf studie: een Data Factory maken](quickstart-create-data-factory-powershell.md)met behulp van Azure PowerShell voor een overzicht met stapsgewijze instructies voor het maken van een Data Factory pijp lijn met behulp van Azure PowerShell-en JSON-definities.

### <a name="pipeline-with-wait-activity"></a>Pijp lijn met wachtende activiteit
In dit voor beeld heeft de pijp lijn twee activiteiten: **Tot** en een **ogen blik geduld**. De wacht activiteit is zo geconfigureerd dat deze gedurende één seconde wordt gewacht. De pijp lijn voert de Web-activiteit in een lus uit met één seconde wacht tijd tussen elke uitvoering. 

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
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
