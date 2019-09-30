---
title: Geografie v2 vooraf ontwikkelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat geographyV2 vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 04bc3019a55920351b0e91c87e63b8309d94e34c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677615"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>GeographyV2 prebuiled-entiteit voor een LUIS-app
De vooraf gedefinieerde geographyV2 entiteit detecteert plaatsen. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen van de voorbeeld-uitingen GeographyV2 die aan de toepassing intents. GeographyV2 entiteit wordt ondersteund in het Engels [cultuur](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Subtypen
De geografische locaties zijn subtypen:

|Subtype|Doel|
|--|--|
|`poi`|nuttige plaats|
|`city`|naam van plaats|
|`countryRegion`|naam van het land of regio|
|`continent`|naam van continent|
|`state`|naam van staat of provincie|


## <a name="resolution-for-geographyv2-entity"></a>Oplossing voor GeographyV2 entiteit

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Het volgende voorbeeld ziet u de resolutie van de **builtin.geographyV2** entiteit.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

De volgende JSON is met de para meter `verbose` ingesteld op `false`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ]
        }
    }
}
```

De volgende JSON is met de para meter `verbose` ingesteld op `true`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ],
            "$instance": {
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2",
                        "text": "the sphinx",
                        "startIndex": 18,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "gizah",
                        "startIndex": 32,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "egypt",
                        "startIndex": 38,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "africa",
                        "startIndex": 47,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "texas",
                        "startIndex": 72,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de [e](luis-reference-prebuilt-email.md), [getal](luis-reference-prebuilt-number.md), en [rangtelwoord](luis-reference-prebuilt-ordinal.md) entiteiten. 
