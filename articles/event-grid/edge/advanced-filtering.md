---
title: Geavanceerde filtering - Azure Event Grid IoT Edge | Microsoft Documenten
description: Geavanceerde filtering in gebeurtenisraster op IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992559"
---
# <a name="advanced-filtering"></a>Geavanceerd filteren
Met Event Grid kunnen filters worden opgegeven op elke eigenschap in de json-payload. Deze filters zijn gemodelleerd `AND` als set van voorwaarden, `OR` met elke buitenste voorwaarde met optionele innerlijke omstandigheden. Voor `AND` elke voorwaarde geeft u de volgende waarden op:

* `OperatorType`- Het type vergelijking.
* `Key`- Het jsonpad naar de eigenschap waarop het filter moet worden toegepast.
* `Value`- De referentiewaarde waartegen het filter `Values` wordt uitgevoerd (of) - De set referentiewaarden waartegen het filter wordt uitgevoerd.

## <a name="json-syntax"></a>SYNTAXIS JSON

De SYNTAXIS VAN JSON voor een geavanceerd filter is als volgt:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filteren op matrixwaarden

Event Grid ondersteunt vandaag de dag geen filtering op een array met waarden. Als een binnenkomende gebeurtenis een matrixwaarde heeft voor de sleutel van het geavanceerde filter, mislukt de overeenkomende bewerking. De inkomende gebeurtenis komt uiteindelijk niet overeen met het evenementabonnement.

## <a name="and-or-not-semantics"></a>EN-OF-NIET semantiek

Merk op dat in het json-voorbeeld dat eerder is gegeven, `AdvancedFilters` een array is. Zie elk `AdvancedFilter` array-element `AND` als een voorwaarde.

Voor de operatoren die meerdere `NumberIn` `NumberNotIn`waarden `StringIn`ondersteunen (zoals , , `OR` enz.), wordt elke waarde als voorwaarde behandeld. Dus, `StringBeginsWith("a", "b", "c")` een zal overeenkomen met een `a` `b` string `c`waarde die begint met een of .

> [!CAUTION]
> De NIET-operatoren - `NumberNotIn` en `StringNotIn` gedragen zich als `Values` EN voorwaarden op elke waarde die in het veld wordt gegeven.
>
> Als u dit niet doet, wordt het filter een Accept-All-filter en wordt het doel van filteren verloren.

## <a name="floating-point-rounding-behavior"></a>Afrondingsgedrag zwevend punt

Gebeurtenisraster gebruikt `decimal` het .NET-type om alle numerieke waarden te verwerken. De getalwaarden die zijn opgegeven in het gebeurtenisabonnement JSON, zijn niet onderhevig aan zwevend puntafrondingsgedrag.

## <a name="case-sensitivity-of-string-filters"></a>Hoofdlettergevoeligheid van tekenreeksfilters

Alle snaarvergelijkingen zijn case-ongevoelig. Er is geen manier om dit gedrag vandaag te veranderen.

## <a name="allowed-advanced-filter-keys"></a>Toegestane geavanceerde filtertoetsen

De `Key` eigenschap kan een bekende eigenschap op het hoogste niveau zijn, of een jsonpad zijn met meerdere punten, waarbij elke stip betekent dat u in een genest json-object stapt.

Event Grid heeft geen speciale betekenis `$` voor het teken in de sleutel, in tegenstelling tot de JSONPath-specificatie.

### <a name="event-grid-schema"></a>Gebeurtenisrasterschema

Voor gebeurtenissen in het schema gebeurtenisraster:

* Id
* Onderwerp
* Onderwerp
* EventType (EventType)
* Gegevensversie
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Aangepast gebeurtenisschema

Er is geen beperking `Key` op het aangepaste gebeurtenisschema, omdat Gebeurtenisraster geen envelopschema afdwingt op de payload.

## <a name="numeric-single-value-filter-examples"></a>Voorbeelden van filtermet numerieke waarde

* AantalGroter dan
* NumberGreaterThanOrEquals NumberGreaterThanOrEquals NumberGreaterThanOrEquals NumberGreater
* Numberlessthan
* Numberlessthanorequals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Voorbeelden van filtervoorbeelden voor numerieke bereikwaarden

* NumberIn
* NummerNotin

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Voorbeelden van filtervoorbeelden voor tekenreeksbereik-waarde

* Tekenreeksbevat
* StringBeginsWith
* StringEndsWith
* Stringin
* StringNotin

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Booleaanse filtervoorbeelden met één waarde

* BoolEquals BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
