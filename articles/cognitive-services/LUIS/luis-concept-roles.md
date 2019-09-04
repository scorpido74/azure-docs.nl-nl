---
title: Rollen voor entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Rollen zijn met de naam, contextuele subtypen van een entiteit die alleen in de patronen gebruikt. Bijvoorbeeld, in de utterance `buy a ticket from New York to London`, New York zowel Londen zijn steden, maar elk een andere betekenis heeft in de zin. New York is de plaats van de oorsprong en Londen is de plaats van bestemming.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257959"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Entiteits rollen voor contextuele subtypen

Met rollen kunnen entiteiten benoemde subtypen hebben. Een rol kan worden gebruikt met een vooraf samengesteld of aangepast entiteits type en wordt gebruikt in beide voor beelden van uitingen en patronen. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Voor beeld van een door de machine geleerde entiteit van rollen

In het utterance ' Koop een ticket van **New York** voor **Londen**, zijn zowel New York als Londen steden, maar elk heeft een andere betekenis in de zin. New York is de plaats van de oorsprong en Londen is de plaats van bestemming. 

```
buy a ticket from New York to London
```

Functies bieden een naam op die verschillen:

|Entiteitstype|Naam van de entiteit|Role|Doel|
|--|--|--|--|
|Simpel|Location|Oorsprong|waar het vlak van verlaat|
|Simpel|Location|destination|waar het vlak van terechtkomt|

## <a name="non-machine-learned-entity-example-of-roles"></a>Voor beeld van rollen die niet voor machines zijn geleerd

In de utterance ' de vergadering plannen van 8 tot 9 ' geven beide cijfers een tijd aan, maar elke keer heeft een andere betekenis in de utterance. Rollen geven de naam voor de verschillen. 

```
Schedule the meeting from 8 to 9
```

|Entiteitstype|Rolnaam|Value|
|--|--|--|
|Vooraf gedefinieerde datetimeV2|StartTime|8|
|Vooraf gedefinieerde datetimeV2|Tijd|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Zijn er meerdere entiteiten in een utterance hetzelfde als rollen? 

Er kunnen meerdere entiteiten in een utterance bestaan en kunnen worden geëxtraheerd zonder gebruik van rollen. Als in de context van de zin wordt aangegeven welke versie van de entiteit een waarde heeft, moet een rol worden gebruikt. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Gebruik geen rollen voor dubbele waarden zonder betekenis

Als de utterance een lijst met locaties bevat, `I want to travel to Seattle, Cairo, and London.`is dit een lijst waarin elk item geen extra betekenis heeft. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Rollen gebruiken als dubbele waarden duiden op betekenis

Als de utterance een lijst met locaties bevat met de betekenis `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, moet deze betekenis van Origin, Layover en Destination worden vastgelegd met rollen.

### <a name="roles-can-indicate-order"></a>Rollen kunnen de volg orde aangeven

Als de utterance is gewijzigd om de volg orde aan te geven die `I want to first start with Seattle, second London, then third Cairo`u wilde ophalen, kunt u op een aantal manieren extra heren ophalen. U kunt de tokens die de rol aangeven, `first start with`, `second`,, `third`labelen. U kunt ook het vooraf gedefinieerde entiteits **rangtelwoord** en de **GeographyV2** vooraf gebouwde entiteit in een samengestelde entiteit gebruiken om het idee van order en Place vast te leggen. 

## <a name="how-are-roles-used-in-example-utterances"></a>Hoe worden rollen gebruikt in voor beeld-uitingen?

Wanneer een entiteit een rol heeft en de entiteit is gemarkeerd in een voor beeld utterance, kunt u kiezen alleen de entiteit selecteren of de entiteit en rol selecteren. 

In het volgende voor beeld uitingen worden de entiteiten en rollen gebruikt:

|Token weergave|Entiteits weergave|
|--|--|
|Ik ben geïnteresseerd in meer informatie over **Seattle**|Ik wil graag meer weten over {location}|
|Een ticket kopen van Seattle naar New York|Een ticket van {Location: Origin} kopen op {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Hoe worden de rollen in patronen gebruikt?
Rollen worden gebruikt in een patroon van sjabloon utterance, binnen de utterance: 

|Patroon met behulp van entiteit|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxis van de rol van patronen
De entiteit en de rol worden tussen haakjes, `{}`. De entiteit en de rol worden gescheiden door een dubbele punt. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entiteits rollen versus samenwerkings rollen

Entiteits rollen zijn van toepassing op het gegevens model van de LUIS-app. Rollen voor [samen werking of Inzender](luis-concept-keys.md#contributions-from-other-authors) zijn van toepassing op niveaus van toegang tot ontwerpen. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Volgende stappen

* Een [praktische zelf studie](tutorial-entity-roles.md) gebruiken met entiteits rollen met niet door machines geleerde entiteiten
* Informatie over het toevoegen [rollen](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
