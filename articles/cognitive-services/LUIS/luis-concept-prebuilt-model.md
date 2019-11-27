---
title: Preconstrueerde modellen-LUIS
titleSuffix: Azure Cognitive Services
description: Vooraf gemaakte modellen bieden domeinen, intents uitingen en entiteiten. U kunt uw app te starten met een vooraf gedefinieerde domein of later een relevante domein toevoegen aan uw app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280844"
---
# <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Vooraf gemaakte modellen bieden domeinen, intents uitingen en entiteiten. U kunt uw app starten met een vooraf gebouwd model of later een relevant model aan uw app toevoegen. 

## <a name="types-of-prebuilt-models"></a>Typen van vooraf gemaakte modellen

LUIS biedt drie soorten vooraf ontwikkelde modellen. Elk model kan worden toegevoegd aan uw app op elk gewenst moment. 

|Modeltype|Bevat|
|--|--|
|[Domeinen](luis-reference-prebuilt-domains.md)|Intents, uitingen, entiteiten|
|Intents|Intents, uitingen|
|[Rijg](luis-reference-prebuilt-entities.md)|Alleen entiteiten| 

## <a name="prebuilt-domains"></a>Vooraf gemaakte domeinen

Language Understanding (LUIS) biedt kant-en- *klare domeinen*. Dit zijn vooraf getrainde modellen van [intenties](luis-how-to-add-intents.md) en [entiteiten](luis-concept-entity-types.md) die samen werken voor domeinen of algemene categorieën van client toepassingen. 

De vooraf gemaakte domeinen zijn getraind en gereed is om toe te voegen aan uw LUIS-app. De intenties en entiteiten van een vooraf gebouwd domein kunnen volledig worden aangepast nadat u ze aan uw app hebt toegevoegd. 

> [!TIP]
> De intenties en entiteiten in een vooraf gedefinieerde domein werken het beste samen. Is het beter om intenties en entiteiten op basis van hetzelfde domein indien mogelijk te combineren.
> De hulpprogramma's vooraf gemaakte domein heeft intents die u voor gebruik in elk domein aanpassen kunt. U kunt bijvoorbeeld `Utilities.Repeat` toevoegen aan uw app en traint dat alle acties die gebruikers mogelijk willen herhalen in uw toepassing worden herkend. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Het gedrag van de intentie van een vooraf gedefinieerde domein wijzigen

Mogelijk vindt u een vooraf gedefinieerde domein bevat een intent die vergelijkbaar is met een doel dat u in uw LUIS-app wilt hebben, maar u wilt zich anders gedragen. **Het voor** bereide domein biedt bijvoorbeeld een `MakeReservation` intentie voor het maken van een restaurant, maar u wilt dat uw app die intentie gebruikt om hotel reserveringen te maken. In dat geval kunt u het gedrag van die intentie wijzigen door bijvoorbeeld uitingen toe te voegen aan het doel om hotel reserveringen te maken en de app vervolgens opnieuw te trainen. 

U vindt een volledige lijst met vooraf gemaakte domeinen in de [Naslag Gids met vooraf gedefinieerde domeinen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Vooraf gedefinieerde intents

LUIS biedt vooraf ontwikkelde intenten en hun uitingen voor elk van de vooraf gemaakte domeinen. Intents kunnen worden toegevoegd zonder het hele domein toe te voegen. Het toevoegen van een intentie is het proces van het toevoegen van een intentie en de uitingen van uw app. Zowel de naam van de intentie en de lijst met utterance kan worden aangepast.  

## <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten

LUIS bevat een set met vooraf gemaakte entiteiten voor het herkennen van algemene typen gegevens, zoals datums, tijden, getallen, metingen en valuta. Ondersteuning voor vooraf gedefinieerde entiteit is afhankelijk van de cultuur van uw LUIS-app. Zie de [vooraf samengestelde entiteits referentie](./luis-reference-prebuilt-entities.md)voor een volledige lijst van de vooraf gemaakte entiteiten die door Luis worden ondersteund, inclusief ondersteuning door de cultuur.

Wanneer een vooraf gedefinieerde entiteit is opgenomen in uw toepassing, wordt de voorspellingen zijn opgenomen in uw gepubliceerde toepassing. Het gedrag van vooraf gemaakte entiteiten is vooropgeleid en **kan niet** worden gewijzigd. 

> [!NOTE]
> **Builtin. datetime** is afgeschaft. Het wordt vervangen door [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), waarmee u de datum-en tijds bereik herkenning kunt herkennen, evenals een verbeterde herkenning van onduidelijke datum en tijd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toevoegen van vooraf gemaakte entiteiten](luis-prebuilt-entities.md) aan uw app.
